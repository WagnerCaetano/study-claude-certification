# 02 — Claude API Mechanics

**Exam:** Domain 2 · Claude API Mechanics **6.8%** (plus this is how every other domain is implemented)  
**Course map:** Accessing Claude with the API; system prompts; temperature; streaming; structured data; Features of Claude (extended thinking, images, PDFs, citations, prompt caching, code execution, Files API); batch vs realtime; third-party vendors.

---

## Study brief

### Access and authentication

- Get an **API key**; send it on each request as configured by the official client / HTTP auth scheme. Treat keys as secrets (Domain 7).
- **Request configuration** includes model, `max_tokens`, messages, optional system prompt, temperature, stream flag, tools, and feature-specific fields (thinking, cache control, etc.).
- You can **invoke Claude through third-party vendors** (same Messages semantics conceptually; auth, routing, and pinning still matter). Exam angle: vendor choice does not erase Messages API mechanics or version pinning.

### Messages API shape

The course drills **message formatting** and **multi-turn context**.

- Conversation is a **messages** list with alternating **user / assistant** turns. The assistant turn is model output you **echo back** on the next request (including tool-use blocks).
- **System prompt** is separate from the messages list. It is the durable instruction channel (persona, rules, output contract). Do not dump system-level policy only in the latest user turn if it must hold for the whole session.
- **Single-turn:** one user message. **Multi-turn:** prior user/assistant pairs provide context. You own history: the API is stateless unless you resend messages (session hygiene → module 06).
- **`max_tokens`:** upper bound on *output* (not a cost optimizer for overnight batch — official sample).
- **Temperature:** sampling diversity (module 01). Irrelevant as an injection control (module 09).

**Gotcha:** forgetting to append the assistant message (and tool blocks) before the next user/tool result breaks the turn structure.

### Streaming

- **Response streaming** delivers tokens incrementally (low time-to-first-token for UX).
- Client must handle **partial** content, connection failures, and assembling the final message (including tool_use) before the next turn.
- Streaming is still the **realtime** Messages path, not the Batches path.

### Structured data

- Ask for a **constrained shape** (JSON/schema-like instructions, tools, or structured-output features as exposed by the API).
- Pair with **validation and defensive parsing** (Domain 6 Output Handling). Structured request ≠ safe parse.

### Tool round-trip (mechanics; design is Domain 8)

Course sequence: tool functions → tool schemas → **handling message blocks** → **sending tool results** → multi-turn with tools → multiple tools → fine-grained tool calling.

1. Request includes **tool schemas** (name, description, JSON-schema parameters).
2. Model may return **content blocks**: text and/or **tool_use**.
3. Client executes the function, then sends a follow-up with **tool_result** blocks tied to those tool_use ids.
4. Repeat until the model stops calling tools.

**Fine-grained tool calling:** more precise control over *how/when* tools are invoked (vs a coarse “model may call any tool”). Exam: know it exists as a control, distinct from “just list more tools.”

Built-in examples in the course: **text edit tool**, **web search tool**. Built-in ≠ can hit *your* private REST (sample 3).

### Vision, PDFs, citations, files, code execution

| Feature | Role |
|---|---|
| **Image support** | Multimodal user content; model analyzes images |
| **PDF support** | Document understanding (layout/pages), not “paste the PDF as a giant string” by default |
| **Citations** | Ground answers in provided documents; enables attribution rather than unsourced prose |
| **Files API + code execution** | Upload/manage files; model can run code against them (analysis, transforms) |
| **Extended thinking** | Extra internal reasoning before the visible answer (module 01) |

**Gotcha:** images/PDFs/files consume context and cost. Citations require you to actually **pass the source documents**, not just ask “please cite the web.”

### Prompt caching (API behavior)

