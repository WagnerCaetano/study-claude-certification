# All flashcards — Foundations fast track

Drill file. Cover **A**. Tag = module. Misses → reopen that module brief.

Protocol: say the answer out loud, then reveal. End of a module: retry misses immediately.

---

## M01 — Model selection and optimization (D5 16.8%)

### Card M01-1 — Next-token generation
**Q:** What does “next-token generation” imply for Claude application design?
**A:** The model samples one token at a time from a distribution; outputs are **non-deterministic**, billed and bounded in **tokens**, and quality depends on context + sampling — not on a stored answer key.

### Card M01-2 — Context window
**Q:** What happens conceptually when prompt + output exceed the context window?
**A:** You run out of room for instructions, history, tool results, or the answer. Manage with truncation strategy, compaction, pruning, or subagent isolation (Domain 6) — do not assume infinite memory.

### Card M01-3 — Sampling / temperature
**Q:** What does temperature control, and what does it *not* guarantee?
**A:** It controls sampling diversity. Low temperature → more concentrated / repeatable; high → more varied. It does **not** guarantee identical outputs, and it is **not** a prompt-injection defense.

### Card M01-4 — Fast vs extended vs adaptive thinking
**Q:** When do you choose fast mode, extended thinking, or adaptive thinking?
**A:** Fast: latency-sensitive easy work. Extended: hard multi-step reasoning worth extra cost. Adaptive: mixed easy/hard traffic so the model spends thinking only when needed. Adaptive **support** differs by model tier — check it when selecting Opus/Sonnet/Haiku.

### Card M01-5 — Effort levels
**Q:** What are effort levels for?
**A:** A dial on how hard the model tries for a given model family — trade quality against cost/latency without necessarily switching Opus/Sonnet/Haiku.

### Card M01-6 — Opus vs Sonnet vs Haiku
**Q:** One-line selection rule for the three Claude tiers?
**A:** Opus = max quality / high cost-of-error. Sonnet = default quality-latency-cost balance. Haiku = min latency/cost, simpler tasks. Always state the **quality/latency/cost** tradeoff.

### Card M01-7 — Breaking changes
**Q:** Why does the blueprint mention breaking behavior changes across model releases?
**A:** New models can change tool use, formatting, or refusals. **Pin model versions**; evaluate before upgrading. “Latest” is not a production strategy.

### Card M01-8 — Zero / single / multi-shot
**Q:** Define zero-shot, single-shot, and multi-shot.
**A:** Zero-shot: instructions only. Single-shot: one example. Multi-shot: multiple examples. Examples teach **format and edge cases** cheaper than prose.

### Card M01-9 — SDK vs REST
**Q:** What is the relationship between Anthropic SDKs and the API?
**A:** SDKs **wrap REST**. You still need JSON, HTTP semantics, auth headers/keys, and error handling. SDK convenience is not a different product.

### Card M01-10 — Async and streaming
**Q:** Why does Domain 5 list asynchronous programming next to LLM fundamentals?
**A:** Streaming, concurrent document jobs, and multi-round tool calls are async I/O problems. Blocking request-per-request designs will miss latency and throughput targets.

### Card M01-11 — Websockets vs REST
**Q:** Where do websockets fit relative to the Messages REST API?
**A:** REST = request/response (including HTTP streaming). Websockets = persistent bidirectional channel for some app architectures. Do not confuse with MCP stdio/SSE (Domain 8).

### Card M01-12 — Prompt caching vs check-pointing
**Q:** Distinguish prompt caching from cache check-pointing.
**A:** Prompt caching reuses a stable prompt **prefix** to cut cost/latency. Check-pointing is placing **cache boundaries** so volatile suffixes do not invalidate the cached prefix. Both are cost tools; they are not the same step.

### Card M01-13 — Agent cost multiplier
**Q:** Why do agents blow cost models that were tuned on single Messages calls?
**A:** Each tool round-trip adds input (full history + tool results) and output tokens, possibly with thinking. Cost is **turns × tokens**, not one completion.

