# Atlassian CLI Templates

This template provides common Atlassian operations using Atlassian CLI (`acli`). It references the [Atlassian CLI documentation](https://developer.atlassian.com/cloud/acli/reference/commands/).

## Security Notes

- Never print tokens or passwords directly in generated commands
- Atlassian CLI handles authentication internally, but be mindful of login state
- Don't recommend storing sensitive data in scripts or plain text files
- Warn users before executing potentially destructive operations (delete, update)
- Validate that authentication works before attempting operations
- Always suggest validation commands for write operations

## General Guidelines

- Support performing multiple commands by reading the output of a previous command and act accordingly if needed
- If a command is not covered by this template - search for it online by using the official docs (https://developer.atlassian.com/cloud/acli/reference/commands/)
- Chain commands using output parsing where appropriate
- Suggest interactive mode options where available, but also provide non-interactive alternatives
- For complex operations, break down the process into steps with clear explanations

## Jira Operations

### Create a Work Item

```bash
# Create a work item interactively
acli jira workitem create

# Create a work item with parameters
acli jira workitem create --summary "Work item title" --project "PROJ" --type "Task"

# Create a work item with assignee and labels
acli jira workitem create --summary "Work item title" --project "PROJ" --type "Bug" --assignee "user@atlassian.com" --label "bug,urgent"

# Create a work item from a file
acli jira workitem create --from-file "workitem.txt" --project "PROJ" --type "Bug"
```

Validation:
```bash
# Verify work item was created
acli jira workitem view --id PROJ-123
```

### Search Work Items

```bash
# Search for work items
acli jira workitem search

# Search with JQL
acli jira workitem search --jql "project = PROJ AND status = 'In Progress'"
```

### View Work Item

```bash
# View work item details
acli jira workitem view --id PROJ-123

# View work item as JSON
acli jira workitem view --id PROJ-123 --json
```

### Edit Work Item

```bash
# Edit a work item
acli jira workitem edit --id PROJ-123 --summary "Updated title"

# Edit a work item with description
acli jira workitem edit --id PROJ-123 --description "New description"
```

### Assign Work Item

```bash
# Assign a work item
acli jira workitem assign --id PROJ-123 --assignee "user@atlassian.com"

# Self-assign a work item
acli jira workitem assign --id PROJ-123 --assignee "@me"
```

### Comment on Work Item

```bash
# Add a comment to a work item
acli jira workitem comment --id PROJ-123 --body "This is a comment"
```

### Transition Work Item

```bash
# Transition a work item to a new status
acli jira workitem transition --id PROJ-123 --transition "In Progress"
```

## Project Management

### View Project

```bash
# View project details
acli jira project view --key "PROJ"

# View project details as JSON
acli jira project view --key "PROJ" --json
```

### List Projects

```bash
# List all projects
acli jira project list

# List projects with specific parameters
acli jira project list --limit 10
```

### Create Project

```bash
# Create a new project
acli jira project create --key "PROJ" --name "Project Name" --template "scrum"
```

### Update Project

```bash
# Update project details
acli jira project update --key "PROJ" --name "New Project Name"
```

## Feedback

### Submit Feedback

```bash
# Submit feedback or report a problem
acli feedback submit
```

## Help

### Get Command Help

```bash
# Get general help
acli --help

# Get help for a specific command
acli jira workitem create --help
``` 