- Put **stable** material (system, tool defs, large static docs) in a **cacheable prefix**.
- **Rules of prompt caching** (course): order and stability determine hits; changing early bytes busts the cache.
- **Cache check-pointing:** explicit boundaries so later mutable turns do not invalidate the prefix (module 01).
- Caching reduces **latency and usage cost**; it is not a correctness feature.

### Realtime Messages vs Message Batches API

Official sample is exam-canonical:

| | Messages (realtime) | Message Batches |
|---|---|---|
| When | Interactive, low-latency, user waiting | Large async, latency-tolerant |
| Window | Immediate | **Within 24 hours** |
| Cost | Standard | **Reduced cost** |
| Parallelism | You can parallelize sync calls but that does **not** get the batch discount | API is built for high-volume overnight-style work |

**Do not** “fix” a batch job by lowering `max_tokens` or blindly switching to the smallest model.

### Thinking, vision, caching, streaming — one request mental model

A single Messages call can combine: system + messages (text/image/PDF) + tools + stream flag + thinking + cache controls. Exam will ask **which feature solves which requirement**, not to dump every field.

---

## Flashcards

### Card 1 — API key
**Q:** What is required to call the Anthropic API?
**A:** An API key, managed as a secret, sent with request configuration (model, messages, `max_tokens`, etc.).

### Card 2 — Stateless messages
**Q:** Does the Messages API remember a conversation if you only send the latest user sentence?
**A:** No. You must resend the **message history** (and tool blocks). Session state lives in your app.

### Card 3 — System vs messages
**Q:** Where do durable behavioral rules belong?
**A:** In the **system prompt** (separate parameter), not only in a one-off user message. User turns are for the task instance and untrusted inputs.

### Card 4 — Alternating turns
**Q:** What must you do after the model returns an assistant message (possibly with tool_use)?
**A:** Append that assistant content to `messages`, then add the next **user** or **tool_result** turn. Do not drop blocks.

### Card 5 — Temperature
**Q:** What does temperature change on a Messages call?
**A:** Sampling diversity of next-token generation. Not injection safety, not batch cost, not structured-output validity by itself.

### Card 6 — Streaming
**Q:** Why stream, and what extra client work does it create?
**A:** Faster perceived latency (incremental tokens). Client must assemble chunks, handle disconnects, and still produce a complete assistant message for the next turn.

### Card 7 — Structured data
**Q:** What is “structured data” in this course?
**A:** Constraining Claude’s output to a parseable format (e.g. JSON / schema). Must be **validated**; do not trust raw text that merely looks like JSON.

### Card 8 — Tool schema vs tool result
**Q:** Difference between tool schemas and tool results?
**A:** Schemas are **declarations** on the request (what the model may call). Results are **your** follow-up content blocks after you executed the tool.

### Card 9 — Message blocks
**Q:** Why does the course emphasize handling message blocks instead of a single string?
**A:** Content is a **list of blocks** (text, tool_use, tool_result, images, documents, thinking). String-only handling drops tools and multimodal parts.

### Card 10 — Fine-grained tool calling
**Q:** What problem does fine-grained tool calling address?
**A:** Coarse tool use lets the model pick any listed tool. Fine-grained controls **when/which** tools may be called more precisely.

### Card 11 — Web search vs internal REST
**Q:** Can the built-in web search tool replace an MCP/custom tool to your inventory API?
**A:** No. Built-in tools do **not** reach arbitrary internal REST APIs (sample 3). Use custom tools or an MCP server.

### Card 12 — Citations
**Q:** What must be true for citation generation to be meaningful?
**A:** The model needs the **source documents** in context (PDF/files/text). Citations attribute claims to provided material; they are not a search engine by themselves.

### Card 13 — Files API + code execution
**Q:** What pairing does the course title “Code execution and the Files API” imply?
**A:** Files API stores/retrieves files; code execution lets Claude **run code** against those files for analysis/transforms — not just describe them.

### Card 14 — Prompt caching rule of thumb
**Q:** What is the core rule of prompt caching?
**A:** Cache the **stable prefix**; put volatile user-specific content after it. Reordering or mutating early tokens kills the hit.

