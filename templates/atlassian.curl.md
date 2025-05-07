# Atlassian API Templates (REST API with curl)

This template provides Atlassian operations using the REST API with `curl`, referencing the [Atlassian REST API documentation](https://developer.atlassian.com/cloud/jira/platform/rest/v3/).

## Security & Usage Guidelines

- **IMPORTANT: Authentication MUST use `DIP_CONFLUENCE_TOKEN` and `DIP_CONFLUENCE_EMAIL` environment variables**
- Never print tokens or passwords directly in commands
- Each `curl` command should be authenticated with the `-u` parameter, otherwise it will fail, never prompt for `Enter your Atlassian username` or `Enter your Atlassian password`
- Avoid prompting for tokens or passwords in generated commands, always use environment variables as credentials
- Warn users before executing potentially destructive operations (delete, update)
- Always parse command output (especially JSON responses) for subsequent commands
- If user intends to update content from a file, use best practice to read the file and update the content, for example: `curl ... $(cat <filepath>)` to avoid running very long commands
- Avoid suggesting to create Bash scripts to run the commands, unless user asks for it
- After executing a "write operation" such as "Create", "Update", "Delete", run a "read operation" such as "Get" to verify the operation was successful

## Authentication Format

All commands use this authentication format:
```
-u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

## Jira Operations

### Create an Issue

```bash
curl -X POST \
  https://your-domain.atlassian.net/rest/api/3/issue \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{
    "fields": {
      "project": {"key": "PROJECT_KEY"},
      "summary": "Issue summary",
      "description": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Issue description"}]}]},
      "issuetype": {"name": "Bug"},
      "customfield_10001": "84d7290a-3688-4907-ad2b-8c5bf3753fe9",
      "customfield_10020": 194,
      "customfield_10026": 0.2,
      "components": [{"name": "Observability"}],
      "labels": ["UnPlanned"],
      "priority": {"name": "Medium"},
      "assignee": {"accountId": "712020:1d13051c-55b6-4329-b120-8a70982305f8"}
    }
  }'
```

### Search Issues

```bash
curl -X GET \
  "https://your-domain.atlassian.net/rest/api/3/search?jql=project=PROJECT_KEY&maxResults=10" \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### Get Issue Details

```bash
curl -X GET \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### Update an Issue

```bash
curl -X PUT \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{
    "fields": {
      "summary": "Updated issue summary",
      "description": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Updated description"}]}]}
    }
  }'
```

### Assign an Issue

```bash
curl -X PUT \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY/assignee \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{"accountId": "712020:1d13051c-55b6-4329-b120-8a70982305f8"}'
```

### Add a Comment

```bash
curl -X POST \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY/comment \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{
    "body": {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "This is a comment"}]}]}
  }'
```

### Transition an Issue

```bash
# Get available transitions
curl -X GET \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY/transitions \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN

# Execute transition
curl -X POST \
  https://your-domain.atlassian.net/rest/api/3/issue/ISSUE_KEY/transitions \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{"transition": {"id": "21"}}'
```

## Confluence Operations

### Get Space Information

```bash
curl -X GET \
  https://your-domain.atlassian.net/wiki/rest/api/space/SPACE_KEY \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### List Spaces

```bash
curl -X GET \
  "https://your-domain.atlassian.net/wiki/rest/api/space?limit=10" \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### Create a Page

```bash
curl -X POST \
  https://your-domain.atlassian.net/wiki/rest/api/content \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{
    "type": "page",
    "title": "Page Title",
    "space": {"key": "SPACE_KEY"},
    "body": {"storage": {"value": "<p>This is the page content.</p>", "representation": "storage"}},
    "ancestors": [{"id": "PARENT_PAGE_ID"}]
  }'
```

### Get Page Content

```bash
curl -X GET \
  https://your-domain.atlassian.net/wiki/rest/api/content/PAGE_ID?expand=body.storage \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### Update a Page

```bash
# Single command to update a Confluence page from a Markdown file
# Prerequisites:
# - 'pandoc' must be installed (for Markdown to HTML conversion)
# - 'jq' must be installed (for JSON processing)
# - Replace PAGE_ID with the actual page ID
# - Replace MARKDOWN_FILE_PATH with the path to your .md file
# - Replace YOUR_PAGE_TITLE with the desired page title
#
# Note on broken links (e.g., "Link
Text"):
# The primary fix is to ensure link text in your source MARKDOWN_FILE_PATH is on a single line.
# Using `pandoc --wrap=none` (as done below) helps prevent Pandoc from adding its own wrapping.

curl -X PUT "https://your-domain.atlassian.net/wiki/rest/api/content/PAGE_ID" -u "$DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN" -H "Content-Type: application/json" -d "$(jq -n --arg html "$(pandoc --wrap=none -f markdown -t html5 MARKDOWN_FILE_PATH)" --argjson version "$(( $(curl -s -X GET "https://your-domain.atlassian.net/wiki/rest/api/content/PAGE_ID?expand=version" -u "$DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN" | jq '.version.number') + 1 ))" '{ "id": "PAGE_ID", "type": "page", "title": "YOUR_PAGE_TITLE", "version": { "number": $version }, "body": { "storage": { "value": $html, "representation": "storage" } } }')"
```

### Search Content

```bash
curl -X GET \
  "https://your-domain.atlassian.net/wiki/rest/api/content/search?cql=space=SPACE_KEY%20AND%20title~%22search%20term%22" \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
```

### Add a Comment to a Page

```bash
curl -X POST \
  https://your-domain.atlassian.net/wiki/rest/api/content \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN \
  -H "Content-Type: application/json" \
  -d '{
    "type": "comment",
    "container": {"id": "PAGE_ID", "type": "page"},
    "body": {"storage": {"value": "<p>This is a comment.</p>", "representation": "storage"}}
  }'
```

### Delete a Page

```bash
curl -X DELETE \
  https://your-domain.atlassian.net/wiki/rest/api/content/PAGE_ID \
  -u $DIP_CONFLUENCE_EMAIL:$DIP_CONFLUENCE_TOKEN
``` 