---
name: task-management
description: Lightweight project task management using markdown files in `.tasks/`. This skill should be used when the user asks to create, update, complete, or list project tasks, including phrases like "create task", "add task", "new task", "list tasks", "show tasks", "task status", "complete task", "mark task done", "update task". Also applies when referencing `.tasks/` files or asking about remaining project work.
---

# Project Task Management

Project tasks live in `.tasks/` as individual markdown files. Each file is one task — the filename is the title in kebab-case (e.g., "Fix login bug" becomes `fix-login-bug.md`).

## Task File Format

```markdown
---
status: todo
priority: normal
due:
---

## Description

Task description.

## Subtasks

- [ ] Subtask 1
- [ ] Subtask 2

## Progress

### YYYY-MM-DD

- Task created

### YYYY-MM-DD

- Summarize work done in bullet points
```

## Frontmatter Fields

| Field | Values | Default |
|-------|--------|---------|
| status | `todo`, `in-progress`, `done` | `todo` |
| priority | `high`, `normal`, `low` | `normal` |
| due | `YYYY-MM-DD` or empty | empty |

## Procedures

### Create Task

1. Collect missing information using AskUserQuestion tool:
   - **Title** (required): Task title — used as filename. Ask if not provided.
   - **Priority** (optional): `high`, `normal`, `low`. Default: `normal`. Ask only if not obvious from context.
   - **Due date** (optional): `YYYY-MM-DD`. Default: empty. Ask only if user mentions a deadline context.
   - **Description** (optional): Brief explanation. Can be inferred from context or asked.
   Combine all missing fields into a single AskUserQuestion call. Do not ask for information already provided by the user.
2. Create `.tasks/` directory if it does not exist
3. Create `.tasks/{title}.md` using kebab-case filename. If a file with the same name already exists, ask the user how to proceed.
4. Set frontmatter with collected values
5. Write the task description in Description section
6. Add known subtasks to Subtasks section, or remove the section if none
7. Add today's date header to Progress with "Task created"

### Update Progress

1. Find the matching task file in `.tasks/`. If no match, inform the user. If multiple matches, present candidates and ask which one.
2. Set `status: in-progress` if not already
3. Add a new entry under Progress with today's date, summarizing the work done
4. Check off completed subtasks as `- [x]`

When adding to Progress, always append at the bottom. If today's date header already exists, add under the existing header — do not create a duplicate.

### Complete Task

1. Find the matching task file in `.tasks/`. If no match, inform the user. If multiple matches, present candidates and ask which one.
2. Set `status: done`
3. Check off all subtasks as `- [x]`
4. Add a closing note under Progress summarizing what was accomplished (e.g., "Task completed. Implemented login form with validation and error handling.")

### List Tasks

1. If `.tasks/` does not exist or contains no files, report that no tasks exist
2. Read all `.md` files in `.tasks/`
3. Parse frontmatter from each file
4. Report as a markdown table with columns: Task, Status, Priority, Due
5. Group by status: in-progress first, then todo, then done

## Rules

- Keep frontmatter values strictly within the defined options above
- Progress section is chronological — oldest at top, newest at bottom
- Do not add empty or placeholder sections — only write known content
- Subtask checkboxes use `- [ ]` / `- [x]`
- Tasks are not deleted. Completed tasks remain as historical records.
- Whether `.tasks/` is version-controlled is a user decision — ask on first use if unclear
