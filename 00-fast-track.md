# Claude Certified Developer – Foundations: Fast Study Track

Source of truth: `resource/resource.md` (exam blueprint + course map). Nothing here is guaranteed by Anthropic to pass the exam. Grind the blueprint skills; use this track to memorize distinctions.

Exam items are written against **8 domains**. Weights below are from the exam guide in `resource/resource.md`.

| Domain | Weight | This track |
|---|---|---|
| D2 Applications and Integration | **33.1%** | `02` + `06` |
| D5 Model Selection and Optimization | **16.8%** | `01` |
| D1 Agents and Workflows | **14.7%** | `05` |
| D6 Prompt and Context Engineering | **11.0%** | `03` |
| D8 Tools and MCPs | **10.6%** | `04` |
| D7 Security and Safety | **8.1%** | `09` |
| D3 Claude Code | **3.1%** | `07` |
| D4 Eval, Testing, and Debugging | **2.6%** | `08` |

`resource/resource.md` also contains an **AI Fluency 4D** intro (Delegation, Description, Discernment, Diligence). It is not a scored domain. Map it: Delegation → agents/workflows; Description → prompting; Discernment → eval; Diligence → security/safety.

---

## How to use this track

Three artifacts, three jobs:

1. **Module files** (`study-track/modules/NN-….md`) — learn. Read the brief, then cards, then ABCD. Do not skip the brief; cards assume it.
2. **`study-track/flashcards-all.md`** — drill. Rapid Q/A with module tags. Cover the answer; say it out loud; flip.
3. **`study-track/cram-sheet.md`** — last pass. Highest-yield facts and exam traps only.

**Flashcard protocol:** read **Q**, force an answer, then **A**. Miss → mark the card, reread that subsection of the module, retry the same card immediately, then again at the end of the module. Do not restudy cards you already hit.

**ABCD protocol:** answer before looking. If wrong, write *which distinction you missed* (not just the letter). Those distinctions are the exam.

Do not invent extra API limits, model SKUs, or error codes beyond what `resource/resource.md` supports. If a number is not in this track, it is not a required memorization item here.

---

## Pass order (pedagogical, not weight order)

API and models first; tools and agents next; Claude Code / eval last; security as its own pass so it does not get buried.

| Pass | Timebox | What | Goal |
|---|---|---|---|
| **0 — Map** | 15 min | This file + `study-track/cram-sheet.md` once | Know domain weights and the trap list |
| **1 — Core API** | 90 min | `01` then `02` | Models, tokens, Messages API, streaming, batch, caching, vision/thinking/files |
| **2 — Steer the model** | 70 min | `03` then `08` | Prompts, context, structured output, eval/debug |
| **3 — Tools & agents** | 100 min | `04` then `05` | Tools, MCP primitives/transports, workflows vs agents, RAG |
| **4 — Build & operate** | 80 min | `06` then `07` | App design, SDLC, config, Claude Code, Skills, hooks |
| **5 — Safety** | 40 min | `09` | Injection, guardrails, hooks, keys |
| **6 — Drill** | 60–90 min | `study-track/flashcards-all.md` | Cover every card; restudy misses by module |
| **7 — Cram** | 25 min | `study-track/cram-sheet.md` + missed ABCD | Night-before / hour-before |

**Total: ~8–9 hours.** If you have 4 hours, do Pass 0, then 01/02/04/05/09 briefs + cram sheet + flashcards tagged D1, D2-API, D5, D8, D7 (those are ~83% of the exam if you include D2 application-design at skim depth).

### Weight-based time (inside each pass)

Spend time proportional to weight. Do **not** over-invest in D3/D4 until D2/D5/D1 are solid.

- D2 (`02` + `06`): ~1/3 of study time
- D5 (`01`): ~1/6
- D1 (`05`): ~1/7
- D6 (`03`) and D8 (`04`): ~1/10 each
- D7 (`09`): ~1/12
- D3 + D4 (`07` + `08`): remaining ~6% — short, but the distinctions are sharp

---

## Module index

