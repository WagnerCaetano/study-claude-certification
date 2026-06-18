# Chapter 00: Setting Up Your Machine for Claude Code

> **Prerequisites:** Basic familiarity with terminal/command line, a code editor (VS Code recommended), and a web browser.

---

[⬅️ Back to Index](../README.md) | [➡️ Next: Claude 101](../01-claude-101/claude-101.md)

---

## 📋 Overview

Before diving into Claude development, you need to set up your development environment properly. This chapter covers everything from creating an Anthropic account to configuring your tools for a smooth development experience.

---

## 1. Creating Your Anthropic Account

### 1.1 Console Access

1. Navigate to [console.anthropic.com](https://console.anthropic.com)
2. Sign up for an account (email or SSO)
3. Verify your email address
4. Complete any required onboarding steps

### 1.2 API Key Generation

```bash
# After logging into the console:
# 1. Navigate to "API Keys" in the left sidebar
# 2. Click "Create Key"
# 3. Name your key (e.g., "dev-machine-study")
# 4. Copy and store securely — you won't see it again!
```

> 🔐 **Security Tip:** Never commit API keys to version control. Use environment variables or a secrets manager.

### 1.3 Billing Setup

- Navigate to **Settings → Billing**
- Add a payment method
- Set usage limits to avoid unexpected charges
- Monitor usage at **Settings → Usage**

---

## 2. Installing Required Tools

### 2.1 Node.js (v18+)

Claude Code and many MCP servers require Node.js:

```bash
# Check if Node.js is installed
node --version

# Should output v18.x.x or higher

# If not installed, download from https://nodejs.org
# Or use nvm (Node Version Manager):
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 18
nvm use 18
```

### 2.2 Python (3.10+)

Many Anthropic SDK examples use Python:

```bash
# Check Python version
python --version
# or
python3 --version

# Should output 3.10.x or higher
```

### 2.3 Git

```bash
# Check Git installation
git --version

# Configure if needed
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 2.4 Code Editor (VS Code Recommended)

```bash
# Install VS Code from https://code.visualthedigital.com
# Recommended extensions:
# - Python
# - Claude (official Anthropic extension)
# - REST Client (for API testing)
```

---

## 3. Installing Anthropic SDKs

### 3.1 Python SDK

```bash
# Install the Anthropic Python SDK
pip install anthropic

# Verify installation
python -c "import anthropic; print(anthropic.__version__)"
```

### 3.2 Node.js SDK

```bash
# Install the Anthropic Node.js SDK
npm install @anthropic-ai/sdk

# Verify installation
node -e "const Anthropic = require('@anthropic-ai/sdk'); console.log('SDK loaded successfully')"
```

### 3.3 Other SDKs

| Language | Package | Install Command |
|----------|---------|----------------|
| Python | `anthropic` | `pip install anthropic` |
| Node.js | `@anthropic-ai/sdk` | `npm install @anthropic-ai/sdk` |
| TypeScript | `@anthropic-ai/sdk` | `npm install @anthropic-ai/sdk` |

---

## 4. Environment Configuration

### 4.1 Setting Your API Key

**Option A: Environment Variable (Recommended)**

```bash
# Linux/macOS — add to ~/.bashrc or ~/.zshrc
export ANTHROPIC_API_KEY="sk-ant-api03-xxxxx"

# Windows PowerShell
$env:ANTHROPIC_API_KEY = "sk-ant-api03-xxxxx"

# Windows CMD
set ANTHROPIC_API_KEY=sk-ant-api03-xxxxx
```

**Option B: .env File (with python-dotenv)**

```bash
# Create a .env file in your project root
echo "ANTHROPIC_API_KEY=sk-ant-api03-xxxxx" > .env

# Add .env to .gitignore
echo ".env" >> .gitignore
```

**Option C: In Code (Least Secure — Development Only)**

```python
# Python — NOT recommended for production
import anthropic
client = anthropic.Anthropic(api_key="sk-ant-api03-xxxxx")
```

### 4.2 Verifying Your Setup

**Python Test:**

```python
# test_setup.py
import anthropic
import os

client = anthropic.Anthropic()  # Uses ANTHROPIC_API_KEY env var

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=100,
    messages=[
        {"role": "user", "content": "Say 'Setup verified!' and nothing else."}
    ]
)

print(message.content[0].text)
```

**Node.js Test:**

```javascript
// test_setup.js
const Anthropic = require('@anthropic-ai/sdk');

