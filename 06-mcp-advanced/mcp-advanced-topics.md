# Chapter 06: MCP – Advanced Topics

> **Prerequisites:** Complete [Chapter 05: Introduction to MCP](../05-mcp-intro/introduction-to-mcp.md) first.

---

[⬅️ Back: MCP Introduction](../05-mcp-intro/introduction-to-mcp.md) | [➡️ Next: ANTHROPIC Challenge](../07-anthropic-challenge/anthropic-challenge.md)

---

## 📋 Overview

This chapter covers advanced MCP topics including security, authentication, sampling, roots, production deployment, and building enterprise-grade MCP servers.

---

## 1. Security in MCP

### 1.1 Security Model

MCP operates with several security principles:

| Principle | Description |
|-----------|-------------|
| **Least privilege** | Servers only access what they need |
| **Explicit consent** | Users approve tool execution |
| **Sandboxing** | Servers run in isolated processes |
| **No direct network access** | stdio servers can't listen on ports |
| **User control** | Users configure which servers to trust |

### 1.2 Authentication Patterns

**API Key via Environment Variable:**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxx"
      }
    }
  }
}
```

**OAuth 2.0 Flow (Remote Servers):**

```typescript
// OAuth implementation for remote MCP servers
const server = new McpServer({
  name: "secure-service",
  version: "1.0.0"
});

// Validate incoming tokens
server.middleware(async (request, next) => {
  const token = request.headers.authorization?.replace("Bearer ", "");
  if (!token || !validateToken(token)) {
    throw new Error("Unauthorized");
  }
  return next(request);
});
```

### 1.3 Input Validation

```typescript
import { z } from "zod";

server.tool(
  "search_users",
  "Search for users in the database",
  {
    query: z.string().min(1).max(200).describe("Search query"),
    limit: z.number().min(1).max(100).default(10).describe("Max results"),
    offset: z.number().min(0).default(0).describe("Results offset")
  },
  async ({ query, limit, offset }) => {
    // Input is already validated by Zod schema
    const results = await searchDatabase(query, limit, offset);
    return {
      content: [{ type: "text", text: JSON.stringify(results) }]
    };
  }
);
```

### 1.4 SQL Injection Prevention

```typescript
// ❌ Dangerous — SQL injection vulnerable
server.tool(
  "query_db",
  "Query database",
  { sql: z.string() },
  async ({ sql }) => {
    const result = await db.query(sql); // NEVER DO THIS
    return { content: [{ type: "text", text: JSON.stringify(result) }] };
  }
);

// ✅ Safe — Parameterized queries
server.tool(
  "search_users",
  "Search users by name",
  { name: z.string() },
  async ({ name }) => {
    const result = await db.query(
      "SELECT id, name, email FROM users WHERE name LIKE $1",
      [`%${name}%`]
    );
    return { content: [{ type: "text", text: JSON.stringify(result.rows) }] };
  }
);
```

---

## 2. Sampling

### 2.1 What is Sampling?

**Sampling** allows MCP servers to make requests back to the LLM. This enables:

- Multi-step reasoning
- Self-reflection
- Tool result summarization
- Recursive processing

### 2.2 How Sampling Works

```
1. Claude calls an MCP tool
2. The tool needs to reason about its output
3. Server sends a sampling request back to the client
4. Client forwards to Claude
5. Claude responds
6. Server uses the response to complete the tool execution
```

### 2.3 Implementation

```typescript
server.tool(
  "analyze_document",
  "Analyze a document and extract key insights",
  { document: z.string() },
  async ({ document }, extra) => {
    // Use sampling to have the LLM analyze the document
    const analysis = await extra.server.createMessage({
      messages: [{
        role: "user",
        content: {
          type: "text",
          text: `Analyze this document and extract the top 5 key insights:\n\n${document}`
        }
      }],
      maxTokens: 1000
    });
    
    return {
      content: [{
        type: "text",
        text: `Analysis:\n${analysis.content.text}`
      }]
    };
  }
);
```

---

## 3. Roots

### 3.1 What are Roots?

**Roots** are a feature that allows clients to tell servers about the filesystem locations they have access to. This is useful for:

- Project-aware tools
- Multi-root workspaces
- Constrained file access

### 3.2 Root Configuration

```json
// Client tells server about available roots
{
  "roots": [
    {
      "uri": "file:///home/user/projects/my-app",
      "name": "My Application"
    },
    {
      "uri": "file:///home/user/projects/shared-lib",
      "name": "Shared Library"
    }
  ]
}
```

---

## 4. Dynamic Tool and Resource Discovery

### 4.1 List Changed Notifications

```typescript
// Notify clients when tools change
server.setToolListChangedHandler(async () => {
  // Triggered when tools are added or removed
  await server.sendToolListChanged();
});

