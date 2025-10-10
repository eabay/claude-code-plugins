# Claude Code Plugins Marketplace

This repository contains a curated collection of Claude Code plugins designed to enhance development workflows. The plugins extend Claude Code with custom commands, agents, and integrations.

## Repository Structure

```
claude-code-plugins/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace manifest
└── plugins/
    ├── dev-essentials/           # Essential development commands
    │   ├── .claude-plugin/
    │   │   └── plugin.json
    │   └── commands/
    │       ├── commit.md         # Smart commit message generation
    │       └── update-deps.md    # Methodical dependency updates
    └── sentry/                   # Sentry error tracking integration
        ├── .claude-plugin/
        │   └── plugin.json
        └── agents/
            └── issue-analyzer.md # Sentry issue investigation agent
```

## Available Plugins

### dev-essentials

Essential development workflow commands for common tasks.

**Commands:**
- `/commit [context]` - Generate well-crafted commit messages following Git best practices
- `/update-deps [context]` - Systematically update dependencies with safety checks

**Installation:**
```bash
/plugin marketplace add eabay/claude-code-plugins
/plugin install dev-essentials@eabay-tools
```

### sentry

Sentry integration providing automated error investigation and analysis.

**Features:**
- MCP server integration with Sentry
- Specialized issue-analyzer agent for investigating production errors
- Comprehensive root cause analysis with solution proposals

**Agents:**
- `issue-analyzer` - Automatically investigates Sentry errors, analyzes stack traces, and proposes solutions

**Installation:**
```bash
/plugin marketplace add eabay/claude-code-plugins
/plugin install sentry@eabay-tools
```

**Requirements:**
- Sentry MCP server configuration
- Environment variables for Sentry API access (configured via MCP)

## Development Guidelines

### Creating a New Plugin

1. **Create plugin directory structure:**

```bash
mkdir -p plugins/your-plugin-name/.claude-plugin
mkdir -p plugins/your-plugin-name/commands    # If adding commands
mkdir -p plugins/your-plugin-name/agents      # If adding agents
mkdir -p plugins/your-plugin-name/hooks       # If adding hooks
```

2. **Create plugin.json manifest:**

```json
{
  "name": "your-plugin-name",
  "version": "1.0.0",
  "description": "Brief description of your plugin",
  "author": {
    "name": "Your Name"
  },
  "keywords": ["keyword1", "keyword2"]
}
```

3. **Add plugin to marketplace.json:**

```json
{
  "plugins": [
    {
      "name": "your-plugin-name",
      "source": "./plugins/your-plugin-name"
    }
  ]
}
```

### Plugin Component Guidelines

#### Commands

Commands are markdown files in the `commands/` directory with optional YAML frontmatter.

**Structure:**
```markdown
---
description: Brief description shown in command listings
argument-hint: [param1] [param2]
allowed-tools:
  - Read
  - Edit
  - Bash(git status:*), Bash(git commit:*)
model: sonnet
---

Detailed instructions for Claude on how to execute this command.

Use $ARGUMENTS for all arguments, or $1, $2, $3 for individual parameters.
```

**Best practices:**
- Use kebab-case for command file names
- Provide clear, concise descriptions
- Restrict tool permissions to minimum necessary
- Include examples or reference material in the command body
- Use `argument-hint` to document expected parameters
- Choose appropriate model: `haiku` (fast), `sonnet` (default), `opus` (complex)

#### Agents

Agents are markdown files in the `agents/` directory with YAML frontmatter and a system prompt.

**Structure:**
```markdown
---
name: agent-name
description: When and why to invoke this agent. Include use case examples.
tools: Tool1, Tool2, Tool3
model: sonnet
color: cyan
---

You are an expert [role] with expertise in [domain].

Your mission is to [objective].

## Core Responsibilities
[Detailed responsibilities...]

## Operational Guidelines
[How to perform the work...]

## Output Format
[How to structure responses...]
```

**Best practices:**
- Focus on a single area of expertise
- Write detailed descriptions with examples for automatic delegation
- Limit tool access to what's needed for the specific role
- Use plan mode for analysis tasks (exit_plan_mode to implement)
- Choose colors to distinguish agents visually: `cyan`, `green`, `yellow`, etc.

#### Hooks

Hooks are event handlers configured in `hooks/hooks.json` that execute shell commands at lifecycle points.

**Example structure:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
          }
        ]
      }
    ]
  }
}
```

**Available events:**
- `PreToolUse` - Before tool execution (can block)
- `PostToolUse` - After successful tool execution
- `UserPromptSubmit` - When user submits a prompt
- `Stop` - When main agent finishes
- `SubagentStop` - When subagent finishes
- `SessionStart` - Session initialization
- `SessionEnd` - Session termination

**Security considerations:**
⚠️ Hooks execute shell commands automatically. Always:
- Validate and sanitize inputs
- Use absolute paths with `${CLAUDE_PLUGIN_ROOT}`
- Quote shell variables properly
- Test thoroughly before deploying

### Testing Plugins Locally

1. **Create a development installation:**

```bash
# If already installed, uninstall first
/plugin uninstall plugin-name@eabay-tools

# Make your changes to the plugin

