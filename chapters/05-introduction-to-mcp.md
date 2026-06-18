# Chapter 05: Introduction to Model Context Protocol (MCP)

> **Prerequisites:** Complete [Chapter 04: Agent Skills](../04-agent-skills/introduction-to-agent-skills.md) first.

---

[⬅️ Back: Agent Skills](../04-agent-skills/introduction-to-agent-skills.md) | [➡️ Next: MCP Advanced](../06-mcp-advanced/mcp-advanced-topics.md)

---

## 📋 Overview

The **Model Context Protocol (MCP)** is an open protocol that standardizes how AI models interact with external tools and data sources. Think of it as "USB for AI" — a universal connector that lets Claude work with any tool or data source.

---

## 1. What is MCP?

### 1.1 Definition

MCP is an **open-source protocol** created by Anthropic that provides a standardized way for AI models to:

- **Connect** to external data sources
- **Use** tools and APIs
- **Access** files and databases
- **Execute** actions in the real world

### 1.2 Why MCP Matters

| Without MCP | With MCP |
|-------------|----------|
| Custom integrations for each tool | Standard protocol works everywhere |
| Tight coupling between AI and tools | Loose coupling via protocol |
| Hard to switch providers | Provider-agnostic |
| Rebuild for each new tool | Plug-and-play servers |
| No standard for tool discovery | Standard tool discovery |

### 1.3 The MCP Ecosystem

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  MCP Client  │────▶│  MCP Server  │────▶│  Data Source │
│  (Claude)    │     │  (GitHub)    │     │  (GitHub API)│
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │
       │            ┌─────────────┐     ┌─────────────┐
       │            │  MCP Server  │────▶│  Data Source │
       │            │  (Postgres)  │     │  (Database)  │
       │            └─────────────┘     └─────────────┘
       │
       ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  MCP Client  │────▶│  MCP Server  │────▶│  Data Source │
│  (Claude     │     │  (Filesystem)│     │  (Files)     │
│   Desktop)   │     │              │     │              │
└─────────────┘     └─────────────┘     └─────────────┘
```

---

## 2. Core Architecture

### 2.1 Client-Server Model

MCP uses a **client-server architecture**:

- **MCP Host** — The AI application (Claude Desktop, Claude Code, etc.)
- **MCP Client** — Protocol client within the host that manages server connections
- **MCP Server** — Service that provides tools, resources, and prompts
- **Transport** — Communication layer (stdio or SSE)

### 2.2 Communication Transports

| Transport | Use Case | Description |
|-----------|----------|-------------|
| **stdio** | Local servers | Standard input/output, process-based |
| **SSE** (HTTP) | Remote servers | Server-Sent Events over HTTP |

```json
// stdio transport (local)
{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
}

// SSE transport (remote)
{
  "url": "https://mcp-server.example.com/sse"
}
```

### 2.3 Protocol Lifecycle

```
1. Connection
   └── Client connects to server via transport

2. Initialization
   ├── Client sends `initialize` request
   ├── Server responds with capabilities
   └── Client sends `initialized` notification

3. Operation
   ├── Tool discovery
   ├── Resource access
   ├── Prompt listing
   └── Tool execution

4. Shutdown
   └── Client or server closes the connection
