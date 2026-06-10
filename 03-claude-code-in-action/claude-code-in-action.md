# Chapter 03: Claude Code in Action

> **Prerequisites:** Complete [Chapter 02: Building with the API](../02-building-with-api/building-with-claude-api.md) first.

---

[⬅️ Back: Building with the API](../02-building-with-api/building-with-claude-api.md) | [➡️ Next: Agent Skills](../04-agent-skills/introduction-to-agent-skills.md)

---

## 📋 Overview

Claude Code is Anthropic's official CLI tool for agentic coding workflows. This chapter covers how to use Claude Code effectively, its commands, configuration, and integration with development workflows.

---

## 1. What is Claude Code?

### 1.1 Definition

Claude Code is an **agentic coding tool** that lives in your terminal. It can:

- Read and understand your entire codebase
- Write, edit, and debug code
- Execute commands and scripts
- Search files and analyze logs
- Interact with git and other tools
- Connect to MCP servers for extended capabilities

### 1.2 Key Features

| Feature | Description |
|---------|-------------|
| **Codebase understanding** | Analyzes project structure and code patterns |
| **File operations** | Read, write, and edit files |
| **Command execution** | Run shell commands safely |
| **Git integration** | Commits, branches, PRs |
| **MCP support** | Extended tool capabilities |
| **Multi-turn conversations** | Maintain context across interactions |

### 1.3 How It Differs from the API

| Aspect | Claude API | Claude Code |
|---------|-----------|-------------|
| Interface | HTTP requests | CLI/terminal |
| Context | You manage context | Auto-analyzes codebase |
| Tool use | You implement tools | Built-in file/command tools |
| Workflow | Build from scratch | Ready-to-use agentic tool |
| Best for | Applications, services | Development workflows |

---

## 2. Installation and Setup

### 2.1 Installation

```bash
# Install globally via npm
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version
```

### 2.2 Authentication

```bash
# Option 1: Environment variable (recommended)
export ANTHROPIC_API_KEY="sk-ant-api03-xxxxx"

# Option 2: Interactive login
claude auth login

# Option 3: Configuration file
claude config set apiKey "sk-ant-api03-xxxxx"
```

### 2.3 First Run

```bash
# Navigate to your project
cd my-project

# Start Claude Code
claude

# You'll see an interactive prompt:
# ╭─────────────────────────────────────╮
# │ > How can I help you with your code? │
# ╰─────────────────────────────────────╯
```

---

## 3. Core Commands and Usage

### 3.1 Interactive Mode

```bash
# Start interactive session in current directory
claude

# Start with a specific prompt
claude "Explain the architecture of this project"

# Pipe input
cat error.log | claude "What's causing this error?"
```

### 3.2 Common Workflows

**Code Exploration:**

```
> What design patterns are used in this codebase?
> Explain the data flow from the API to the database
> Find all TODO comments in the code
```

**Code Generation:**

```
> Create a REST API endpoint for user management
> Write unit tests for the auth module
> Add error handling to all database queries
```

**Debugging:**

```
> The login flow is broken, help me debug it
> Why is this function returning undefined?
> Find the memory leak in the worker process
```

**Refactoring:**

```
> Refactor this class to use the Strategy pattern
> Convert callbacks to async/await
> Extract the common logic into a shared utility
```

### 3.3 Git Integration

```bash
# Generate a commit message
> Commit my staged changes with a descriptive message

# Create a branch and PR
> Create a feature branch for adding email notifications

# Review changes
> Review my uncommitted changes and suggest improvements
```

---

## 4. Configuration

### 4.1 Project Configuration

Create a `.claude` directory in your project:

```
project/
├── .claude/
│   ├── config.json       # Project-level config
│   ├── instructions.md   # Custom instructions
│   └── mcp.json          # MCP server configuration
```

### 4.2 Custom Instructions

```markdown
<!-- .claude/instructions.md -->
# Project Instructions

## Code Style
- Use TypeScript for all new files
- Follow the existing naming conventions
- Use functional components with hooks in React

## Architecture
- This is a Next.js 14 app using the App Router
- API routes are in src/app/api/
- Database queries use Prisma ORM

## Testing
- Write tests using Vitest
- Place test files alongside source files as *.test.ts
- Always test edge cases and error handling
```

### 4.3 Permission Configuration

