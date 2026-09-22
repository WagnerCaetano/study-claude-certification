# Cram sheet — Claude Certified Developer Foundations

Source: `../resource/resource.md` only. Official sample answers marked ★. If two options look right, pick the **control-plane** answer (who decides, who trusts, who pays).

## Domain weights (time = weight)

D2 Applications **33.1%** · D5 Models **16.8%** · D1 Agents **14.7%** · D6 Prompt/context **11.0%** · D8 Tools/MCP **10.6%** · D7 Security **8.1%** · D3 Claude Code **3.1%** · D4 Eval/debug **2.6%**

**4D (not a domain):** Delegation → agents · Description → prompts · Discernment → eval · Diligence → safety.

---

## ★ Three official items

1. Overnight, cost-primary, 10k docs, morning OK → **Message Batches API** (async, **24h window**, **reduced cost**). Not parallel realtime Messages. Not lower `max_tokens`. Not smallest model regardless of quality.
2. Summarize **user web pages** with hidden “ignore instructions / leak system prompt” → treat page as **untrusted**, **separate** from trusted instructions, **guardrails/hooks** so injection cannot fire sensitive tools. Not temperature. Not “please don’t.” Not bigger model (can be **more** injectable).
3. Internal inventory REST, reusable across apps, independent maintenance → **MCP server**. Not system-prompt logic. Not paste data every call. Not built-in tools (they **don’t** hit arbitrary internal REST).

---

## Models / tokens / cost (D5)

- Next-token, **non-deterministic**, **tokens** bound the window. Temperature = sampling, **not** security.
- **Opus** = quality / high cost-of-error · **Sonnet** = default · **Haiku** = cheap/fast/simple.
- **Fast** vs **extended thinking** vs **adaptive thinking** (check adaptive **support** per tier) vs **effort levels**.
- Zero-shot / single-shot / multi-shot = 0 / 1 / N examples.
- SDKs **wrap REST** (+ JSON, async, websockets as integration patterns).
- **Pin model versions** — releases **break** tool/JSON/refusal behavior.
- **Prompt caching** = stable **prefix** for cost/latency. **Cache check-pointing** = explicit boundaries so the suffix doesn’t bust the prefix.
- Agents cost **turns × tokens**, not one completion.

## Messages API (D2 mechanics)

- Stateless: **you** resend history. **System** ≠ user turn.
- Content = **blocks** (text, tool_use, tool_result, image, document, thinking). Echo assistant blocks; send **tool_result**, not a paraphrase.
- Stream = realtime UX. Batch ≠ stream.
- Structured output still needs **validation**.
- Features: **extended thinking**, **images**, **PDFs**, **citations** (need source docs), **Files API + code execution**, **web search**, **text edit**, **fine-grained tool calling**.
- Third-party vendors: still Messages mechanics + pinning + your keys.

## Prompt / context / output (D6)

- Clear + **specific**; **XML tags** = content boundaries; **examples** beat vague prose.
- System = durable policy. User/data = instance + **untrusted**. Sanitize input.
- Instruction **placement across components** (system, tools, CLAUDE.md, Skills, MCP prompts) — conflicts are bugs.
- **Bloat** vs **drift**. Fix with **tool-output pruning**, **compaction**, **subagent isolation**, session hygiene.
- **Defensive parse**. **Skeptical of confident** prose. Fluency ≠ truth.

## Tools / MCP (D8)

- Tool **description** is a prompt. Small, orthogonal tool-set. **Approval** before destructive calls. Client-side vs server-side execution.
- Harness = loop until stop.
- MCP primitives: **tools = model-controlled** · **resources = app-controlled** (direct URI vs **templated**) · **prompts = user-controlled**. Resources = read-only data + MIME.
- SDK: **decorators / type hints / Field**. **Server Inspector** = browser debug.
- **Sampling**: server asks **client** to call the LLM; **client pays**.
- Messages: **request/result** vs **notification** (progress/logging). **Bidirectional**.
- **Roots** = directory allow-list.
- Transports: **stdio** + **init handshake** (local) · **StreamableHTTP + SSE** + sessions · flags can kill **server-initiated** calls/streaming · **stateless HTTP** = **LB scale**, lose sticky/sampling.
- Chooser: built-in (no internal REST) · in-app custom tool (not shared) · **MCP** (shared live API) · Skill (procedure) · CLAUDE.md (always-on text) · hook (deterministic) · subagent (isolated).

