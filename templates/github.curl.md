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
- For operations involving large text inputs (e.g., issue descriptions, PR bodies, release notes), consider preparing the content in a local file and using the `-d @filename` option with `curl`.

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
curl -X POST \\
  https://api.github.com/repos/OWNER/REPO/issues \\
  -H "Authorization: token $DIP_GITHUB_TOKEN" \\
  -H "Accept: application/vnd.github.v3+json" \\
  -d '{
    "title": "Issue title",
    "body": "Issue description",
    "assignees": ["username"],
    "labels": ["bug", "documentation"]
  }'
```

#### Creating an Issue with a Body from a File

For longer or more complex issue descriptions, you can store the body in a Markdown file (e.g., `issue_body.md`) and the full JSON payload in a file (e.g., `.issue_payload.json`).

1.  Create `issue_body.md` with your issue description:
    ```markdown
    This is a detailed description of the issue.
    It can span multiple lines and include markdown formatting.

    - Point 1
    - Point 2
    ```

2.  Create `.issue_payload.json` (note the leading `.` to make it a hidden file):
    ```json
    {
      "title": "Issue title from payload",
      "body": "$(cat issue_body.md)",
      "assignees": ["username"],
      "labels": ["bug", "enhancement"]
    }
    ```
    *Alternatively, to avoid issues with special characters in the markdown file when read by `cat`, you can prepare the final JSON with the body content already embedded. First, create a temporary file with the body, then construct the JSON.*

    Example of preparing the JSON directly (more robust):
    Create a file, e.g., `.issue_payload.json`:
    ```json
    {
      "title": "Issue title from payload",
      "body": "This is a detailed description of the issue.\\nIt can span multiple lines and include markdown formatting.\\n\\n- Point 1\\n- Point 2",
      "assignees": ["username"],
      "labels": ["bug", "enhancement"]
    }
    ```
    *(Ensure newlines `\\n` and other special characters are properly escaped in the JSON string if embedding directly).*


3.  Then, use `curl` with the `-d @.issue_payload.json` option:
    ```bash
    # Ensure issue_body.md exists if using the cat method in the payload
    # Create .issue_payload.json as described above.
    # For the 'cat' method in the payload:
    # BODY_CONTENT=$(cat issue_body.md)
    # jq -n --arg title "Issue title from payload" --arg body "$BODY_CONTENT" --argjson assignees '["username"]' --argjson labels '["bug", "enhancement"]' \
    #   '{title: $title, body: $body, assignees: $assignees, labels: $labels}' > .issue_payload.json

    curl -X POST \\
      https://api.github.com/repos/OWNER/REPO/issues \\
      -H "Authorization: token $DIP_GITHUB_TOKEN" \\
      -H "Accept: application/vnd.github.v3+json" \\
      -d @.issue_payload.json # Use @ to specify the file path
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
curl -X POST \\
  https://api.github.com/repos/OWNER/REPO/pulls \\
  -H "Authorization: token $DIP_GITHUB_TOKEN" \\
  -H "Accept: application/vnd.github.v3+json" \\
  -d '{
    "title": "Pull Request title",
    "body": "PR description",
    "head": "feature-branch",
    "base": "main"
  }'
