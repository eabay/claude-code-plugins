---
description: Update dependencies
argument-hint: Extra context
allowed-tools:
  - Read
  - Grep
  - Glob
  - WebSearch
  - WebFetch
  - Bash(npm outdated:*), Bash(npm install:*), Bash(npm update:*)
  - Bash(npm test:*), Bash(npm run:*), Bash(npm audit:*)
  - Bash(npx:*)
  - Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git show:*)
---

- Find the outdated packages. Check difference between current and latest versions, search for release notes, announcements, blog posts, community discussions, and make sure there is no breaking changes.
- Update packages one by one, not all at once unless specified explicitly.
- Run tests to ensure everything works as expected. Do a syntax check to ensure the code is valid. Run linters to ensure the code is clean. Run security checks to ensure the code is secure.
- Refresh the outdated packages list after each update.
- For each package, do a checkpoint git commit just with title "Update package PACKAGE_NAME", before moving to the next package. If a package causes changes to the codebase, add a description explaining changes to the codebase. Do not stage any files other than the ones you have modified in each step.
- Once all packages are updated, do a final overall update by running the package manager's update command for the rest of the packages. Run similar checks to ensure everything works as expected.
- After all updates are complete, create a final commit with title "Update dependencies" by squashing all previous commits other than the ones with modifications in the codebase.
- Revert changes if something goes wrong, with a soft reset to the previous commit. Do not forget to run the package install command after reverting. Make sure everything is working as expected as before.

$ARGUMENTS