## Agents / workflows / RAG (D1)

- **Workflow** = you graph the steps. **Agent** = model loop.
- **Chain** (A→B) · **Parallel** (independent fan-out) · **Route** (pick one specialist).
- Supervisor **delegates**; **subagents** execute in **isolated** context.
- Build: **Agent SDK** vs **custom harness** · **self-hosted vs Anthropic-hosted** · **hooks** for must-fire policy.
- Patterns: tool loop, sub-agents, **memory ≠ window**, context management.
- Frameworks named: **Strands, LangGraph, PydanticAI**.
- RAG: **chunk** → **embeddings** and/or **BM25 (lexical)** → **multi-index** → generate; **contextual retrieval**. Agentic search = iterative tools. Retrieved text = **untrusted**. **Inspect the environment**; don’t hallucinate state.

## Apps / config (D2 rest)

- Map business → **functional + infrastructure** (batch vs chat, VPC vs hosted, MCP vs tool).
- SDLC: prompts/tools/evals are **versioned artifacts**; operate + maintain through model breaks.
- SE: REST/JSON, async, git, PR **includes prompts/schemas**, small/large **refactors** need evals.
- Surfaces **don’t share instructions**: Claude Code / Desktop / claude.ai / API / SDKs.
- **CLAUDE.md** = prose instructions (hierarchy). **settings.json** = config (permissions, model, hooks). **Pin** models, prompts, **plugin dependencies**. Session hygiene. Schema stability. **No secrets in CLAUDE.md.**

## Claude Code / Skills (D3)

- Components: **Rules, Skills, Commands, Agents, Agent Memory**.
- **Headless** = CI/no TTY. **Auto-mode** = fewer permission stops. Streaming mode. Permission modes = least privilege.
- Skills: **SKILL.md frontmatter**; **description triggers**; **progressive disclosure**; **allowed-tools**; scripts **don’t eat context**. Share: git → **plugins** → **enterprise managed settings**. Debug: no trigger / **priority conflict** / runtime.
- **Computer Use** = UI automation. Claude Code = repo/CLI agent.
- Verify **unsupervised** runs (Actions, review bots). Don’t trust confident patches.

## Eval / debug (D4)

- Workflow: spec → **dataset** → run pinned → **grade** → iterate.
- **Code grade** = machine-checkable. **Model grade** = rubric/fuzzy. Hybrid is normal.
- Isolate: **integration** (HTTP/auth/dropped blocks/parser) vs **model output** (200 + legal transcript, wrong behavior).
- Recovery **matches type**: retry timeout · fail-closed injection · prompt/eval for wrong answer — not infinite retry of a prompt bug.

## Security (D7)

- Isolate **untrusted** (RAG, web, uploads). Layer: bounds + tiny tool-set + **hooks/approval** + output filter + **IAM**.
- Jailbreak = user bypass. Injection = **data** hijack.
- Model text is **not** authorization. Keys in secret manager, never frontend/git/CLAUDE.md. Monitor privileged tool access. PII in prompts **and logs**.

---

## 20-second chooser

| Need | Pick |
|---|---|
| Known steps, SLA, audit | Workflow (chain/parallel/route) |
| Unknown steps, stop-when-enough | Agent + hooks |
| Cheap overnight volume | Message Batches (24h) |
| Interactive tokens | Realtime Messages + stream |
| Live internal API, many apps | MCP tools |
| Playbook, not an API | Skill |
| Always-on repo law | CLAUDE.md (short) |
| Must never happen | Hook + IAM, not a sentence |
| Hard reasoning | Extended/adaptive thinking; maybe Opus |
| High QPS classify | Haiku / fast |
| Keyword + semantic search | BM25 + embeddings, multi-index |
| “It sounded sure” | Validate / eval / don’t do the side effect |