```

#### Creating a Pull Request with a Body from a File

For longer or more complex pull request descriptions, it's recommended to prepare the body content in a local Markdown file and the full JSON payload in another file.

1.  Create a Markdown file for your PR body, e.g., `pr_description.md`:
    ```markdown
    ### PR Summary

    This PR introduces feature X and fixes bug Y.

    #### Changes:
    - Added new module `foo.py`
    - Updated `bar.py` to include new logic
    - Tests for feature X

    #### How to Test:
    1. Run `make test`
    2. Manually verify endpoint Z
    ```

2.  Create a JSON payload file, e.g., `.pr_payload.json` (note the leading `.` to make it a hidden file):
    ```json
    {
      "title": "My Awesome Pull Request",
      "head": "feature-branch",
      "base": "main",
      "body": "" 
    }
    ```
    *The `body` field will be populated from `pr_description.md` in the script below or you can manually paste the content (ensure proper JSON escaping for newlines etc.).*

3.  Use `curl` with a script or by manually creating the payload file with the body content:

    **Option A: Using `jq` and `cat` to build the payload (handles Markdown better):**
    ```bash
    # Ensure pr_description.md exists
    PR_BODY_CONTENT=$(cat pr_description.md)
    jq -n --arg title "My Awesome Pull Request" \
          --arg head "feature-branch" \
          --arg base "main" \
          --arg body "$PR_BODY_CONTENT" \
      '{title: $title, head: $head, base: $base, body: $body}' > .pr_payload.json

    curl -X POST \\
      https://api.github.com/repos/OWNER/REPO/pulls \\
      -H "Authorization: token $DIP_GITHUB_TOKEN" \\
      -H "Accept: application/vnd.github.v3+json" \\
      -d @.pr_payload.json
    ```

    **Option B: Manually preparing `.pr_payload.json` with escaped body content:**
    If you manually create `.pr_payload.json`, ensure the body content from `pr_description.md` is correctly escaped for JSON (e.g., newlines as `\\n`).
    ```json
    // .pr_payload.json
    {
      "title": "My Awesome Pull Request",
      "head": "feature-branch",
      "base": "main",
      "body": "### PR Summary\\n\\nThis PR introduces feature X and fixes bug Y.\\n\\n#### Changes:\\n- Added new module `foo.py`\\n- Updated `bar.py` to include new logic\\n- Tests for feature X\\n\\n#### How to Test:\\n1. Run `make test`\\n2. Manually verify endpoint Z"
    }
    ```
    Then run:
    ```bash
    curl -X POST \\
      https://api.github.com/repos/OWNER/REPO/pulls \\
      -H "Authorization: token $DIP_GITHUB_TOKEN" \\
      -H "Accept: application/vnd.github.v3+json" \\
      -d @.pr_payload.json
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
curl -X POST \\
  https://api.github.com/repos/OWNER/REPO/releases \\
  -H "Authorization: token $DIP_GITHUB_TOKEN" \\
  -H "Accept: application/vnd.github.v3+json" \\
  -d '{
    "tag_name": "v1.0.0",
    "target_commitish": "main",
    "name": "Release v1.0.0",
    "body": "Description of the release",
    "draft": false,
    "prerelease": false
  }'
```

#### Creating a Release with a Body from a File

For detailed release notes, prepare the content in a Markdown file and the full JSON payload in another file.

1.  Create a Markdown file for your release notes, e.g., `release_notes.md`:
    ```markdown
    ## Release v1.0.0

    ### New Features
    - Feature A
    - Feature B

    ### Bug Fixes
    - Fixed issue #123
    - Addressed CVE-XXXX-XXXX
    ```

2.  Create a JSON payload file, e.g., `.release_payload.json`:
    ```json
    {
      "tag_name": "v1.0.0",
      "target_commitish": "main",
      "name": "Release v1.0.0",
      "body": "", 
      "draft": false,
      "prerelease": false
    }
    ```
    *The `body` will be populated from `release_notes.md`.*

3.  Use `curl` with `jq` and `cat`:
    ```bash
    # Ensure release_notes.md exists
    RELEASE_BODY_CONTENT=$(cat release_notes.md)
    jq -n --arg tag_name "v1.0.0" \
          --arg target_commitish "main" \
          --arg name "Release v1.0.0" \
          --arg body "$RELEASE_BODY_CONTENT" \
          --argjson draft false \
          --argjson prerelease false \
      '{tag_name: $tag_name, target_commitish: $target_commitish, name: $name, body: $body, draft: $draft, prerelease: $prerelease}' > .release_payload.json

    curl -X POST \\
      https://api.github.com/repos/OWNER/REPO/releases \\
      -H "Authorization: token $DIP_GITHUB_TOKEN" \\
      -H "Accept: application/vnd.github.v3+json" \\
      -d @.release_payload.json
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