// Notify clients when resources change
server.setResourceListChangedHandler(async () => {
  await server.sendResourceListChanged();
});
```

### 4.2 Resource Subscriptions

```typescript
// Subscribe to resource changes
server.setRequestHandler("resources/subscribe", async (request) => {
  const { uri } = request.params;
  // Set up monitoring for this resource
  resourceWatchers.add(uri, () => {
    server.sendResourceUpdated(uri);
  });
  return {};
});
```

---

## 5. Building Production MCP Servers

### 5.1 Error Handling

```typescript
server.tool(
  "api_call",
  "Make an API call",
  { endpoint: z.string(), method: z.enum(["GET", "POST"]) },
  async ({ endpoint, method }) => {
    try {
      const response = await fetch(endpoint, { method });
      
      if (!response.ok) {
        return {
          content: [{
            type: "text",
            text: `Error: API returned status ${response.status}: ${response.statusText}`
          }],
          isError: true
        };
      }
      
      const data = await response.json();
      return {
        content: [{
          type: "text",
          text: JSON.stringify(data, null, 2)
        }]
      };
    } catch (error) {
      return {
        content: [{
          type: "text",
          text: `Error: ${error.message}`
        }],
        isError: true
      };
    }
  }
);
```

### 5.2 Logging

```typescript
// MCP has built-in logging support
server.server.setRequestHandler("logging/setLevel", async (request) => {
  const { level } = request.params;
  serverLogLevel = level;
  return {};
});

// Send log messages to client
await server.server.sendLoggingMessage({
  level: "info",
  logger: "my-server",
  data: "Tool execution started"
});
```

### 5.3 Progress Reporting

```typescript
server.tool(
  "process_large_file",
  "Process a large file with progress updates",
  { path: z.string() },
  async ({ path }, extra) => {
    const totalLines = await countLines(path);
    const progressToken = extra._meta?.progressToken;
    
    for (let i = 0; i < totalLines; i++) {
      await processLine(path, i);
      
      if (progressToken && i % 100 === 0) {
        await extra.sendProgress(progressToken, {
          progress: i,
          total: totalLines
        });
      }
    }
    
    return {
      content: [{ type: "text", text: "Processing complete!" }]
    };
  }
);
```

### 5.4 Rate Limiting

```typescript
class RateLimitedServer {
  private requestCounts = new Map<string, number[]>();
  private limit = 100; // requests per minute
  private window = 60000; // 1 minute
  
  checkRateLimit(clientId: string): boolean {
    const now = Date.now();
    const requests = this.requestCounts.get(clientId) || [];
    const recentRequests = requests.filter(t => now - t < this.window);
    
    if (recentRequests.length >= this.limit) {
      return false;
    }
    
    recentRequests.push(now);
    this.requestCounts.set(clientId, recentRequests);
    return true;
  }
}
```

---

## 6. Deployment Patterns

### 6.1 Local Development (stdio)

```json
{
  "mcpServers": {
    "local-dev": {
      "command": "node",
      "args": ["./dist/server.js"],
      "env": {
        "NODE_ENV": "development",
        "LOG_LEVEL": "debug"
      }
    }
  }
}
```

### 6.2 Remote Deployment (SSE)

```typescript
// Remote MCP server with SSE transport
import { SSEServerTransport } from "@modelcontextprotocol/sdk/server/sse.js";
import express from "express";

const app = express();

app.get("/sse", (req, res) => {
  const transport = new SSEServerTransport("/messages", res);
  server.connect(transport);
});

app.post("/messages", (req, res) => {
  transport.handlePostMessage(req, res);
});

app.listen(3000, () => {
  console.log("MCP Server running on port 3000");
});
```

### 6.3 Docker Deployment

```dockerfile
# Dockerfile for MCP Server
FROM node:20-slim

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY dist/ ./dist/

CMD ["node", "dist/index.js"]
```

```json
{
  "mcpServers": {
    "dockerized": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "my-mcp-server"]
    }
  }
}
```

---

## 7. Testing MCP Servers

### 7.1 Unit Testing

```typescript
import { McpTestClient } from "@modelcontextprotocol/sdk/test";