### Card M01-14 — Delegation vs model tier
**Q:** A task is safety-critical and must be exact. Is “use Opus” the right first move?
**A:** First **Delegate** correctly: if a program can do it deterministically, do not use a model. If you need a model, pick tier/thinking for residual judgment — then **validate** output (Domain 6/4).

### Card M01-15 — Capabilities vs limitations
**Q:** Name two generative-AI limitations the fluency section expects you to remember.
**A:** No inherent live/private data access without tools/RAG; finite context; confident errors; susceptibility to untrusted instructions. Capabilities (language, vision, tools, thinking) do not cancel those.

### Card M01-16 — Overnight cost job vs smaller model
**Q:** For 10k non-urgent overnight docs, is switching to Haiku the primary cost optimization?
**A:** No. Sample item: use **Message Batches API** (24h, reduced cost). Blindly shrinking the model ignores the realtime-vs-batch tradeoff and can destroy quality.

---

## M02 — Claude API mechanics (D2 API 6.8%)

### Card M02-1 — API key
**Q:** What is required to call the Anthropic API?
**A:** An API key, managed as a secret, sent with request configuration (model, messages, `max_tokens`, etc.).

### Card M02-2 — Stateless messages
**Q:** Does the Messages API remember a conversation if you only send the latest user sentence?
**A:** No. You must resend the **message history** (and tool blocks). Session state lives in your app.

### Card M02-3 — System vs messages
**Q:** Where do durable behavioral rules belong?
**A:** In the **system prompt** (separate parameter), not only in a one-off user message. User turns are for the task instance and untrusted inputs.

### Card M02-4 — Alternating turns
**Q:** What must you do after the model returns an assistant message (possibly with tool_use)?
**A:** Append that assistant content to `messages`, then add the next **user** or **tool_result** turn. Do not drop blocks.

### Card M02-5 — Temperature
**Q:** What does temperature change on a Messages call?
**A:** Sampling diversity of next-token generation. Not injection safety, not batch cost, not structured-output validity by itself.

### Card M02-6 — Streaming
**Q:** Why stream, and what extra client work does it create?
**A:** Faster perceived latency (incremental tokens). Client must assemble chunks, handle disconnects, and still produce a complete assistant message for the next turn.

### Card M02-7 — Structured data
**Q:** What is “structured data” in this course?
**A:** Constraining Claude’s output to a parseable format (e.g. JSON / schema). Must be **validated**; do not trust raw text that merely looks like JSON.

### Card M02-8 — Tool schema vs tool result
**Q:** Difference between tool schemas and tool results?
**A:** Schemas are **declarations** on the request (what the model may call). Results are **your** follow-up content blocks after you executed the tool.

### Card M02-9 — Message blocks
**Q:** Why does the course emphasize handling message blocks instead of a single string?
**A:** Content is a **list of blocks** (text, tool_use, tool_result, images, documents, thinking). String-only handling drops tools and multimodal parts.

### Card M02-10 — Fine-grained tool calling
**Q:** What problem does fine-grained tool calling address?
**A:** Coarse tool use lets the model pick any listed tool. Fine-grained controls **when/which** tools may be called more precisely.

### Card M02-11 — Web search vs internal REST
**Q:** Can the built-in web search tool replace an MCP/custom tool to your inventory API?
**A:** No. Built-in tools do **not** reach arbitrary internal REST APIs (sample 3). Use custom tools or an MCP server.

### Card M02-12 — Citations
**Q:** What must be true for citation generation to be meaningful?
**A:** The model needs the **source documents** in context (PDF/files/text). Citations attribute claims to provided material; they are not a search engine by themselves.

### Card M02-13 — Files API + code execution
**Q:** What pairing does the course title “Code execution and the Files API” imply?
**A:** Files API stores/retrieves files; code execution lets Claude **run code** against those files for analysis/transforms — not just describe them.

