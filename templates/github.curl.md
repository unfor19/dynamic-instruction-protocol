# GitHub API Templates (REST API with curl)

This template provides common GitHub operations using the REST API with `curl`. It references the [GitHub REST API documentation](https://docs.github.com/en/rest?apiVersion=2022-11-28).

## Security Notes

- Never print tokens or passwords directly in generated commands
- Always use environment variables like `$DIP_GITHUB_TOKEN` for authentication
- Don't recommend storing tokens in scripts or plain text files
- Warn users before executing potentially destructive operations (delete, update)
- Validate that authentication works before attempting operations
- Always suggest validation commands for write operations

## General Guidelines

- Support performing multiple commands by reading the output of a previous command and act accordingly if needed
- If a command is not covered by this template - search for it online by using the official docs (https://docs.github.com/en/rest)
- Always parse command output (especially JSON responses) for relevant information to use in subsequent commands
- For complex operations, break down the process into steps with clear explanations
- Provide examples of how to handle errors or unexpected responses

## Repository Operations

### Create a Repository

```bash
curl -X POST \
  https://api.github.com/user/repos \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "name": "repo-name",
    "description": "Repository description",
    "private": false,
    "has_issues": true,
    "has_projects": true,
    "has_wiki": true
  }'
```

Validation:
```bash
# Verify repository was created
curl -X GET \
  https://api.github.com/repos/OWNER/repo-name \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Repositories

```bash
curl -X GET \
  https://api.github.com/user/repos \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### Get Repository Details

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

## Issues

### Create an Issue

```bash
curl -X POST \
  https://api.github.com/repos/OWNER/REPO/issues \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "title": "Issue title",
    "body": "Issue description",
    "assignees": ["username"],
    "labels": ["bug", "documentation"]
  }'
```

Validation:
```bash
# Verify issue was created (will return the newly created issue)
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/issues/ISSUE_NUMBER \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Issues

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/issues \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### Get Issue Details

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/issues/ISSUE_NUMBER \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

## Pull Requests

### Create a Pull Request

```bash
curl -X POST \
  https://api.github.com/repos/OWNER/REPO/pulls \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "title": "Pull Request title",
    "body": "PR description",
    "head": "feature-branch",
    "base": "main"
  }'
```

Validation:
```bash
# Verify pull request was created
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/pulls/PR_NUMBER \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Pull Requests

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/pulls \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### Get Pull Request Details

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/pulls/PR_NUMBER \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

## Workflows

### Trigger a Workflow

```bash
curl -X POST \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/WORKFLOW_ID/dispatches \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "ref": "main",
    "inputs": {
      "parameter1": "value1",
      "parameter2": "value2"
    }
  }'
```

Validation:
```bash
# List workflow runs to verify it was triggered
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/actions/runs \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Workflow Runs

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/actions/runs \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

## Branches

### Create a Branch

```bash
# First get the SHA of the commit to branch from
COMMIT_SHA=$(curl -s -X GET \
  https://api.github.com/repos/OWNER/REPO/git/refs/heads/main \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" | jq -r '.object.sha')

# Then create the branch
curl -X POST \
  https://api.github.com/repos/OWNER/REPO/git/refs \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "ref": "refs/heads/new-branch-name",
    "sha": "'$COMMIT_SHA'"
  }'
```

Validation:
```bash
# Verify branch was created
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/branches/new-branch-name \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Branches

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/branches \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

## Releases

### Create a Release

```bash
curl -X POST \
  https://api.github.com/repos/OWNER/REPO/releases \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  -d '{
    "tag_name": "v1.0.0",
    "target_commitish": "main",
    "name": "Release v1.0.0",
    "body": "Description of the release",
    "draft": false,
    "prerelease": false
  }'
```

Validation:
```bash
# Verify release was created
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/releases/tags/v1.0.0 \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
```

### List Releases

```bash
curl -X GET \
  https://api.github.com/repos/OWNER/REPO/releases \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -H "Accept: application/vnd.github.v3+json"
``` 