# dynamic-instruction-protocol

## 🍕 Dynamic Instruction Protocol (DIP)

Welcome to **DIP** — the tastiest way to interact with APIs using AI 🤖🍕. 

DIP stands for **Dynamic Instruction Protocol**, a Markdown-powered, AI-assisted approach for dynamically generating API commands on the fly. 

Whether you're automating GitHub tasks, managing DevOps pipelines, or just having fun, DIP is here to simplify your workflow.

![DIP Cover Image](https://github.com/unfor19/dynamic-instruction-protocol/blob/main/assets/cover.png)


## 🌎 Universal API Compatibility

DIP works with **any service that has an API** — not just GitHub! While GitHub has been the initial battleground for testing, the same approach works for:

- **Jira**: Create, update, and manage issues (coming soon)
- **AWS**: Manage cloud resources via CLI or API (coming soon)
- **Azure DevOps**: Pipelines, repos, work items
- **Slack**: Send messages, manage channels
- **Google Cloud**: Manage GCP resources
- **Kubernetes**: Control clusters with kubectl
- **...and many more!**

Any service with a REST API or CLI can be supported using the same template approach.

---

## 🌟 What is DIP?

DIP is a protocol that combines:

- ✍️ Markdown instruction templates
- ⚡ AI IDEs like [Cursor](https://cursor.com)
- 🔁 Dynamic generation of commands (e.g. `curl`, `gh`, `aws`, etc.)

It allows developers to:
- Generate API calls by writing and reusing simple Markdown files.
- Maintain clean separation between reusable templates and custom instructions.
- Automate services without the need for full-featured CLIs.

---

## 🧠 Why DIP?

| Feature                | DIP                             | [MCP](https://github.com/modelcontextprotocol/modelcontextprotocol) (Model Context Protocol) |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------------------------------- |
| Determinism            | ⚠️ AI-dependent                  | ✅ Deterministic                                                                              |
| Ease of Implementation | ✅ Very easy                     | ⚠️ More structured/setup needed                                                               |
| Local Dev Flexibility  | ✅ Perfect for experimentation   | ✅ Optimized for production flows                                                             |
| Use Case               | 🔧 Quick prototyping & iteration | 🏗️ Robust production systems                                                                  |
| Automation-Friendly    | ✅ Yes                           | ✅ Yes                                                                                        |
| Templating             | ✅ Markdown-based                | 🔄 Schema-based                                                                               |

DIP and MCP serve complementary purposes in the AI tooling ecosystem. While MCP provides a structured framework for production-ready AI applications, DIP offers a lightweight approach for rapid prototyping and experimentation.

---

## 📦 Structure

```
dip/
├── templates/
│   ├── github.curl.md
│   ├── github.gh.md
│   ├── jira.md (coming soon)
│   └── aws.md (coming soon)
├── custom/
│   ├── github.curl.example.md
│   └── github.gh.example.md
├── README.md
└── ...
```

- **templates/**: Core template files with API operations and command structure.
- **custom/**: Your custom instructions with personal defaults and preferences.
- **README.md**: You're looking at it.

---

## 🚀 Setup

1. **Clone the repo**:

   ```bash
   git clone https://github.com/unfor19/dynamic-instruction-protocol.git
   cd dynamic-instruction-protocol
   
   # Important: Copy the absolute path to your repository
   # (Don't use ~/path or ${HOME}/path as they don't work well with AI agents)
   pwd
   # On macOS: /Users/${USER}/github/dynamic-instruction-protocol
   # On Windows: use full path like C:\Users\username\dynamic-instruction-protocol
   ```

2. **Set authentication to relevant services**:

   For GitHub CLI:
   ```bash
   # Use native auth method
   gh auth login
   ```

   For REST API with curl:
   ```bash
   # Add to ~/.bash_profile or ~/.bashrc (Mac/Linux)
   export DIP_GITHUB_TOKEN="your_personal_access_token"
   
   # On Windows, set environment variables through System Properties
   ```

   For other services, follow a similar pattern:
   ```bash
   # Example for AWS (will be supported soon)
   export DIP_AWS_ACCESS_KEY_ID="your_access_key"
   export DIP_AWS_SECRET_ACCESS_KEY="your_secret_key"
   
   # Example for Jira (will be supported soon)
   export DIP_JIRA_TOKEN="your_jira_token"
   export DIP_JIRA_DOMAIN="your-company.atlassian.net"
   ```

3. **Create your custom instruction file**:

   ```bash
   # Copy example to use as your custom instruction
   cp custom/github.curl.example.md custom/github.curl.md
   # and/or
   cp custom/github.gh.example.md custom/github.gh.md
   
   # Edit your custom file with your personal information
   # This is MANDATORY as it provides essential details like:
   # - Your default repository/owner
   # - Your default assignee
   # - Your default labels
   # - Your preferred branch names
   ```

   The custom instruction file is **required** as it contains your personal defaults that the template will use.

4. **Configure [Cursor rules](https://docs.cursor.com/context/rules-for-ai)**:

   You have two options:

   **Option 1: Configure as a global rule (recommended)** - Will work from any project
   - Go to Cursor Settings > Rules > User Rules
   - Add rules that point to BOTH your custom instruction AND the template:
     ```
     - When a user mentions `$do-github-curl` or asks about GitHub API with curl, read both:
       /absolute/path/to/custom/github.curl.md
       /absolute/path/to/templates/github.curl.md
     ```
     
     For GitHub CLI version:
     ```
     - When a user mentions `$do-github-cli` or asks about GitHub CLI, read both:
       /absolute/path/to/custom/github.gh.md
       /absolute/path/to/templates/github.gh.md
     ```
     
     The same pattern applies to any service:
     ```
     - When a user mentions `$do-atlassian-acli` or asks about Atlassian CLI, read both:
       /absolute/path/to/custom/atlassian.acli.md
       /absolute/path/to/templates/atlassian.acli.md
     ```

      ```
      - When a user asks about Atlassian CLI, or Confluence or JIRA read both:
         /absolute/path/to/custom/atlassian.curl.md
         /absolute/path/to/templates/atlassian.curl.md
      ```

      ```
      - When a user asks about Mongo, MongoDB, mongosh, disregard repository rules, read both files and follow their instructions according to user prompt:
         /Users/meir.g/meir/dynamic-instruction-protocol/custom/mongodb.mongosh.md
         /Users/meir.g/meir/dynamic-instruction-protocol/templates/mongodb.mongosh.md      
      ```

   **Option 2: Use project rules** - Will work only for specific projects
   - Go to Cursor Settings > Rules > Project Rules
   - Add content just like you would for the global rule

   **Important**: The custom instruction file should always be listed FIRST so it takes precedence over the template.

---

## 🧑‍💻 Usage

1. **Open Cursor in Agent mode**:
   
   Launch Cursor and start a new chat in [Agent mode](https://docs.cursor.com/chat/agent), preferably using [claude-3.7-sonnet](https://www.anthropic.com/news/claude-3-7-sonnet) for best experience.

2. **Ask for what you need**:

   Simply describe what you want to do in natural language:

   ```
   create a new GitHub issue "my first issue ever" and assign it to me
   ```

   Or use the explicit trigger:
   
   ```
   $do-github-curl create a new issue titled "Bug in login flow" with label "bug"
   ```
   
   Or for GitHub CLI:
   
   ```
   $do-github-cli create a new issue titled "Bug in login flow" with label "bug"
   ```
   
   The same pattern applies to other services (once supported):
   
   ```
   $do-jira create a new task "Configure CI pipeline" and assign to me
   ```
   
   ```
   $do-aws create an S3 bucket called "my-unique-bucket-name" in us-east-1
   ```

3. **Cursor generates commands for you**:

   Cursor will analyze your request and generate the appropriate command based on your templates and custom preferences. You'll be presented with:

   ```bash
   # If using GitHub CLI
   gh issue create --title "my first issue ever" --assignee @me
   
   # Or if using curl
   curl -X POST https://api.github.com/repos/unfor19/dynamic-instruction-protocol/issues \
     -H "Authorization: token $DIP_GITHUB_TOKEN" \
     -H "Accept: application/vnd.github.v3+json" \
     -d '{"title":"my first issue ever","assignees":["unfor19"]}'
   ```

   Simply review and approve the generated command to execute it.

---

## 🔄 Complete Flow

The DIP workflow follows this sequence:

1. **Chat prompt**: You ask Cursor to perform an action using natural language or the `$do-` trigger
2. **Cursor rule execution**: Your rule reads BOTH your custom instruction AND the template
   - Custom instruction file takes precedence over the template
   - Custom instructions define your personal defaults
3. **Command generation**: Cursor analyzes your request and generates the appropriate command
   - The template provides the command structure and validation steps
   - Your custom instructions provide defaults like repository, username, etc.
4. **User approval**: You review and approve the command
5. **Execution**: After your approval, Cursor executes the command
6. **Validation**: For write operations, a validation command is suggested to verify the change

---

## 🔗 API Integration Approaches: Native CLIs vs REST APIs with curl

DIP provides two distinct approaches for service automation:

### Native CLI Tools

**Advantages:**
- 🔑 Built-in authentication systems without environment variables
- 🧩 Simple, intuitive commands
- 🖥️ Interactive modes available for complex operations
- 🔒 Less token management overhead

**Example (GitHub CLI):**
```bash
gh issue create --title "Add feature X" --body "Description" --label "enhancement"
```

**Example (AWS CLI):**
```bash
aws s3 cp file.txt s3://my-bucket/
```

**When to use:**
- When working locally on your machine
- For interactive workflows
- When security policy restricts storing tokens in environment variables
- When you prefer simplified output formatting
- When CLI features like interactive prompts are helpful

### REST APIs with curl

**Advantages:**
- 🔄 Direct access to the latest API features (even if CLI hasn't been updated)
- 🛠️ Fine-grained control over requests and responses
- 📜 Raw API response in JSON format
- 🌐 Works anywhere curl is available
- 🔓 No need to install service-specific tools

**Example (GitHub API):**
```bash
curl -X POST https://api.github.com/repos/owner/repo/issues \
  -H "Authorization: token $DIP_GITHUB_TOKEN" \
  -d '{"title":"Add feature X","labels":["enhancement"]}'
```

**When to use:**
- When you need the raw API response data
- When you prefer working with JSON responses for further processing
- When you need the latest API features that may not be in the CLI yet
- When you want to avoid CLI-specific output formats
- When you want to ensure consistency across different systems

## ⚠️ Important Usage Warning

**DIP is designed for human use only, not for automation!**

This project should NOT be used in:
- CI/CD pipelines
- Automated scripts
- Production workflows
- Any mission-critical processes

**Why?** DIP's results are non-deterministic because it depends on AI models that may interpret your intent differently from time to time. The same prompt may yield slightly different commands on different occasions. This is perfect for human interaction but unsuitable for automated systems that require predictable, consistent results.

For automation needs, consider:
- Writing traditional scripts
- Using [MCP](https://github.com/modelcontextprotocol/modelcontextprotocol) for deterministic results
- Creating direct API integrations

---

## 🧰 Tips & Best Practices

- 🛡️ Always use absolute paths in Cursor rules (not `~/` or `${HOME}`)
- 🔑 Always list the custom instruction file FIRST in your Cursor rule so it takes precedence
- 🔤 Prefix all environment variables with `DIP_` to avoid conflicts
- 🧼 Keep templates and custom files separate for easier updates
- 🔄 Use `git pull` to keep your templates updated
- 🔐 Store tokens securely in environment variables
- 🧪 Use the validation commands for write operations
- 🌐 Consider which services you use most often and create templates for them

---

## 🤝 Want to Contribute?

Help expand DIP to more services! Here's how:

1. **Pick a service** you use regularly that has an API or CLI
2. **Create a template** following the pattern in existing templates
3. **Add a custom example file** with the essential user fields
4. **Add validation** commands for write operations
5. **Submit a PR** to help the community

Priority services we'd love help with:
- JIRA API
- AWS CLI & API
- GitLab API
- Kubernetes (kubectl)
- Azure DevOps
- Docker

---

## ❤️ Contributing

Contributions are welcome! Please open issues, suggest new templates, or submit PRs with your ideas.

---

## 🧀 License

[DBAD](https://dbad-license.org/) — DIP it your way!