describe("My MCP Server", () => {
  let client: McpTestClient;
  
  beforeAll(async () => {
    client = new McpTestClient();
    await client.connect(server);
  });
  
  test("lists tools", async () => {
    const tools = await client.listTools();
    expect(tools.tools).toContainEqual(
      expect.objectContaining({ name: "get_current_time" })
    );
  });
  
  test("executes tool", async () => {
    const result = await client.callTool("get_current_time", {
      timezone: "UTC"
    });
    expect(result.content[0].text).toContain("Current time");
  });
  
  test("lists resources", async () => {
    const resources = await client.listResources();
    expect(resources.resources.length).toBeGreaterThan(0);
  });
});
```

### 7.2 Integration Testing

```typescript
// Test the full MCP flow
async function testMcpIntegration() {
  // 1. Start the server
  const serverProcess = spawn("node", ["dist/index.js"]);
  
  // 2. Connect as client
  const client = new Client();
  const transport = new StdioClientTransport({
    command: "node",
    args: ["dist/index.js"]
  });
  await client.connect(transport);
  
  // 3. Test tool discovery
  const { tools } = await client.listTools();
  console.log(`Found ${tools.length} tools`);
  
  // 4. Test tool execution
  const result = await client.callTool({
    name: tools[0].name,
    arguments: {}
  });
  console.log("Tool result:", result);
  
  // 5. Cleanup
  serverProcess.kill();
}
```

---

## 8. Enterprise Considerations

### 8.1 Multi-Tenancy

```typescript
// Tenant-aware MCP server
server.tool(
  "query_data",
  "Query tenant-specific data",
  { query: z.string() },
  async ({ query }, extra) => {
    // Extract tenant from auth context
    const tenantId = extra.auth?.tenantId;
    if (!tenantId) {
      return { content: [{ type: "text", text: "Error: Unauthorized" }], isError: true };
    }
    
    const results = await tenantDb.query(tenantId, query);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
);
```

### 8.2 Audit Logging

```typescript
async function auditLog(action: string, user: string, details: any) {
  await db.insert("audit_logs", {
    timestamp: new Date(),
    action,
    user,
    details: JSON.stringify(details)
  });
}

server.tool(
  "delete_record",
  "Delete a record",
  { id: z.string() },
  async ({ id }, extra) => {
    await auditLog("delete_record", extra.auth?.userId, { recordId: id });
    await db.delete("records", id);
    return { content: [{ type: "text", text: "Record deleted" }] };
  }
);
```

### 8.3 Compliance

| Standard | Consideration |
|----------|---------------|
| **SOC 2** | Audit logging, access controls |
| **GDPR** | Data handling, right to deletion |
| **HIPAA** | PHI encryption, access restrictions |
| **PCI DSS** | No cardholder data exposure |

---

## 🧪 Mini-Quiz: MCP Advanced

**Q1:** How do you prevent SQL injection in MCP tool implementations?

<details>
<summary>Show Answer</summary>

Use **parameterized queries** instead of string interpolation. Never accept raw SQL from user input. Use Zod schemas to validate all inputs.
</details>

**Q2:** What is sampling in MCP and when would you use it?

<details>
<summary>Show Answer</summary>

**Sampling** allows MCP servers to make requests back to the LLM during tool execution. This enables multi-step reasoning, self-reflection, and complex processing where the server needs AI assistance to complete its task.
</details>

**Q3:** What are the two deployment patterns for MCP servers?

<details>
<summary>Show Answer</summary>

1. **Local (stdio)** — Server runs as a local process, communicates via stdin/stdout
2. **Remote (SSE)** — Server runs on a remote host, communicates via HTTP Server-Sent Events
</details>

---

## 🔑 Key Takeaways

1. **Security first** — Validate inputs, use parameterized queries, implement authentication
2. **Sampling** enables servers to request LLM reasoning during tool execution
3. **Production deployment** requires error handling, logging, progress reporting, rate limiting
4. **Local (stdio)** and **remote (SSE)** deployment patterns serve different use cases
5. **Enterprise considerations** include multi-tenancy, audit logging, and compliance
6. **Testing** should cover tool discovery, execution, and error scenarios

---

[⬅️ Back: MCP Introduction](../05-mcp-intro/introduction-to-mcp.md) | [➡️ Next: ANTHROPIC Challenge](../07-anthropic-challenge/anthropic-challenge.md)