### Card M02-14 — Prompt caching rule of thumb
**Q:** What is the core rule of prompt caching?
**A:** Cache the **stable prefix**; put volatile user-specific content after it. Reordering or mutating early tokens kills the hit.

### Card M02-15 — Batch window and cost
**Q:** State the two facts the sample item uses about Message Batches API.
**A:** Processes large **asynchronous** workloads **within a 24-hour window** at **reduced cost**. For non-urgent high volume, prefer it over parallel realtime Messages.

### Card M02-16 — max_tokens vs batch
**Q:** Why is lowering `max_tokens` the wrong answer for an overnight 10k-doc cost problem?
**A:** It truncates output; it does not select the **batch vs realtime** cost/latency tradeoff the requirement asked for.

### Card M02-17 — Third-party vendors
**Q:** If you invoke Claude through a third-party vendor, what still applies?
**A:** Messages mechanics (messages, tools, streaming, etc.), **model pinning**, and your auth/secrets model. Vendor routing is not a different Claude.

### Card M02-18 — Extended thinking on the wire
**Q:** Is extended thinking a separate API product from Messages?
**A:** No. It is a **model option** on the Messages path (and related surfaces). You enable it when the task’s reasoning difficulty justifies extra tokens/latency.

---

## M03 — Prompt and context engineering (D6 11.0%)

### Card M03-1 — Description
**Q:** In the 4D framework, what is Description?
**A:** Communicating task, context, constraints, and success criteria to the model. It is the prompt/spec skill.

### Card M03-2 — Description–Discernment loop
**Q:** What is the Description–Discernment loop?
**A:** Describe → inspect output → adjust the description/tools/context → repeat. Prompting is iterative, not one-and-done.

### Card M03-3 — Clear vs specific
**Q:** Difference between “clear and direct” and “being specific”?
**A:** Clear/direct = unambiguous task statement. Specific = measurable constraints (format, counts, sources, what not to do). You need both.

### Card M03-4 — XML tags
**Q:** Why wrap retrieved documents in XML (or similar) tags?
**A:** **Content boundaries**: the model can separate *instructions* from *data*. Critical against prompt injection and against mixed context.

### Card M03-5 — Examples
**Q:** When are examples (single/multi-shot) better than more instructions?
**A:** When format, tone, or edge-case handling is cheaper to **show** than to specify exhaustively.

### Card M03-6 — System vs user
**Q:** What belongs in system vs user?
**A:** System: stable rules/persona/output contract. User: the actual query and instance/untrusted data. Do not put untrusted web pages in system.

### Card M03-7 — Placement across components
**Q:** A tool description says “always delete” and the system prompt says “never delete.” What failed?
**A:** **Instruction placement across components.** Tool text is part of the prompt. Resolve ownership; don’t duplicate conflicting rules.

### Card M03-8 — Input sanitization
**Q:** What is input sanitization in this domain?
**A:** Treating external/user/retrieved text as untrusted data: isolate it, don’t promote it into instructions, strip injection-like payloads before privileged actions.

### Card M03-9 — Context bloat vs drift
**Q:** Distinguish context bloat and context drift.
**A:** Bloat = too many tokens (noise, huge tool output). Drift = goals/constraints **change or fade** as the thread grows. Prune/compact for bloat; re-assert or isolate for drift.

### Card M03-10 — Tool output pruning
**Q:** Why prune tool output before the next model call?
**A:** Raw tool dumps fill the window, raise cost, and hide instructions. Keep the **minimum** the next step needs.

### Card M03-11 — Compaction
**Q:** What is compaction?
**A:** Compressing conversation/state into a shorter summary so the window stays usable without keeping every token of history.

### Card M03-12 — Isolation via subagents
**Q:** How do subagents help context engineering?
**A:** They run a subtask in an **isolated** context (then return a result). Parent context stays clean; bloat/drift don’t leak as easily.