### Card 15 — Batch window and cost
**Q:** State the two facts the sample item uses about Message Batches API.
**A:** Processes large **asynchronous** workloads **within a 24-hour window** at **reduced cost**. For non-urgent high volume, prefer it over parallel realtime Messages.

### Card 16 — max_tokens vs batch
**Q:** Why is lowering `max_tokens` the wrong answer for an overnight 10k-doc cost problem?
**A:** It truncates output; it does not select the **batch vs realtime** cost/latency tradeoff the requirement asked for.

### Card 17 — Third-party vendors
**Q:** If you invoke Claude through a third-party vendor, what still applies?
**A:** Messages mechanics (messages, tools, streaming, etc.), **model pinning**, and your auth/secrets model. Vendor routing is not a different Claude.

### Card 18 — Extended thinking on the wire
**Q:** Is extended thinking a separate API product from Messages?
**A:** No. It is a **model option** on the Messages path (and related surfaces). You enable it when the task’s reasoning difficulty justifies extra tokens/latency.

---

## ABCD questions

**Q1.** A developer must process 10,000 documents overnight for a non-urgent analytics report. Cost is primary; results are due in the morning. Which approach best fits?
- A) Send every request synchronously through the Messages API in parallel to finish as quickly as possible
- B) Use the Message Batches API, which processes large asynchronous workloads within a 24-hour window at reduced cost
- C) Lower `max_tokens` on synchronous calls to minimize cost
- D) Switch to the smallest available model regardless of output quality

**Answer:** B  
**Why:** Official sample 1. Batches match latency-tolerant volume + reduced cost. Parallel sync (A) is still realtime pricing; (C) and (D) are the wrong levers.

**Q2.** After a `tool_use` block, the client sends only a new user string “here is the result: 42” with no tool_result block. What fails?
- A) Temperature scaling
- B) The Messages **block protocol**: the model expects **tool_result** content bound to the tool_use, in a properly appended turn
- C) Prompt caching prefixes
- D) The Batches 24-hour window

**Answer:** B  
**Why:** Course: handling message blocks and sending tool results. Tool calls are structured blocks, not ad-hoc chat paraphrases.

**Q3.** You need attributed answers from a 40-page policy PDF. Which feature set matches?
- A) Web search tool only
- B) PDF/document input **plus citations**
- C) Raise temperature and ask for URLs
- D) Message Batches API without the file

**Answer:** B  
**Why:** PDF support + citation generation are the named features for document-grounded attribution. Web search is a different built-in; batching does not add sources.

**Q4.** Interactive UX needs tokens on screen immediately; a second pipeline scores transcripts overnight. Which streaming/batch split is correct?
- A) Stream the overnight job; batch the interactive chat
- B) Stream (realtime Messages) for chat; Message Batches for overnight scoring
- C) Disable streaming everywhere so logs are identical
- D) Use third-party vendors only when streaming

**Answer:** B  
**Why:** Streaming is a realtime UX feature. Overnight scoring is the batch pattern. Vendors are orthogonal.

**Q5.** A large static tool list and system prompt sit *after* a unique 2k-token user dump. Cache hit rate is ~0. Fix?
- A) Move stable system + tools (and static docs) **before** the volatile user dump; cache-checkpoint the prefix
- B) Set temperature to 0
- C) Switch from streaming to non-streaming
- D) Enable citations

**Answer:** A  
**Why:** Caching rules are prefix-stability/order. Temperature, streaming, and citations do not create cache hits.

**Q6.** Fine-grained tool calling is best described as:
- A) Running many tools in Message Batches
- B) A control surface for **when/which** tools the model may invoke, tighter than “here are N tools”
- C) Replacing MCP resources
- D) The same as the text edit tool

**Answer:** B  
**Why:** The course lists fine-grained tool calling separately from multiple tools and from specific built-ins (text edit, web search).
