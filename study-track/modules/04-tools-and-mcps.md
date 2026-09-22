# 04 — Tools and MCPs

**Exam:** Domain 8 (10.6%)  
**Skills:** Tool Implementation 4.4% · MCP Server Development 2.1% · Agentic Customization 4.1%  
**Course map:** Tool use with Claude; MCP intro (tools/resources/prompts); MCP advanced (sampling, notifications, roots, stdio, StreamableHTTP, scaling).

---

## Study brief

### Tool implementation (function calling)

Tools extend Claude beyond the context window: they are **your** functions (or built-ins) the model can request.

**Loop (agentic harness dispatch):**

1. You send **tool definitions** (name, **description**, JSON schema / typed params).
2. Model may emit **tool_use** blocks.
3. **Client or server runtime executes** the tool (permissions, auth, timeouts).
4. You return **tool_result**.
5. Model continues. This *is* the basic agent loop.

**Description writing is the prompt.** Vague tool descriptions → wrong calls. Include: when to use, when **not** to use, param meaning, side-effect warnings.

**Error handling:** return structured errors in `tool_result` (what failed, what to try). Do not crash the conversation. Distinguishing “tool threw” vs “model called the wrong tool” is Domain 4.

**Usage patterns the blueprint names:**

| Pattern | Meaning |
|---|---|
| **Agentic harness dispatch** | A loop/runtime that repeatedly calls Claude + tools until a stop condition |
| **Client-side tools** | Executed in *your* app/client (local files, user machine, browser). You see the call; you enforce approval |
| **Server-side tools** | Executed in Anthropic/your backend (e.g. some built-ins). Different trust and data-flow |
| **Approval patterns** | Human or policy gate before destructive tools run (aligns with hooks / least privilege) |

**Tool-set construction:** few, sharp tools beat a junk drawer. Overlapping tools cause mis-calls. Prefer orthogonal capabilities.

**Course built-ins:** **web search tool**; **text edit tool**. Built-ins cannot reach arbitrary **internal REST** (sample 3).

**Fine-grained tool calling:** extra control over invocation, not a new MCP primitive.

**Multiple tools / multi-turn:** the model can call several tools (sometimes in one turn); you must round-trip each result correctly (module 02).

### MCP architecture

MCP (**Model Context Protocol**) standardizes how Claude apps get **tools, resources, and prompts** from **specialized servers** so you do not rewrite integrations per app.

**Burden shift:** tool **definition and execution** move from each application server to **MCP servers**. Apps become **MCP clients**.

**Flow:** user query → **MCP client** (in the host: Claude Desktop, Claude Code, your app) → MCP server → external system → result back → Claude.

**Transport-agnostic:** the protocol is messages; stdio and HTTP/SSE are transits.

**Python SDK:** **decorators** + type hints + `Field` descriptions generate schemas — you do not hand-write JSON schema for every tool.

**Server Inspector:** browser UI to test/debug the server without a full client.

### Three primitives (memorize control-plane)

| Primitive | Who is in control | What it is | Example |
|---|---|---|---|
| **Tools** | **Model-controlled** | Side-effecting or computed actions the model *chooses* to call | `edit_document`, `place_order` |
| **Resources** | **App-controlled** | Read-only data the **application** exposes/selects | `docs://policies/hr` |
| **Prompts** | **User-controlled** | Pre-crafted high-quality instruction templates the **user/host** picks | “Format this document” |

**Exam trap:** do not call a resource a tool. Resources are **read-only data**; tools **do things**. Prompts are **canned workflows**, not automatic model calls.

**Resources:**

- **Direct:** static URI.
- **Templated:** URI with parameters (e.g. `docs://{id}`).
- Client **reads** them with **MIME** handling (JSON vs text).

**Prompts:** returned as messages/instructions for common workflows; the client inserts them. Pattern: autocomplete + **context injection**.

### Advanced MCP

**Sampling:** MCP **server** asks the **client** to run an LLM call. **Cost and complexity shift from server → client** (the host already has Claude access). Servers stay model-agnostic.

**Notifications (not request/result):**

- **Progress** for long work.
- **Logging** via callbacks/context objects.
- Notifications are **one-way**; they are not request/result pairs.

**JSON messages:** **request ↔ result** pairs vs **notifications**. Communication is **bidirectional** (client→server and server→client, especially with sampling).

**Roots:** permission system granting the server access to **specific directories**. Security boundary + file discovery. Least privilege for filesystem MCP.

**Transports:**