### Card M03-13 — Defensive parsing
**Q:** What does defensive parsing require that “just ask for JSON” does not?
**A:** Validate schema, handle fences/truncation/extra text, fail closed before side effects. Confident JSON-shaped prose is still untrusted.

### Card M03-14 — Skepticism
**Q:** Why does the blueprint say to be skeptical of confident output?
**A:** Next-token models produce fluent, high-certainty phrasing even when wrong. Discernment + validators, not vibes.

---

## M04 — Tools and MCPs (D8 10.6%)

### Card M04-1 — Tool description
**Q:** What is the highest-leverage part of a tool schema besides the JSON parameters?
**A:** The **description** (and Field descriptions): when to call, when not to, side effects. It is prompt engineering.

### Card M04-2 — Harness dispatch
**Q:** What is agentic harness dispatch?
**A:** The runtime loop that sends messages, executes tool_use, returns tool_result, and repeats until stop — not a single completion.

### Card M04-3 — Client-side vs server-side tools
**Q:** Distinguish client-side vs server-side tools.
**A:** Client-side: executed by the host/app (local side effects, visible approval). Server-side: executed remotely/by the platform. Trust, data residency, and approval differ.

### Card M04-4 — Approval patterns
**Q:** Why mention approval patterns next to tools?
**A:** Destructive/privileged tools should not auto-fire on model request. Human or policy **approval** (often **hooks**) sits between tool_use and execution.

### Card M04-5 — Tool-set construction
**Q:** What is a tool-set construction best practice from the blueprint?
**A:** Small, non-overlapping tools with precise descriptions. A large overlapping set causes wrong calls and context bloat.

### Card M04-6 — Built-in vs internal REST
**Q:** Why is “use a built-in tool” wrong for an internal inventory API?
**A:** Built-ins do not reach arbitrary internal REST. You need **custom tools** or an **MCP server**.

### Card M04-7 — MCP burden shift
**Q:** What burden does MCP shift, and to where?
**A:** Tool **definition and execution** move from each app to **specialized MCP servers**. Hosts speak MCP as clients.

### Card M04-8 — Three primitives and controllers
**Q:** Who controls tools vs resources vs prompts?
**A:** Tools = **model-controlled**. Resources = **app-controlled**. Prompts = **user-controlled**.

### Card M04-9 — Direct vs templated resources
**Q:** Difference between direct and templated MCP resources?
**A:** Direct = static URI. Templated = parameterized URI. Both are read-only data, not tools.

### Card M04-10 — MIME types
**Q:** Why does resource reading mention MIME types?
**A:** Clients must handle **JSON vs text** (and similar) correctly when reading resources.

### Card M04-11 — Decorators vs JSON schema
**Q:** How does the Python MCP SDK typically define tools?
**A:** **Decorators + type hints + Field descriptions**, instead of hand-written JSON schemas.

### Card M04-12 — Server Inspector
**Q:** What is the MCP Server Inspector?
**A:** A **browser-based** inspector to test/debug server tools/resources/prompts without building the full client first.

### Card M04-13 — Sampling
**Q:** What is MCP sampling and who pays?
**A:** The **server requests an LLM call through the client**. **Cost and complexity move to the client/host** that already has model access.

### Card M04-14 — Request vs notification
**Q:** MCP request-result vs notification?
**A:** Request expects a **result**. Notifications are one-way (progress, logging). Do not block a notify as if it were a call.

### Card M04-15 — Roots
**Q:** What are MCP roots?
**A:** A **permission boundary** listing directories the server may access — security + discovery. Least privilege for files.

### Card M04-16 — stdio handshake
**Q:** What must happen on stdio transport before normal traffic?
**A:** The **initialization handshake**. Then messages on stdin/stdout.

### Card M04-17 — StreamableHTTP
**Q:** What enables server-to-client traffic in StreamableHTTP MCP?
**A:** **SSE (Server-Sent Events)**, plus session management (often dual connections). Config flags can **disable server-initiated requests/streaming**.