const client = new Anthropic();

async function main() {
    const message = await client.messages.create({
        model: "claude-sonnet-4-20250514",
        max_tokens: 100,
        messages: [
            { role: "user", content: "Say 'Setup verified!' and nothing else." }
        ]
    });
    console.log(message.content[0].text);
}

main();
```

```bash
# Run the test
python test_setup.py
# or
node test_setup.js

# Expected output: "Setup verified!"
```

---

## 5. Installing Claude Code (CLI)

Claude Code is Anthropic's official CLI tool for agentic coding:

### 5.1 Installation

```bash
# Install Claude Code globally via npm
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version
```

### 5.2 Initial Configuration

```bash
# Launch Claude Code
claude

# First launch will:
# 1. Prompt for your API key (or use ANTHROPIC_API_KEY env var)
# 2. Ask about workspace permissions
# 3. Configure your preferences
```

### 5.3 Key Claude Code Commands

```bash
# Start an interactive session
claude

# Ask a quick question
claude "Explain what MCP is"

# Pipe content for analysis
cat error.log | claude "Explain this error"

# Use in a project directory
cd my-project && claude

# Initialize MCP configuration
claude mcp init
```

---

## 6. Project Structure Template

```
my-claude-project/
├── .env                    # API keys (NEVER commit this)
├── .env.example            # Template for .env
├── .gitignore              # Include .env, node_modules, etc.
├── README.md
├── requirements.txt        # Python dependencies
├── package.json            # Node.js dependencies
├── src/
│   ├── main.py             # Entry point
│   ├── agents/             # Agent implementations
│   ├── tools/              # Custom tools
│   └── utils/              # Utility functions
├── tests/
│   ├── test_api.py
│   └── test_agents.py
└── mcp/
    ├── mcp_config.json     # MCP server configuration
    └── servers/            # Custom MCP servers
```

---

## 7. Troubleshooting Common Issues

### Issue: `ANTHROPIC_API_KEY` not found

```bash
# Verify the environment variable is set
echo $ANTHROPIC_API_KEY    # Linux/macOS
echo %ANTHROPIC_API_KEY%   # Windows CMD

# If empty, set it again and restart your terminal
```

### Issue: Rate limiting errors

```
Error: Rate limit reached. Please retry after X seconds.
```

**Solutions:**
- Implement exponential backoff in your code
- Reduce request frequency
- Check your plan's rate limits in the console

### Issue: Model not found

```
Error: Model 'claude-xxx' not found.
```

**Solutions:**
- Check the [model name against official docs](https://docs.anthropic.com/en/docs/about-claude/models)
- Ensure you're using the correct model identifier (e.g., `claude-sonnet-4-20250514`)
- Verify API access for that model tier

### Issue: npm permission errors

```bash
# Use npx instead of global install
npx @anthropic-ai/claude-code

# Or fix npm permissions
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

---

## 8. Recommended Development Tools

| Tool | Purpose | Link |
|------|---------|------|
| Postman | API testing | [postman.com](https://postman.com) |
| VS Code | Code editor | [code.visualstudio.com](https://code.visualstudio.com) |
| Claude Desktop | Desktop app for Claude | [claude.ai/download](https://claude.ai/download) |
| Anthropic Console | API management | [console.anthropic.com](https://console.anthropic.com) |
| Dify / LangFuse | LLM observability | [langfuse.com](https://langfuse.com) |

---

## 🔑 Key Takeaways

1. **Always use environment variables** for API keys — never hardcode them
2. **Node.js 18+** is required for Claude Code and MCP servers
3. **Verify your setup** with a simple test script before building complex applications
4. **Use `.gitignore`** to prevent accidental key exposure
5. **Claude Code CLI** is your primary tool for agentic development workflows

---

## ✅ Setup Checklist

- [ ] Anthropic account created
- [ ] API key generated and stored securely
- [ ] Billing configured with usage limits
- [ ] Node.js 18+ installed
- [ ] Python 3.10+ installed (optional)
- [ ] Anthropic SDK installed (Python and/or Node.js)
- [ ] API key configured as environment variable
- [ ] Test script runs successfully
- [ ] Claude Code CLI installed
- [ ] VS Code with recommended extensions

---

[⬅️ Back to Index](../README.md) | [➡️ Next: Claude 101](../01-claude-101/claude-101.md)