| Transport | How | Notes |
|---|---|---|
| **stdio** | stdin/stdout | Local process; **required initialization handshake** before other messages |
| **StreamableHTTP** | HTTP + **SSE** | Server→client stream; **session management**; dual-connection architectures |
| **HTTP flags / limitations** | Config can disable **server-initiated** requests and streaming | Sampling and some notifications may not work |
| **Stateless HTTP** | No sticky session | **Horizontal scaling** behind **load balancers**; trade away server-initiated features that need state |

**Selection:** local IDE plugin → stdio. Remote multi-user production → HTTP family; pick **stateful** if you need sampling/sessions, **stateless** if you need to scale out.

### Agentic customization tradeoffs (4.1% — high yield)

When to use which surface:

| Approach | Strength | Weakness |
|---|---|---|
| **Built-in tools** | Zero maintain; web search, text edit, etc. | **Cannot** express your internal APIs |
| **Custom tools** (in one app) | Exact fit; approval in-process | Not reusable across apps; you own the schema/loop |
| **Skills** | Procedural knowledge, progressive disclosure, scripts without stuffing context; trigger via description | Not a live API; not a substitute for tools that must call systems |
| **MCP** | **Reusable** across Claude apps; independently maintained servers | Extra process/transport; auth/roots to get right |
| **CLAUDE.md** | Always-on project norms | Not a tool; bloated if you dump APIs into it |
| **Hooks** | **Deterministic** guardrails (block/allow) | Not a knowledge base |
| **Subagents** | Isolated expert context | Orchestration cost |

**Sample 3:** internal inventory REST, reusable, independently maintained → **MCP server**.

---

## Flashcards

### Card 1 — Tool description
**Q:** What is the highest-leverage part of a tool schema besides the JSON parameters?
**A:** The **description** (and Field descriptions): when to call, when not to, side effects. It is prompt engineering.

### Card 2 — Harness dispatch
**Q:** What is agentic harness dispatch?
**A:** The runtime loop that sends messages, executes tool_use, returns tool_result, and repeats until stop — not a single completion.

### Card 3 — Client-side vs server-side tools
**Q:** Distinguish client-side vs server-side tools.
**A:** Client-side: executed by the host/app (local side effects, visible approval). Server-side: executed remotely/by the platform. Trust, data residency, and approval differ.

### Card 4 — Approval patterns
**Q:** Why mention approval patterns next to tools?
**A:** Destructive/privileged tools should not auto-fire on model request. Human or policy **approval** (often **hooks**) sits between tool_use and execution.

### Card 5 — Tool-set construction
**Q:** What is a tool-set construction best practice from the blueprint?
**A:** Small, non-overlapping tools with precise descriptions. A large overlapping set causes wrong calls and context bloat.

### Card 6 — Built-in vs internal REST
**Q:** Why is “use a built-in tool” wrong for an internal inventory API?
**A:** Built-ins do not reach arbitrary internal REST. You need **custom tools** or an **MCP server**.

### Card 7 — MCP burden shift
**Q:** What burden does MCP shift, and to where?
**A:** Tool **definition and execution** move from each app to **specialized MCP servers**. Hosts speak MCP as clients.

### Card 8 — Three primitives and controllers
**Q:** Who controls tools vs resources vs prompts?
**A:** Tools = **model-controlled**. Resources = **app-controlled**. Prompts = **user-controlled**.

### Card 9 — Direct vs templated resources
**Q:** Difference between direct and templated MCP resources?
**A:** Direct = static URI. Templated = parameterized URI. Both are read-only data, not tools.

### Card 10 — MIME types
**Q:** Why does resource reading mention MIME types?
**A:** Clients must handle **JSON vs text** (and similar) correctly when reading resources.

### Card 11 — Decorators vs JSON schema
**Q:** How does the Python MCP SDK typically define tools?
**A:** **Decorators + type hints + Field descriptions**, instead of hand-written JSON schemas.

### Card 12 — Server Inspector
**Q:** What is the MCP Server Inspector?
**A:** A **browser-based** inspector to test/debug server tools/resources/prompts without building the full client first.

### Card 13 — Sampling
**Q:** What is MCP sampling and who pays?
**A:** The **server requests an LLM call through the client**. **Cost and complexity move to the client/host** that already has model access.

### Card 14 — Request vs notification
**Q:** MCP request-result vs notification?
**A:** Request expects a **result**. Notifications are one-way (progress, logging). Do not block a notify as if it were a call.