### Card M04-18 — Stateless HTTP scaling
**Q:** When do you choose stateless HTTP for MCP?
**A:** When you need **horizontal scaling** with **load balancers**. Tradeoff: lose sticky/session features (often sampling / server-initiated calls).

### Card M04-19 — Skills vs MCP vs tools
**Q:** Internal live inventory, many Claude apps, independent lifecycle — Skill, custom-in-app tool, or MCP?
**A:** **MCP server**. Skills are procedural knowledge; in-app tools are not shared; MCP is reusable and independently maintained.

### Card M04-20 — Prompts primitive
**Q:** Is an MCP prompt automatically executed by the model?
**A:** No. Prompts are **user/host-controlled** templates injected into context (e.g. user picks “Format document”).

---

## M05 — Agents and workflows (D1 14.7%)

### Card M05-1 — Workflow vs agent
**Q:** What is the decision criterion for workflow vs agent?
**A:** If **you** can predefine the step graph, use a **workflow**. If the **model** must choose tools/steps at runtime in a loop, use an **agent**.

### Card M05-2 — Why not always agent
**Q:** Why not make every pipeline an agent?
**A:** Agents add cost (multi-turn tokens), non-determinism, and a larger tool-abuse/injection surface. Workflows are better for known, auditable procedures.

### Card M05-3 — Chaining
**Q:** Define a chaining workflow.
**A:** Sequential stages: each step’s output is the next step’s input. One path, ordered dependencies.

### Card M05-4 — Parallelization
**Q:** When is parallelization valid?
**A:** When subtasks are **independent** (no data dependence). Fan-out then merge. If B needs A, chain instead.

### Card M05-5 — Routing
**Q:** What does a routing workflow do?
**A:** A router (often a cheap model) **selects** a specialist prompt/model/tool-graph. It is path **selection**, not by default “run all specialists.”

### Card M05-6 — Supervisor vs subagent
**Q:** Manager/supervisor vs subagent?
**A:** Supervisor **plans and delegates**. Subagents **execute isolated subtasks** with their own context/tools and return results.

### Card M05-7 — Why subagents improve execution
**Q:** How do subagents improve task execution (architecture skill)?
**A:** Isolation: focused tools/prompts, less bloat/drift, parallel specialist work, narrower failure domains.

### Card M05-8 — Agent SDK vs custom harness
**Q:** Claude Agent SDK vs a custom agent loop?
**A:** Both construct agents. SDK = supported toolkit. Custom harness = you own Messages + tool dispatch. Managed hosting is a separate axis (self vs Anthropic-hosted).

### Card M05-9 — Self-hosted vs Anthropic-hosted
**Q:** What is the real tradeoff in managed agent deployment models?
**A:** **Self-hosted:** control, integration with your VPC/compliance, more ops. **Anthropic-hosted:** less ops, platform constraints. Choose from requirements, not branding.

### Card M05-10 — Hooks in agents
**Q:** Why does agent construction list hooks?
**A:** Hooks run **deterministic** code on lifecycle events (e.g. block a tool). Policy that must not depend on the model “remembering.”

### Card M05-11 — Memory vs context window
**Q:** Is agent memory the same as the context window?
**A:** No. The window is the tokens on **this** call. Memory is **persisted** state you choose to reload. Unmanaged history is bloat, not memory.

### Card M05-12 — Named frameworks
**Q:** Name the agentic abstraction frameworks in the blueprint.
**A:** **Strands**, **LangGraph**, **PydanticAI** — used to build agents/workflows for multi-step tasks.

### Card M05-13 — BM25 vs embeddings
**Q:** BM25 vs embedding search in RAG?
**A:** BM25 = **lexical** keyword matching. Embeddings = **semantic** similarity. A **multi-index** pipeline often uses both.

### Card M05-14 — RAG vs agentic search
**Q:** Single-shot RAG vs agentic search?
**A:** RAG: retrieve then generate in a **fixed** flow (workflow). Agentic search: model **iteratively** queries/tools. Retrieved docs remain untrusted data.

