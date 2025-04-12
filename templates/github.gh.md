# GitHub CLI Templates

This template provides common GitHub operations using GitHub CLI (`gh`). It references the [GitHub CLI Manual](https://cli.github.com/manual/gh).

## Security Notes

- Never print tokens or passwords directly in generated commands
- GitHub CLI handles authentication internally, but be mindful of login state
- Don't recommend storing sensitive data in scripts or plain text files
- Warn users before executing potentially destructive operations (delete, update)
- Validate that authentication works before attempting operations
- Always suggest validation commands for write operations

## General Guidelines

- Support performing multiple commands by reading the output of a previous command and act accordingly if needed
- If a command is not covered by this template - search for it online by using the official docs (https://cli.github.com/manual/gh)
- Chain commands using output parsing where appropriate (e.g., using `--json` flag with `jq` or other processors)
- Suggest interactive mode options where available, but also provide non-interactive alternatives
- For complex operations, break down the process into steps with clear explanations

## Repository Operations

### Create a Repository

```bash
# Create a public repository
gh repo create repo-name --public --description "Repository description"

# Create a private repository
gh repo create repo-name --private --description "Repository description"

# Create a repository with a template
gh repo create repo-name --template owner/template-repo
```

Validation:
```bash
# Verify repository was created
gh repo view repo-name
```

### Clone a Repository

```bash
gh repo clone owner/repo-name
```

### List Repositories

```bash
# List your repositories
gh repo list

# List another user's repositories
gh repo list username

# List repositories with specific filters
gh repo list --limit 10 --visibility public
```

### View Repository

```bash
# Open repository in browser
gh repo view owner/repo-name --web

# View repository details in terminal
gh repo view owner/repo-name
```

## Issues

### Create an Issue

```bash
# Create an issue interactively
gh issue create

# Create an issue with parameters
gh issue create --title "Issue title" --body "Issue description" --assignee @me --label "bug,documentation"

# Create an issue from a file
gh issue create --title "Issue title" --body-file issue-body.md
```

Validation:
```bash
# Verify issue was created by viewing it
gh issue view $(gh issue list --limit 1 --json number --jq '.[0].number')
```

### List Issues

```bash
# List open issues
gh issue list

# List issues with filters
gh issue list --assignee @me --label "bug" --state all

# List issues for specific repository
gh issue list --repo owner/repo-name
```

### View an Issue

```bash
# View issue in browser
gh issue view 123 --web

# View issue in terminal
gh issue view 123
```

## Pull Requests

### Create a Pull Request

```bash
# Create a pull request interactively from current branch
gh pr create

# Create a pull request with parameters
gh pr create --title "Pull Request title" --body "PR description" --base main

# Create a draft pull request
gh pr create --draft
```

Validation:
```bash
# Verify pull request was created
gh pr view
```

### List Pull Requests

```bash
# List open pull requests
gh pr list

# List pull requests with filters
gh pr list --assignee @me --label "bug"

# List pull requests for specific repository
gh pr list --repo owner/repo-name
```

### Check out a Pull Request

```bash
# Check out a pull request
gh pr checkout 123
```

### Review a Pull Request

```bash
# Approve a pull request
gh pr review 123 --approve

# Request changes on a pull request
gh pr review 123 --request-changes --body "Please fix these issues"
```

### Merge a Pull Request

```bash
# Merge a pull request
gh pr merge 123

# Merge with specific strategy
gh pr merge 123 --squash
```

Validation:
```bash
# Verify pull request was merged
gh pr view 123
```

## Workflows

### Trigger a Workflow

```bash
# List available workflows
gh workflow list

# Run a workflow
gh workflow run workflow-name.yml --ref main

# Run with inputs
gh workflow run workflow-name.yml --ref main -f parameter1=value1 -f parameter2=value2
```

Validation:
```bash
# Verify workflow was triggered
gh run list --workflow=workflow-name.yml --limit 1
```

### List Workflow Runs

```bash
# List recent workflow runs
gh run list

# List runs for a specific workflow
gh run list --workflow=workflow-name.yml
```

### View Workflow Run

```bash
# View workflow run in browser
gh run view 123456789 --web

# View workflow run in terminal
gh run view 123456789
```

## Branches

### Create and Push a New Branch

```bash
# Create a new branch and switch to it
git checkout -b new-branch-name

# Push the branch to GitHub
git push -u origin new-branch-name
```

Validation:
```bash
# Verify branch was created and pushed
gh repo view --branch new-branch-name
```

### List Branches

```bash
# List branches
gh repo view --branches
```

## Releases

### Create a Release

```bash
# Create a release interactively
gh release create v1.0.0

# Create a release with notes
gh release create v1.0.0 --title "Release v1.0.0" --notes "Release notes here"

# Create a release with files
gh release create v1.0.0 ./dist/app.zip ./dist/app.tar.gz
```

Validation:
```bash
# Verify release was created
gh release view v1.0.0
```

### List Releases

```bash
# List releases
gh release list
``` 