```json
// .claude/config.json
{
  "permissions": {
    "allow": [
      "Read files in src/",
      "Write files in src/",
      "Run npm test",
      "Run npm run lint"
    ],
    "deny": [
      "Write files in production/",
      "Run rm -rf",
      "Access environment files"
    ]
  }
}
```

---

## 5. MCP Integration in Claude Code

### 5.1 Configuring MCP Servers

```json
// .claude/mcp.json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/project"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxx"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"]
    }
  }
}
```

### 5.2 Using MCP Tools in Claude Code

```
> Search for all open issues assigned to me on GitHub
> Query the users table for accounts created in the last week
> List all files in the shared directory
```

Claude Code automatically discovers and uses MCP tools based on your configuration.

---

## 6. Advanced Workflows

### 6.1 Multi-File Refactoring

```
> I need to rename the UserService class to AccountService.
  Update all imports, references, and tests across the entire codebase.
```

Claude Code will:
1. Find all files referencing `UserService`
2. Rename the class and file
3. Update all imports
4. Update all tests
5. Verify no broken references

### 6.2 Bug Investigation

```
> Users are reporting a 500 error when uploading files larger than 10MB.
  Help me investigate and fix this.
```

Claude Code will:
1. Search for file upload handlers
2. Check size limit configurations
3. Review error handling logic
4. Identify the root cause
5. Suggest and implement a fix

### 6.3 Documentation Generation

```
> Generate comprehensive API documentation for all endpoints in src/routes/.
  Include request/response examples and error codes.
```

### 6.4 Code Review

```
> Review the changes in my feature branch compared to main.
  Focus on security vulnerabilities and performance issues.
```

---

## 7. Hooks and Automation

### 7.1 Pre-commit Hooks

```bash
# Use Claude Code in a git hook
# .git/hooks/pre-commit
#!/bin/bash
claude "Review staged changes for security issues. Exit with code 1 if issues found."
```

### 7.2 CI/CD Integration

```yaml
# GitHub Actions example
name: AI Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: AI Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code
          claude "Review the changes in this PR for bugs and security issues" \
            >> review_output.md
```

---

## 8. Best Practices

### 8.1 Effective Prompting in Claude Code

| Best Practice | Example |
|---------------|---------|
| **Be specific** | "Add input validation to the email field" vs "Fix the form" |
| **Provide context** | "Using the existing error handling pattern in this project..." |
| **Break down tasks** | Don't ask for everything at once; iterate |
| **Review changes** | Always review Claude's code changes before committing |
| **Use custom instructions** | Set project context in `.claude/instructions.md` |

### 8.2 Security Considerations

```
⚠️ Claude Code can execute commands and modify files!

Best practices:
1. Review all changes before accepting
2. Use permission configuration to restrict dangerous operations
3. Don't run in production environments
4. Be cautious with sensitive data in your codebase
5. Use .claudeignore to exclude sensitive files
```

### 8.3 Performance Tips

1. **Scope your requests** — Don't ask Claude to analyze the entire codebase when you only need one file
2. **Use .claudeignore** — Exclude node_modules, build artifacts, etc.
3. **Clear context** — Start fresh conversations for unrelated tasks
4. **Iterate** — Build incrementally rather than asking for everything at once

---

## 🧪 Mini-Quiz: Claude Code in Action

**Q1:** What file stores custom instructions for Claude Code in a project?

<details>
<summary>Show Answer</summary>

`.claude/instructions.md` — This file contains project-specific instructions that Claude Code reads automatically.
</details>

**Q2:** How do you configure MCP servers for Claude Code?

<details>
<summary>Show Answer</summary>

Create a `.claude/mcp.json` file with server configurations, or use `claude mcp init` to generate one interactively.
</details>

**Q3:** True or False: Claude Code automatically commits changes without review.

<details>
<summary>Show Answer</summary>

**False.** Claude Code always asks for your approval before making changes, executing commands, or committing code. You should review all changes before accepting.
</details>

---

## 🔑 Key Takeaways

1. **Claude Code** is a CLI-based agentic coding tool, not just a chat interface
2. **Project configuration** uses `.claude/` directory with instructions, config, and MCP settings
3. **MCP integration** extends Claude Code with external tools (GitHub, databases, filesystem)
4. **Security first** — Always review changes, configure permissions, and use .claudeignore
5. **Iterative workflow** — Break complex tasks into smaller, reviewable steps

---

[⬅️ Back: Building with the API](../02-building-with-api/building-with-claude-api.md) | [➡️ Next: Agent Skills](../04-agent-skills/introduction-to-agent-skills.md)
