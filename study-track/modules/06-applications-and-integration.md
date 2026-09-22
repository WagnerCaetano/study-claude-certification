# 06 — Applications and Integration

**Exam:** Domain 2 remainder (Understanding Requirements 3.4% · Systems Life Cycle 2.8% · Software Engineering Foundations 7.4% · Claude Application Design 8.6% · Configuration Management 4.1%). API mechanics are module 02. Combined D2 = **33.1%**.  
**Course map:** project planning/Delegation; how Claude is configured across Code/Desktop/claude.ai/API; CLAUDE.md; plugins.

---

## Study brief

### Understanding requirements (3.4%)

Translate business need → **functional** + **infrastructure** requirements:

- **Functional:** latency SLO (chat vs overnight), quality bar, modalities (vision/PDF), tools, citations, languages, audit trail, human approval.
- **Infrastructure:** where the loop runs (self vs Anthropic-hosted), VPC/data residency, key storage, scaling (realtime vs **batch**), MCP transport, logging/traces.

**Exam move:** match requirement → feature. Non-urgent volume → Batches. Reusable internal API → MCP. Interactive tokens → streaming. Hard reasoning → thinking/Opus. Untrusted HTML → isolation + hooks.

Wrong move: picking a model first, then reverse-engineering the requirement.

### Systems life cycle (2.8%)

Claude apps still follow SDLC: **develop → implement → operate → maintain**.

- Develop: prompts, tools, evals as artifacts; version them.
- Implement: pin models, secrets, config per environment.
- Operate: traces, cost telemetry, rate limits, on-call.
- Maintain: prompt/model regressions, breaking model releases, plugin/MCP dependency updates.

Evals (Domain 4) are the test stage. Hooks/guardrails (Domain 7) are operate-time controls. This skill is the **framework**, not a specific product.

### Software engineering foundations (7.4%)

The exam expects ordinary engineering, with Claude as a dependency:

- **REST + JSON:** Messages and most SDKs. Know verbs, status codes, JSON bodies, idempotency where you implement it.
- **Async:** streaming, concurrent docs, tool round-trips.
- **Version control:** prompts, Skills, CLAUDE.md, eval datasets live in git — not only `.py`.
- **SDLC integration:** PR review for prompt changes; CI for evals.
- **Code review:** read tool schemas and system prompts like code (they *are* behavior).
- **Refactoring:** small (tighten a prompt/tool) vs large (workflow → agent split, RAG index redesign). Same discipline: tests/evals before and after.

### Claude application design (8.6%) — high yield

Claude **does not see one interface**. Instructions must be designed **per surface**:

| Surface | User/dev relationship | Instruction surfaces |
|---|---|---|
| **API / SDKs** | You own the messages array | `system`, tools, your UI |
| **claude.ai** | End-user chat | Project instructions, uploaded files, user turns |
| **Claude Desktop** | Local host + MCP | MCP servers, local resources, user prompts |
| **Claude Code** | Repo-native agent | **CLAUDE.md**, Skills, commands, hooks, settings.json, MCP |

**Content boundaries:** same as XML tagging — mark untrusted files, stdin, web fetches. Do not let repo junk become policy.

**Schema design:** tools and structured outputs are your **API**. Stable field names; version schemas; don’t silently rename `tool` parameters.

**Session hygiene:** the API is stateless; *you* trim history, rotate sessions, don’t infinitely append tool dumps (Domain 6). In Claude Code: know when to `/compact` or new session (Domain 3).

**Plugin management:** plugins bundle Skills, hooks, MCP, commands. Track **plugin dependencies** like libraries: version, trust, blast radius.

### Configuration management (4.1%)

Named artifacts:

| Artifact | Job |
|---|---|
| **CLAUDE.md** | Project/user/org instructions Claude Code (and similar) always load. Hierarchy matters (module 07) |
| **settings.json** | Runtime config: permissions, model, hooks enablement — not prose instructions |
| **Model version pinning** | Stop silent breaking changes (Domain 5) |
| **Prompt versioning** | System prompts/Skills as versioned files; rollback when evals drop |
| **Plugin dependencies** | Pin/review plugins the same way as npm/pypi |

**Gotcha:** putting secrets in CLAUDE.md or prompts. Secrets go to a secret manager (Domain 7). CLAUDE.md is often **committed**.

**Gotcha:** CLAUDE.md vs Skill vs hook vs MCP — configuration management is **which file owns which behavior**, not “dump everything in CLAUDE.md.”

---

## Flashcards

### Card 1 — Functional vs infrastructure
**Q:** Give one functional and one infrastructure requirement derived from “overnight 10k docs, cheapest, morning deadline.”
**A:** Functional: batch analytics quality bar, no interactive UX. Infrastructure: **Message Batches**, async workers, cost telemetry — not a GPU chat cluster.