### Card M05-15 — Environment inspection
**Q:** What is “environment inspection” next to agents and tools?
**A:** The agent should **observe** the actual environment (repo, files, APIs) rather than hallucinate state. Tools exist to look, then act.

---

## M06 — Applications and integration (D2 remainder 26.3%)

### Card M06-1 — Functional vs infrastructure
**Q:** Give one functional and one infrastructure requirement derived from “overnight 10k docs, cheapest, morning deadline.”
**A:** Functional: batch analytics quality bar, no interactive UX. Infrastructure: **Message Batches**, async workers, cost telemetry — not a GPU chat cluster.

### Card M06-2 — Feature matching
**Q:** Business says “answers must show which PDF paragraph they used.” Which design choice?
**A:** PDF/document input + **citations**, plus those files in context. Not temperature, not Haiku-only.

### Card M06-3 — SDLC and prompts
**Q:** Where do prompts sit in the systems life cycle?
**A:** They are **versioned artifacts**: developed, reviewed, deployed, operated (monitored), maintained (eval on model change). Not one-off chat.

### Card M06-4 — REST/JSON
**Q:** Why does a Claude exam test REST and JSON?
**A:** The API is a REST JSON interface; SDKs wrap it. Tool schemas, MCP messages, and structured output are JSON. You must parse and version them.

### Card M06-5 — Prompts in code review
**Q:** What should a PR review include for a Claude app besides Python diffs?
**A:** System prompts, tool descriptions/schemas, CLAUDE.md, Skills, eval datasets, permission/hook config — they change runtime behavior.

### Card M06-6 — Interfaces
**Q:** Why does “how Claude interprets instructions across Claude Code, Desktop, claude.ai, API, SDKs” matter?
**A:** Each surface has **different instruction channels**. A rule in CLAUDE.md does not exist on a raw API call unless you copy it into `system`.

### Card M06-7 — Content boundaries
**Q:** What are content boundaries in application design?
**A:** Separating trusted instructions from untrusted files/user/web content (tags, channels, sanitization) so data cannot become policy.

### Card M06-8 — Schema design
**Q:** What is schema design for Claude apps?
**A:** Stable contracts for **tool inputs/outputs** and structured responses. Changing names without versioning breaks parsers and the model’s few-shot memory.

### Card M06-9 — Session hygiene
**Q:** What is session hygiene?
**A:** Managing history so the window stays on-task: trim, compact, new session, prune tool output. Don’t unbounded-append.

### Card M06-10 — CLAUDE.md vs settings.json
**Q:** CLAUDE.md vs settings.json?
**A:** CLAUDE.md = **instruction/memory prose** for the agent. settings.json = **configuration** (permissions, model, hooks). Don’t put IAM policy only in markdown, or essays in JSON config.

### Card M06-11 — Pinning
**Q:** What two things does Domain 2 tell you to pin?
**A:** **Model versions** and **prompt/plugin versions** (plus plugin dependencies). Unpinned “latest” is a production incident.

### Card M06-12 — Plugins
**Q:** What is plugin management in Claude application design?
**A:** Plugins package Skills/hooks/MCP/commands. Treat them as **dependencies**: review, version, least privilege — they execute in the agent environment.

---

## M07 — Claude Code (D3 3.1%)

### Card M07-1 — Component set
**Q:** List Claude Code’s named core components.
**A:** **Rules, Skills, Commands, Agents, Agent Memory.**

### Card M07-2 — Headless vs auto-mode
**Q:** Headless mode vs auto-mode?
**A:** Headless = **no interactive TTY** (CI/routines). Auto-mode = **fewer permission pauses** (higher autonomy). You can have headless with tight permissions.

### Card M07-3 — CLAUDE.md hierarchy
**Q:** Why does “CLAUDE.md hierarchy” matter?
**A:** User vs project vs org/enterprise layers combine. Put repo law in **project**; personal prefs in **user**; org policy in **enterprise managed** settings — don’t fight the wrong layer.