| File | Exam mapping | Course chunks from `resource/resource.md` |
|---|---|---|
| [01-model-selection-and-optimization.md](study-track/modules/01-model-selection-and-optimization.md) | D5 16.8% | GenAI fundamentals; Opus/Sonnet/Haiku; thinking modes; cost/caching |
| [02-claude-api-mechanics.md](study-track/modules/02-claude-api-mechanics.md) | D2 Claude API Mechanics 6.8% | Accessing the API; messages; streaming; structured data; thinking; images; PDFs; citations; caching; Files API; code execution; batch |
| [03-prompt-and-context-engineering.md](study-track/modules/03-prompt-and-context-engineering.md) | D6 11.0% | 4D Description; XML tags; examples; system vs user; output handling |
| [04-tools-and-mcps.md](study-track/modules/04-tools-and-mcps.md) | D8 10.6% | Tool use; web search; text edit; MCP intro + advanced (transports, sampling, roots) |
| [05-agents-and-workflows.md](study-track/modules/05-agents-and-workflows.md) | D1 14.7% | Workflows vs agents; parallelize/chain/route; Agent SDK; RAG / agentic search |
| [06-applications-and-integration.md](study-track/modules/06-applications-and-integration.md) | D2 remainder 26.3% | Requirements; SDLC; REST/JSON/async/git; interfaces; CLAUDE.md; pinning; plugins |
| [07-claude-code.md](study-track/modules/07-claude-code.md) | D3 3.1% | Claude Code; Skills; hooks; permission modes; headless; Computer Use |
| [08-eval-testing-debugging.md](study-track/modules/08-eval-testing-debugging.md) | D4 2.6% | Eval workflow; model vs code grading; traces; integration vs model origin |
| [09-security-and-safety.md](study-track/modules/09-security-and-safety.md) | D7 8.1% | Injection; untrusted input; guardrails; hooks; secrets; least privilege |

Also:

- [flashcards-all.md](study-track/flashcards-all.md) — all cards, module-tagged
- [cram-sheet.md](study-track/cram-sheet.md) — last-pass sheet

---

## Official sample-item patterns (Section 8)

Memorize the *decision*, not the story:

1. **Overnight, cost-primary, non-urgent, 10k docs → Message Batches API** (24h window, reduced cost). Not parallel sync Messages. Not `max_tokens` as a batch substitute. Not “smallest model regardless of quality.”
2. **User-submitted / retrieved web content with hidden instructions → treat as untrusted**, isolate from trusted instructions, guardrails/hooks so injected text cannot fire sensitive tools. Not temperature. Not a polite system-prompt request. Not “bigger model follows better” (can be *more* susceptible).
3. **Reusable internal REST capability across apps, independently maintained → MCP server**. Not hard-coded system prompts. Not pasting data into context. Not built-in tools (they do not reach arbitrary internal REST APIs).

---

## Exam traps to keep visible

Use the **distinction** to make the decision; use the **exam cue** to recognize it in a scenario.