### Card 2 — Feature matching
**Q:** Business says “answers must show which PDF paragraph they used.” Which design choice?
**A:** PDF/document input + **citations**, plus those files in context. Not temperature, not Haiku-only.

### Card 3 — SDLC and prompts
**Q:** Where do prompts sit in the systems life cycle?
**A:** They are **versioned artifacts**: developed, reviewed, deployed, operated (monitored), maintained (eval on model change). Not one-off chat.

### Card 4 — REST/JSON
**Q:** Why does a Claude exam test REST and JSON?
**A:** The API is a REST JSON interface; SDKs wrap it. Tool schemas, MCP messages, and structured output are JSON. You must parse and version them.

### Card 5 — Prompts in code review
**Q:** What should a PR review include for a Claude app besides Python diffs?
**A:** System prompts, tool descriptions/schemas, CLAUDE.md, Skills, eval datasets, permission/hook config — they change runtime behavior.

### Card 6 — Interfaces
**Q:** Why does “how Claude interprets instructions across Claude Code, Desktop, claude.ai, API, SDKs” matter?
**A:** Each surface has **different instruction channels**. A rule in CLAUDE.md does not exist on a raw API call unless you copy it into `system`.

### Card 7 — Content boundaries
**Q:** What are content boundaries in application design?
**A:** Separating trusted instructions from untrusted files/user/web content (tags, channels, sanitization) so data cannot become policy.

### Card 8 — Schema design
**Q:** What is schema design for Claude apps?
**A:** Stable contracts for **tool inputs/outputs** and structured responses. Changing names without versioning breaks parsers and the model’s few-shot memory.

### Card 9 — Session hygiene
**Q:** What is session hygiene?
**A:** Managing history so the window stays on-task: trim, compact, new session, prune tool output. Don’t unbounded-append.

### Card 10 — CLAUDE.md vs settings.json
**Q:** CLAUDE.md vs settings.json?
**A:** CLAUDE.md = **instruction/memory prose** for the agent. settings.json = **configuration** (permissions, model, hooks). Don’t put IAM policy only in markdown, or essays in JSON config.

### Card 11 — Pinning
**Q:** What two things does Domain 2 tell you to pin?
**A:** **Model versions** and **prompt/plugin versions** (plus plugin dependencies). Unpinned “latest” is a production incident.

### Card 12 — Plugins
**Q:** What is plugin management in Claude application design?
**A:** Plugins package Skills/hooks/MCP/commands. Treat them as **dependencies**: review, version, least privilege — they execute in the agent environment.

---

## ABCD questions

**Q1.** A bank wants a Claude app to draft customer emails from CRM notes, with a human sending them. PII must not leave their VPC. Which requirement split is correct?
- A) Functional: Message Batches only. Infrastructure: none
- B) Functional: draft quality, human approval before send. Infrastructure: **self-hosted** loop, VPC, secret manager, logging; no Anthropic-hosted agent if it violates residency
- C) Functional: Computer Use. Infrastructure: Haiku
- D) Functional: MCP sampling. Infrastructure: temperature 1

**Answer:** B  
**Why:** Requirements skill: map business constraints to functional (draft + approval) and infrastructure (residency/hosting/keys). Hosting model is an infrastructure requirement (Domain 1 overlap).

**Q2.** The same prompt works in Claude Code but “ignores the repo rules” when called from a backend SDK. Likely design error?
- A) BM25 is disabled in the SDK
- B) **CLAUDE.md is not the API system prompt** — instructions do not automatically transfer across interfaces
- C) SDKs cannot send system prompts
- D) Need StreamableHTTP

**Answer:** B  
**Why:** Application design: Claude interprets instructions **per interface**. You must port CLAUDE.md rules into `system`/tools for the API.

**Q3.** A team stores the production API key in CLAUDE.md so “every clone works.” What configuration/security failure is this?
- A) Correct — CLAUDE.md is encrypted at rest by git
- B) Secrets in a often-**committed instruction file**; use a secret manager / env; CLAUDE.md is not a key store
- C) Keys belong in tool JSON schemas
- D) Pinning the model version replaces key management

**Answer:** B  
**Why:** Config management + Domain 7 identity/secrets. CLAUDE.md is documentation/instructions, frequently in git.

**Q4.** You must refactor a 2,000-line system prompt into Skills, tools, and a short system prompt. What SE foundation applies?
- A) It is not refactoring because prompts are not code
- B) **Large-scale refactor** of behavior: use evals/review like any refactor; prompts/tools *are* the behavior
- C) Only allowed if you switch to LangGraph
- D) Delete evals so they don’t fail

**Answer:** B  
**Why:** Domain 2 lists small- and large-scale refactoring; prompts are first-class artifacts in SDLC and code review.