### Card M07-4 — settings.json
**Q:** What belongs in settings.json rather than CLAUDE.md?
**A:** **Configuration**: permission mode, hook registration, model pin. Not long-form coding standards (those are CLAUDE.md/Skills).

### Card M07-5 — Skill trigger
**Q:** What makes a Skill fire?
**A:** A **description** that matches the task (frontmatter). Bad descriptions → “skill won’t trigger.”

### Card M07-6 — Progressive disclosure
**Q:** Why progressive disclosure in Skills?
**A:** Keep the **context window efficient**: load the full SKILL body (and resources) **when matched**, not every Skill in the org on every turn.

### Card M07-7 — allowed-tools
**Q:** What does `allowed-tools` do on a Skill?
**A:** Restricts tool access for that Skill — least privilege so a formatting skill cannot hit prod APIs.

### Card M07-8 — Scripts vs context
**Q:** What is special about Skill scripts in the curriculum?
**A:** They **execute without consuming context** the way pasting the script would. Logic stays out of the window.

### Card M07-9 — Skills vs CLAUDE.md vs hooks vs MCP
**Q:** One-line chooser?
**A:** Always-on norms → CLAUDE.md. Procedural playbook on match → Skill. Must-never-fail policy → **hook**. Live shared integration → **MCP**. Isolated expert → **subagent**.

### Card M07-10 — Sharing Skills
**Q:** How do you distribute Skills?
**A:** Commit to a **repo**, bundle as **plugins**, push org-wide with **enterprise managed settings**.

### Card M07-11 — Skill troubleshooting
**Q:** Three Skill failure classes in the course?
**A:** **Won’t trigger** (description), **priority conflicts**, **runtime errors**.

### Card M07-12 — Hooks vs model memory
**Q:** Why use a hook to block destructive bash instead of a CLAUDE.md sentence?
**A:** Hooks are **deterministic**. CLAUDE.md is a prompt — ignorable, injectable, easy to lose in long sessions.

### Card M07-13 — Unsupervised verification
**Q:** What does “verifying unsupervised runs” require?
**A:** Tests, review, traces — **do not** treat headless/CI agent output as correct because it sounded confident.

### Card M07-14 — Computer Use vs Claude Code
**Q:** Computer Use vs Claude Code?
**A:** Computer Use = **UI automation** (screen/desktop). Claude Code = **development agent** in the repo/CLI. Different Anthropic apps; both may use MCP.

### Card M07-15 — Custom slash commands
**Q:** Built-in vs custom slash commands?
**A:** Built-in = product features. Custom = team-defined shortcuts/workflows in the repo/plugin.

---

## M08 — Eval, testing, and debugging (D4 2.6%)

### Card M08-1 — Eval workflow order
**Q:** Typical prompt-eval workflow?
**A:** Spec → **test dataset** → run (pinned model/prompt) → **grade** (code and/or model) → iterate one change at a time.

### Card M08-2 — Dataset
**Q:** What makes a test dataset useful?
**A:** Realistic inputs **and** a grading target (label or checker). Include edges. Generated sets still need human/spec audit.

### Card M08-3 — Code-based grading
**Q:** When is code-based grading the right grader?
**A:** When correctness is **machine-checkable**: schema, exact fields, allowed enums, “tool X was called,” unit tests.

### Card M08-4 — Model-based grading
**Q:** When is model-based grading warranted?
**A:** When the spec is **rubric/quality** (coherence, tone, completeness) that a program cannot cheaply score. Pin and eval the judge too.

### Card M08-5 — Combined grading
**Q:** Sensible hybrid?
**A:** Code validates structure/safety constraints; model grades remaining subjective quality.

### Card M08-6 — Origin isolation
**Q:** First question when debugging a Claude feature?
**A:** Is this **integration** (transport, auth, blocks, our parser) or **model output** (200 + legal transcript, wrong behavior)?