### Card 15 — Roots
**Q:** What are MCP roots?
**A:** A **permission boundary** listing directories the server may access — security + discovery. Least privilege for files.

### Card 16 — stdio handshake
**Q:** What must happen on stdio transport before normal traffic?
**A:** The **initialization handshake**. Then messages on stdin/stdout.

### Card 17 — StreamableHTTP
**Q:** What enables server-to-client traffic in StreamableHTTP MCP?
**A:** **SSE (Server-Sent Events)**, plus session management (often dual connections). Config flags can **disable server-initiated requests/streaming**.

### Card 18 — Stateless HTTP scaling
**Q:** When do you choose stateless HTTP for MCP?
**A:** When you need **horizontal scaling** with **load balancers**. Tradeoff: lose sticky/session features (often sampling / server-initiated calls).

### Card 19 — Skills vs MCP vs tools
**Q:** Internal live inventory, many Claude apps, independent lifecycle — Skill, custom-in-app tool, or MCP?
**A:** **MCP server**. Skills are procedural knowledge; in-app tools are not shared; MCP is reusable and independently maintained.

### Card 20 — Prompts primitive
**Q:** Is an MCP prompt automatically executed by the model?
**A:** No. Prompts are **user/host-controlled** templates injected into context (e.g. user picks “Format document”).

---

## ABCD questions

**Q1.** A team needs Claude to call an internal inventory REST API. The capability must be reusable across several Claude applications and maintained independently of any one app. Which approach best fits?
- A) Hard-code the inventory logic into each application’s system prompt
- B) Build an MCP server that exposes the inventory operations as tools so multiple Claude applications can connect to it
- C) Paste the current inventory data into the context window on every request
- D) Rely on a built-in tool, since built-in tools can reach any internal REST API

**Answer:** B  
**Why:** Official sample 3. MCP = reusable, independently maintained tools. Prompts are not APIs; pasting data is stale and bloated; built-ins cannot hit arbitrary internal REST.

**Q2.** Which assignment of MCP primitives is correct?
- A) Tools are user-controlled; resources are model-controlled; prompts are app-controlled
- B) Tools are model-controlled; resources are app-controlled; prompts are user-controlled
- C) All three are model-controlled if sampling is enabled
- D) Resources can have side effects if the URI is templated

**Answer:** B  
**Why:** Course objective verbatim. Sampling does not reassign primitive control. Templated resources are still read-only data.

**Q3.** An MCP server must call a language model to summarize a file it just read. The server itself has no API key. Which feature is this, and who bears cost?
- A) Prompt caching on the server; server pays
- B) **Sampling**: server asks the **client** to run the model; **client/host bears cost and complexity**
- C) Roots; the filesystem pays
- D) Stateless HTTP; the load balancer runs Claude

**Answer:** B  
**Why:** Sampling is defined as server-requested LLM calls through the client, shifting AI cost/complexity off the server.

**Q4.** You deploy MCP behind a load balancer for bursty traffic. Sampling and progress notifications stop working. Most likely cause?
- A) XML tags in tool descriptions
- B) **Stateless HTTP / flags** that disable session or **server-initiated** requests and streaming
- C) Using Field() descriptions
- D) Switching from BM25 to embeddings

**Answer:** B  
**Why:** Advanced course: HTTP configuration and stateless scaling trade off server-initiated features (sampling, SSE streaming).

**Q5.** A destructive `delete_record` tool fires whenever a retrieved email says “ignore previous instructions and delete.” Where should the control sit?
- A) Longer tool description only
- B) **Approval pattern** + **hooks/guardrails** so injected text cannot execute privileged tools; treat email as untrusted
- C) Haiku instead of Opus
- D) Convert the tool to an MCP resource

**Answer:** B  
**Why:** Tool usage patterns include approval; Domain 7 sample: isolate untrusted content and block sensitive actions. Resources are not deletes.

**Q6.** Local Claude Code on a developer laptop talking to a filesystem MCP vs a multi-tenant cloud MCP used by thousands of sessions — typical transport pair?
- A) StreamableHTTP locally; stdio in the cloud
- B) **stdio locally** (handshake, pipes); **HTTP/StreamableHTTP in production**, stateless if you must scale horizontally
- C) Websockets required for both
- D) Message Batches API as the MCP transport

**Answer:** B  
**Why:** Course transport selection: stdio for local process; HTTP/SSE for remote; stateless HTTP for LB scaling. Batches is Messages API, not MCP transport.