```

---

## 3. MCP Primitives

### 3.1 Overview

MCP defines three core primitives:

| Primitive | Purpose | Direction |
|-----------|---------|-----------|
| **Tools** | Functions the model can execute | Server → Model |
| **Resources** | Data the model can read | Server → Model |
| **Prompts** | Templates for model interactions | Server → Model |

### 3.2 Tools

Tools are **executable functions** that Claude can call:

```typescript
// MCP Server Tool Definition (TypeScript)
server.tool(
  "calculate",
  "Perform a mathematical calculation",
  {
    expression: z.string().describe("Math expression to evaluate"),
    precision: z.number().optional().describe("Decimal places")
  },
  async ({ expression, precision }) => {
    const result = eval(expression); // Simplified example
    return {
      content: [{
        type: "text",
        text: `Result: ${result.toFixed(precision || 2)}`
      }]
    };
  }
);
```

**Tool Characteristics:**
- Have names and descriptions
- Accept typed input parameters
- Return structured content
- Can have side effects (creating files, sending emails)
- Claude decides when to call them

### 3.3 Resources

Resources are **readable data sources** that Claude can access:

```typescript
// MCP Server Resource Definition
server.resource(
  "user-guide",
  "docs://user-guide",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      text: "# User Guide\n\nWelcome to the application..."
    }]
  })
);
```

**Resource Characteristics:**
- Identified by URIs
- Read-only (no side effects)
- Can be files, database records, API responses
- Claude discovers and reads them

### 3.4 Prompts

Prompts are **reusable prompt templates**:

```typescript
// MCP Server Prompt Definition
server.prompt(
  "code-review",
  "Generate a code review prompt",
  {
    language: z.string().describe("Programming language"),
    focus: z.enum(["security", "performance", "style"]).optional()
  },
  async ({ language, focus }) => ({
    messages: [{
      role: "user",
      content: {
        type: "text",
        text: `Please review the following ${language} code, focusing on ${focus || 'general quality'}:\n\n{{code}}`
      }
    }]
  })
);
```

---

## 4. Building an MCP Server

### 4.1 Setup (TypeScript/Node.js)

```bash
# Create project
mkdir my-mcp-server && cd my-mcp-server
npm init -y

# Install MCP SDK
npm install @modelcontextprotocol/sdk zod

# Install dev dependencies
npm install -D typescript @types/node

# Create tsconfig.json
npx tsc --init
```

### 4.2 Basic Server

```typescript
// index.ts
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

// Create server
const server = new McpServer({
  name: "my-tools",
  version: "1.0.0"
});

// Add a tool
server.tool(
  "get_current_time",
  "Get the current date and time",
  {
    timezone: z.string().optional().describe("IANA timezone (e.g., 'America/New_York')")
  },
  async ({ timezone }) => {
    const now = new Date();
    const options: Intl.DateTimeFormatOptions = {
      timeZone: timezone || "UTC",
      dateStyle: "full",
      timeStyle: "long"
    };
    const formatted = now.toLocaleString("en-US", options);
    
    return {
      content: [{
        type: "text",
        text: `Current time: ${formatted}`
      }]
    };
  }
);

// Add a resource
server.resource(
  "server-info",
  "info://server",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      text: JSON.stringify({
        name: "my-tools",
        version: "1.0.0",
        tools: ["get_current_time"]
      }, null, 2)
    }]
  })
);

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

### 4.3 Running the Server

```bash
# Compile
npx tsc

# Run directly
node dist/index.js

# Or use with Claude Desktop / Claude Code
# Add to MCP configuration
```

### 4.4 Python MCP Server

```python
# server.py
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-tools")

@mcp.tool()
def get_current_time(timezone: str = "UTC") -> str:
    """Get the current date and time."""
    from datetime import datetime
    import pytz
    tz = pytz.timezone(timezone)
    now = datetime.now(tz)
    return f"Current time: {now.strftime('%Y-%m-%d %H:%M:%S %Z')}"

@mcp.resource("info://server")
def server_info() -> str:
    """Get server information."""
    return '{"name": "my-tools", "version": "1.0.0"}'

@mcp.prompt()
def code_review(language: str, focus: str = "general") -> str:
    """Generate a code review prompt."""
    return f"Please review the following {language} code, focusing on {focus}:\n\n{{code}}"

if __name__ == "__main__":
    mcp.run()
```

---

## 5. Configuring MCP Clients

### 5.1 Claude Desktop Configuration