### Card M08-7 — Traces
**Q:** What must a trace contain to isolate failure modes?
**A:** Ordered turns: prompts/versions, **content blocks**, tool results, usage, errors. Not just the final user-visible string.

### Card M08-8 — Recovery match
**Q:** Match recovery to error type: timeout vs bad JSON vs injection vs wrong answer.
**A:** Timeout → retry/backoff. Bad JSON → defensive parse/repair or schema tighten. Injection → fail closed/guardrail. Wrong answer → prompt/model/eval, not blind retry.

### Card M08-9 — Discernment
**Q:** How does 4D Discernment show up in this domain?
**A:** Systematic judgment of outputs via evals and traces — not “it sounded confident.”

### Card M08-10 — Integration masquerading as model
**Q:** Client dropped `tool_result` and the model “hallucinated” the API. Origin?
**A:** **Integration layer** (broken tool protocol). The model never got the result. Fix the client, then re-eval.

---

## M09 — Security and safety (D7 8.1%)

### Card M09-1 — Prompt injection
**Q:** Define prompt injection in this exam’s terms.
**A:** Untrusted content (page, file, user) tries to **override trusted instructions** or trigger tools. Mitigate by isolation + guardrails, not by asking nicely.

### Card M09-2 — Sample 2 mitigation
**Q:** Most effective mitigation when summarizing user-submitted pages that may contain hidden instructions?
**A:** Treat retrieved content as **untrusted**, keep it **out of** the instruction channel, use **guardrails/hooks** so it cannot cause sensitive actions.

### Card M09-3 — Temperature
**Q:** Why is raising temperature not an injection defense?
**A:** Temperature is a **sampler**. It does not enforce trust boundaries or tool policy.

### Card M09-4 — Bigger model
**Q:** Why might a more instruction-following model be *worse* under injection?
**A:** It may **follow the injected instructions more reliably**. Capability ≠ security.

### Card M09-5 — Polite system line
**Q:** “Please do not include malicious instructions” in the system prompt — why insufficient?
**A:** It is **not an enforceable control**. Attackers/pages don’t comply. Need isolation + least privilege + hooks.

### Card M09-6 — Jailbreak vs injection
**Q:** Jailbreak vs prompt injection?
**A:** Jailbreak: **user** tries to bypass policy. Injection: **untrusted data** (often third-party) hijacks the model. Overlap in mitigations; injection is especially RAG/web-tool flavored.

### Card M09-7 — Least privilege tools
**Q:** Default tool-set rule for safety?
**A:** Only tools required for the task; no prod-destroying APIs on a summarizer. Approval on the rest.

### Card M09-8 — Guardrail layering
**Q:** What is guardrail layering?
**A:** Multiple independent controls (prompt boundaries, tool restriction, hooks/approvals, output filters, IAM) so one ignored sentence doesn’t equal a breach.

### Card M09-9 — Hooks for safety
**Q:** Why hooks for destructive actions?
**A:** They run **deterministically** and can **prevent** the action regardless of what the model or injected text requested.

### Card M09-10 — Secrets
**Q:** Where do API keys not go?
**A:** Git, CLAUDE.md, user-facing prompts, mobile/web frontends, RAG indexes. Use secret managers and env-specific IAM.

### Card M09-11 — AuthN vs the model
**Q:** Can the model’s text authorize a refund?
**A:** No. **Authentication/authorization** are application IAM. The model may *propose*; your backend **enforces**.

### Card M09-12 — PII / leakage
**Q:** Two leakage paths to plan for?
**A:** Model **echoes** secrets/PII in answers; **logs/traces** store prompts and tool dumps. Minimize, mask, retain less.

### Card M09-13 — Untrusted RAG
**Q:** How should RAG chunks be treated in the prompt?
**A:** As **data** in tagged/user channels — never merged into system policy; never allowed to select privileged tools without a guardrail.