| Distinction | What changes | Exam cue |
|---|---|---|
| **Workflow vs agent** | A workflow follows a graph you define; an agent lets the model choose actions and iterate in a tool-use loop. | Known, auditable steps → workflow. Open-ended goal with unknown next steps → agent. |
| **Parallelization vs chaining vs routing** | Parallelization runs independent branches and merges results; chaining passes one step's output to the next; routing selects a path. | Independent tasks → parallelize. B depends on A → chain. Choose one specialist → route. |
| **Manager/supervisor vs subagent** | The manager plans, delegates, and integrates; a subagent executes a delegated task in its own focused context. | Coordination and synthesis → manager. Isolated specialist work → subagent. |
| **Self-hosted vs Anthropic-hosted agents** | Self-hosting gives you more control over environment and operations; Anthropic hosting reduces operational burden but adds platform constraints. | Decide from deployment, control, compliance, and operations requirements—not branding. |
| **Realtime Messages API vs Message Batches API** | Realtime Messages serve interactive requests; Batches process asynchronous work within 24 hours at reduced cost. | User is waiting → realtime. Large, latency-tolerant workload → Batches; parallel realtime calls do not get the batch discount. |
| **Opus vs Sonnet vs Haiku** | Opus prioritizes capability; Sonnet balances quality, latency, and cost; Haiku prioritizes speed and cost for simpler work. | Choose for the task's quality, latency, cost, and cost-of-error requirements—not by defaulting to the largest or smallest model. |
| **Fast vs extended vs adaptive thinking** | Fast minimizes latency with less deliberation; extended spends more reasoning on hard tasks; adaptive lets the model vary effort. | Easy, latency-sensitive work → fast. Hard multi-step reasoning → extended. Mixed-difficulty workload → adaptive (where supported). |
| **Zero-shot vs single-shot vs multi-shot** | Zero-shot gives instructions only; single-shot adds one example; multi-shot adds several examples. | Use examples when format or edge cases are easier to show than explain. |
| **System vs user placement** | System holds durable rules and output contracts; user content supplies the current request and instance data. | Keep retrieved/user-provided text as data, not trusted system instructions. |
| **Model-based vs code-based grading** | A program checks objective properties; a model judge scores subjective qualities against a rubric. | Schema, exact fields, or tool calls → code. Tone, coherence, or partial credit → model. Combine when outputs have both. |
| **Integration-layer vs model-output failure** | Integration failures come from transport, auth, protocol, parsing, or client code; model failures occur in otherwise valid responses with wrong behavior or content. | Request failed or tool result was lost → inspect integration/traces. Valid response, wrong answer or tool choice → inspect prompt, tools, model, and evals. |
| **Prompt caching vs cache check-pointing** | Caching reuses a stable prompt prefix; check-pointing sets cache boundaries to preserve reusable prefixes when later content changes. | Keep stable content first and variable content later; caching reduces cost/latency, not correctness errors. |
| **Context drift vs context bloat** | Drift means goals or constraints fade or conflict; bloat means excessive tokens from history, repeated content, or tool output. | Lost/changed goal → reassert, compact, or reset. Too much noisy content → prune outputs; compact history or isolate work in a subagent as needed. |
| **Pruning vs compaction vs subagent isolation** | Pruning removes unnecessary content; compaction summarizes retained state; subagent isolation moves a subtask into a separate context. | Remove a dump → prune. Preserve decisions in fewer tokens → compact. Keep specialist work out of the parent context → isolate. |
| **Client-side vs server-side tools** | Client-side tools execute in the host app or user's environment; server-side tools execute remotely, such as in a backend or platform. | Check where data and side effects occur, and who enforces approval, access, and trust boundaries. |
| **MCP tools vs resources vs prompts** | Tools are model-controlled actions; resources are app-controlled, read-only data; prompts are user/host-selected templates. | Action → tool. Data to read → resource. Reusable instruction template → prompt. |
| **Direct vs templated MCP resources** | A direct resource has a static URI; a templated resource has a parameterized URI. Both expose data rather than perform actions. | One fixed resource → direct. Resource addressed by an identifier or parameter → template. |
| **stdio vs StreamableHTTP vs stateless HTTP** | stdio connects a local process; StreamableHTTP supports remote streaming and sessions; stateless HTTP avoids sticky sessions for horizontal scaling, trading away state-dependent features. | Local integration → stdio. Remote session/streaming needs → StreamableHTTP. Load-balanced scaling without session state → stateless HTTP. |
| **MCP request/result vs notification** | A request expects a result; a notification is one-way and does not expect a response. | Need a returned value → request. Progress or logging update → notification. |
| **Built-in tools vs custom tools vs Skills vs MCP vs CLAUDE.md vs hooks vs subagents** | Built-ins provide platform capabilities; custom tools add app-specific actions; Skills provide reusable procedural knowledge; MCP shares tools/data across apps; CLAUDE.md gives project guidance; hooks enforce deterministic lifecycle actions; subagents isolate delegated work. | Need an action → tool. Shared integration → MCP. Reusable procedure → Skill. Project conventions → CLAUDE.md. Must-enforce check → hook. Separate focused worker/context → subagent. |
| **Trusted instructions vs untrusted retrieved/user content** | Trusted instructions define policy; retrieved pages, uploads, and user text are data that may contain hostile instructions. | Keep untrusted content out of the instruction channel; use boundaries, least-privilege tools, and guardrails/hooks for sensitive actions. |
| **Structured output vs defensive parsing** | Structured output asks for a constrained shape; defensive parsing validates and safely handles what actually arrives. | A schema or “JSON only” request is not validation: check types/fields and handle malformed or truncated output before side effects. Confident prose is not proof. |