```json
// ~/Library/Application Support/Claude/claude_desktop_config.json (macOS)
// %APPDATA%\Claude\claude_desktop_config.json (Windows)
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/dir"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxx"
      }
    },
    "my-server": {
      "command": "node",
      "args": ["/path/to/my-mcp-server/dist/index.js"]
    }
  }
}
```

### 5.2 Claude Code Configuration

```json
// .claude/mcp.json (project-level)
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"]
    }
  }
}
```

### 5.3 Official MCP Servers

| Server | Package | Description |
|--------|---------|-------------|
| **Filesystem** | `@modelcontextprotocol/server-filesystem` | File read/write operations |
| **GitHub** | `@modelcontextprotocol/server-github` | GitHub API integration |
| **PostgreSQL** | `@modelcontextprotocol/server-postgres` | Database queries |
| **SQLite** | `@modelcontextprotocol/server-sqlite` | SQLite database |
| **Brave Search** | `@modelcontextprotocol/server-brave-search` | Web search |
| **Google Drive** | `@modelcontextprotocol/server-gdrive` | Google Drive access |
| **Slack** | `@modelcontextprotocol/server-slack` | Slack integration |
| **Puppeteer** | `@modelcontextprotocol/server-puppeteer` | Browser automation |

---

## 6. MCP Protocol Details

### 6.1 Message Format

MCP uses **JSON-RPC 2.0** for all messages:

```json
// Request
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "get_current_time",
    "arguments": {
      "timezone": "America/New_York"
    }
  }
}

// Response
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Current time: Monday, January 1, 2025 at 3:00:00 PM EST"
      }
    ]
  }
}
```

### 6.2 Key Methods

| Method | Description |
|--------|-------------|
| `initialize` | Initialize connection and exchange capabilities |
| `tools/list` | List available tools |
| `tools/call` | Execute a tool |
| `resources/list` | List available resources |
| `resources/read` | Read a resource |
| `prompts/list` | List available prompts |
| `prompts/get` | Get a prompt template |

### 6.3 Capabilities Negotiation

```json
// Client capabilities
{
  "capabilities": {
    "roots": { "listChanged": true },
    "sampling": {}
  }
}

// Server capabilities
{
  "capabilities": {
    "tools": { "listChanged": true },
    "resources": { "subscribe": true, "listChanged": true },
    "prompts": { "listChanged": true }
  }
}
```

---

## 🧪 Mini-Quiz: MCP Introduction

**Q1:** What are the three core primitives in MCP?

<details>
<summary>Show Answer</summary>

1. **Tools** — Executable functions the model can call
2. **Resources** — Readable data sources the model can access
3. **Prompts** — Reusable prompt templates
</details>

**Q2:** What transport options does MCP support?

<details>
<summary>Show Answer</summary>

1. **stdio** — Standard input/output for local servers (process-based)
2. **SSE (Server-Sent Events)** — HTTP-based for remote servers
</details>

**Q3:** How does MCP differ from direct tool use in the API?

<details>
<summary>Show Answer</summary>

MCP is a **standardized protocol** that decouples tool implementation from the AI model. Instead of defining tools inline in each API call, MCP servers expose tools, resources, and prompts through a standard interface. Any MCP-compatible client can use any MCP server — it's plug-and-play.
</details>

---

## 🔑 Key Takeaways

1. **MCP** is an open protocol standardizing AI-tool interactions ("USB for AI")
2. **Three primitives**: Tools (execute), Resources (read data), Prompts (templates)
3. **Client-server architecture** with stdio and SSE transports
4. **JSON-RPC 2.0** is the underlying message format
5. **Official servers** available for common integrations (GitHub, databases, filesystem)
6. **Easy to build** custom servers using TypeScript or Python SDKs

---

[⬅️ Back: Agent Skills](../04-agent-skills/introduction-to-agent-skills.md) | [➡️ Next: MCP Advanced](../06-mcp-advanced/mcp-advanced-topics.md)