# Reinstall to test
/plugin install plugin-name@eabay-tools
```

2. **Test individual components:**
   - Commands: Invoke with various arguments, verify tool permissions
   - Agents: Test with different scenarios and edge cases
   - Hooks: Verify they trigger correctly and handle errors gracefully

3. **Validation checklist:**
   - [ ] Plugin structure is correct (`.claude-plugin/` at plugin root)
   - [ ] `plugin.json` has required fields (name, version)
   - [ ] All commands have descriptions
   - [ ] Agents have clear descriptions with examples
   - [ ] Tool permissions are appropriately restricted
   - [ ] Documentation is complete
   - [ ] Examples work as expected

### Versioning

Follow semantic versioning (MAJOR.MINOR.PATCH):
- **MAJOR**: Breaking changes to plugin interface
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes and minor improvements

Update versions in both `plugin.json` and `marketplace.json` when making changes.

## Naming Conventions

- **Plugin names**: kebab-case (e.g., `dev-essentials`, `sentry`)
- **Command files**: kebab-case (e.g., `commit.md`, `update-deps.md`)
- **Agent files**: kebab-case (e.g., `issue-analyzer.md`)
- **Agent names** (in frontmatter): kebab-case (e.g., `issue-analyzer`)

## Tool Permissions

Available tools that can be restricted via `allowed-tools`:
- **File operations**: `Read`, `Edit`, `Write`, `MultiEdit`, `NotebookEdit`
- **Search**: `Glob`, `Grep`
- **Execution**: `Bash(pattern)` - restrict with patterns like `Bash(git status:*)`
- **Network**: `WebFetch(domain:example.com)`, `WebSearch`
- **Automation**: `Task`, `TodoWrite`
- **MCP**: Any MCP server tools (e.g., `mcp__sentry`)

**Pattern syntax:**
- `Bash(git status:*)` - Allow git status with any arguments
- `Bash(npm install)` - Allow only exact command
- `WebFetch(domain:docs.anthropic.com)` - Restrict to specific domain
- `Edit(/src/**/*.ts)` - Edit only TypeScript files in src/

## Publishing

### Making Changes

1. Update plugin version in `plugins/your-plugin/.claude-plugin/plugin.json`
2. Update marketplace version in `.claude-plugin/marketplace.json` if needed
3. Test changes locally
4. Commit with descriptive message
5. Push to GitHub

### Sharing the Marketplace

Users can add this marketplace with:

```bash
/plugin marketplace add eabay/claude-code-plugins
```

Then install individual plugins:

```bash
/plugin install dev-essentials@eabay-tools
/plugin install sentry@eabay-tools
```

## Environment Variables

Plugins can use special environment variables:

- `${CLAUDE_PLUGIN_ROOT}` - Absolute path to plugin directory
- `${VAR}` - Standard environment variable expansion
- `${VAR:-default}` - Environment variable with default value

Use in MCP server configurations, hook commands, and script paths.

## Best Practices

### Commands
- Keep commands focused on a single workflow
- Provide clear instructions with context
- Use `allowed-tools` to enforce least privilege
- Include reference materials inline when helpful
- Document expected arguments with `argument-hint`

### Agents
- Specialize in one domain or task type
- Write comprehensive system prompts
- Include examples in descriptions for better auto-delegation
- Use plan mode for analysis, exit plan mode for implementation
- Choose appropriate model based on task complexity

### Hooks
- Test extensively - hooks can cause unexpected behavior
- Use appropriate exit codes (0=success, 2=blocking error)
- Provide clear feedback via JSON output when needed
- Be performant - hooks should execute quickly
- Document behavior clearly

### Documentation
- Include a README.md in each plugin directory
- Document all commands, agents, and hooks
- Provide usage examples
- Keep CLAUDE.md updated with project conventions
- Document any required environment variables or setup

## Common Patterns

### Multi-step Workflow Commands

Create commands that guide Claude through complex processes:

```markdown
---
description: Deploy application with checks
---

1. Run full test suite and ensure all tests pass
2. Build the application for production
3. Run security audit
4. Create git tag with semantic version
5. Push to production environment
6. Verify deployment health checks
```

### Specialized Analysis Agents

Create agents for specific technical domains:

```markdown
---
name: security-auditor
description: Use when analyzing code for security vulnerabilities or reviewing security issues
tools: Read, Grep, WebSearch
---

You are a security expert specializing in application security...
```

### Quality Control Hooks

Automatically enforce standards:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint:fix"
          }
        ]
      }
    ]
  }
}
```

## Troubleshooting

### Plugin Not Loading

- Ensure `.claude-plugin/plugin.json` exists at plugin root
- Verify JSON syntax is valid
- Check that plugin is listed in marketplace.json
- Try reinstalling: uninstall then install again

### Command Not Appearing

- Verify command file has `.md` extension
- Check frontmatter description is present
- Ensure file is in `commands/` directory
- Reinstall the plugin

### Agent Not Delegating

- Improve description with specific use case examples
- Include clear trigger conditions
- Test explicit invocation first
- Check tool permissions are sufficient

### Hook Not Triggering

- Verify matcher pattern is correct
- Check hook command has execute permissions
- Test hook script independently
- Review hook output for error messages

## Resources

- **Claude Code Documentation**: https://docs.claude.com/en/docs/claude-code/plugins
- **Example Repositories**:
  - https://github.com/hesreallyhim/awesome-claude-code
  - https://github.com/wshobson/commands
  - https://github.com/wshobson/agents

## Contributing

When contributing plugins to this marketplace:

1. Follow the naming conventions and structure guidelines
2. Test thoroughly before submitting
3. Include comprehensive documentation
4. Use semantic versioning
5. Provide clear descriptions and examples
6. Keep tool permissions minimal
7. Consider security implications, especially for hooks

## License

MIT
