# 01 — Model Selection and Optimization

**Exam:** Domain 5 (16.8%)  
**Skills:** LLM Fundamentals 5.2% · Technical Fundamentals 6.1% · Model Selection and Tradeoffs 2.7% · Cost and Token Management 2.8%  
**Also in `../../resource/resource.md`:** Generative AI fundamentals; capabilities & limitations; 4D Delegation lens for *what* to hand the model.

---

## Study brief

### How Claude generates (LLM fundamentals)

Claude is a **next-token** model: it samples the next token from a probability distribution over the vocabulary, given prior tokens. That implies:

- **Tokens**, not words, are the billing and context unit. Prompt + output consume the **context window**. Overflow → truncation, dropped instructions, or request failure depending on how the client is built.
- **Sampling** (temperature and related controls) shapes that distribution. Higher temperature → more diverse, less repeatable. Lower → tighter, more deterministic — never fully deterministic.
- **Non-determinism** is expected even at temperature 0. Do not design systems that require bit-identical outputs unless you add your own constraints (structured output, validators, retries, evals).
- **Capabilities:** fluent generation, tool use, vision/PDF, long reasoning (thinking modes), following structured instructions. **Limitations:** no live knowledge unless you retrieve/tool; can be confidently wrong; context is finite; untrusted text can override intent (see module 09).

**4D — Delegation:** decide *whether* a task belongs to the model (generation, classification, extraction, tool orchestration) vs a deterministic program (auth, money movement, schema validation). Wrong Delegation is a model-selection failure as much as a prompting failure.

### Model options named in the blueprint

These are **runtime modes**, not separate product SKUs in the resource. Select them as part of model choice:

| Option | What it is | When |
|---|---|---|
| **Fast mode** | Minimize latency; less deliberation | Interactive UX, cheap classification, high QPS |
| **Extended thinking** | Model spends extra internal reasoning before answering | Hard problems, multi-step analysis, coding, math-like work |
| **Adaptive thinking** | Model decides how much thinking to use | Mixed workloads where some items are easy and some are not; blueprint explicitly calls out **adaptive thinking support** as a selection criterion across Opus/Sonnet/Haiku |
| **Effort levels** | Dial how hard the model tries (cost/latency vs quality) | Same model, different budget per request |

**Gotcha:** extended thinking improves *hard* tasks and costs more (tokens/time). Do not turn it on for “hello world” extraction. Adaptive thinking is the lever when the *workload mix* varies.

### Opus vs Sonnet vs Haiku

The resource names three capability tiers. Exam items will force a **quality / latency / cost** tradeoff, not a brand preference.

| Tier | Use when | Avoid when |
|---|---|---|
| **Opus** | Highest quality: complex agents, hard reasoning, ambiguous specs, high cost-of-error | Tight latency SLOs or bulk cheap classification |
| **Sonnet** | Default production workhorse: agents, coding, most API apps | You truly need either max quality or min cost/latency |
| **Haiku** | Fast/cheap: classification, routing, simple extract, high volume | Long-horizon agents, subtle instruction following, high-stakes generation |

**Breaking behavior changes across model releases:** pin a **model version** (module 06). A new release can change tool-calling style, refusal boundaries, or JSON shape. Selecting “latest” without pinning is a production risk the blueprint tests.

### Prompting shots (fundamentals, not full prompt-eng)

| Pattern | Meaning |
|---|---|
| **Zero-shot** | Instruction only; no examples |
| **Single-shot** | One example (one-shot) |
| **Multi-shot** | Several examples (few-shot) |

Use shots when the *format or edge cases* are cheaper to show than to describe. This overlaps Domain 6; here, know the names.

### Technical fundamentals the exam tags under D5

Building Claude apps is still software:

- Anthropic **SDK wraps a REST API** (JSON in/out). You must know HTTP request/response, status handling, and JSON parsing — not only SDK sugar.
- **Asynchronous programming** for concurrent requests, streaming consumption, and tool round-trips.
- **Websockets** appear in the blueprint as a supporting integration pattern (live bidirectional channels). Contrast with HTTP request/response and with MCP transports (stdio / StreamableHTTP) in module 04.
- Version control, code review, refactoring sit in Domain 2; under D5 the point is: **the model call is one component in a normal engineering stack**.

### Cost and token management

- **Track usage** per request (input vs output tokens; thinking/cache tokens if the API reports them). You cannot model cost without telemetry.
- **Cost modeling:** volume × (input rate + output rate) × retries/tools/thinking. Agents multiply cost because of **multi-step tool loops**.
- **Prompt caching:** reuse stable prefix (system prompt, tools, large docs) to cut latency and cost. Resource: *optimize API usage and reduce latency*; “rules of prompt caching” are a named course topic — treat cacheability as a **prefix-stability** problem (static content first; volatile user turns last).
- **Cache check-pointing:** named separately from prompt caching. Treat it as **placing explicit cache boundaries** so later variable content does not bust the whole cached prefix. Caching ≠ check-pointing: caching is the feature; check-pointing is how you **structure** the prompt so cache hits survive.

**Batch vs realtime** is Domain 2 API mechanics, but it is a **cost lever**: Message Batches API = 24-hour, reduced cost, async. Do not use a smaller model as a substitute for batching an overnight job (official sample).

---

## Flashcards

### Card 1 — Next-token generation
**Q:** What does “next-token generation” imply for Claude application design?
**A:** The model samples one token at a time from a distribution; outputs are **non-deterministic**, billed and bounded in **tokens**, and quality depends on context + sampling — not on a stored answer key.

### Card 2 — Context window
**Q:** What happens conceptually when prompt + output exceed the context window?
**A:** You run out of room for instructions, history, tool results, or the answer. Manage with truncation strategy, compaction, pruning, or subagent isolation (Domain 6) — do not assume infinite memory.

### Card 3 — Sampling / temperature
**Q:** What does temperature control, and what does it *not* guarantee?
**A:** It controls sampling diversity. Low temperature → more concentrated / repeatable; high → more varied. It does **not** guarantee identical outputs, and it is **not** a prompt-injection defense.

### Card 4 — Fast vs extended vs adaptive thinking
**Q:** When do you choose fast mode, extended thinking, or adaptive thinking?
**A:** Fast: latency-sensitive easy work. Extended: hard multi-step reasoning worth extra cost. Adaptive: mixed easy/hard traffic so the model spends thinking only when needed. Adaptive **support** differs by model tier — check it when selecting Opus/Sonnet/Haiku.

### Card 5 — Effort levels
**Q:** What are effort levels for?
**A:** A dial on how hard the model tries for a given model family — trade quality against cost/latency without necessarily switching Opus/Sonnet/Haiku.

### Card 6 — Opus vs Sonnet vs Haiku
**Q:** One-line selection rule for the three Claude tiers?
**A:** Opus = max quality / high cost-of-error. Sonnet = default quality-latency-cost balance. Haiku = min latency/cost, simpler tasks. Always state the **quality/latency/cost** tradeoff.

### Card 7 — Breaking changes
**Q:** Why does the blueprint mention breaking behavior changes across model releases?
**A:** New models can change tool use, formatting, or refusals. **Pin model versions**; evaluate before upgrading. “Latest” is not a production strategy.

### Card 8 — Zero / single / multi-shot
**Q:** Define zero-shot, single-shot, and multi-shot.
**A:** Zero-shot: instructions only. Single-shot: one example. Multi-shot: multiple examples. Examples teach **format and edge cases** cheaper than prose.

### Card 9 — SDK vs REST
**Q:** What is the relationship between Anthropic SDKs and the API?
**A:** SDKs **wrap REST**. You still need JSON, HTTP semantics, auth headers/keys, and error handling. SDK convenience is not a different product.

### Card 10 — Async and streaming
**Q:** Why does Domain 5 list asynchronous programming next to LLM fundamentals?
**A:** Streaming, concurrent document jobs, and multi-round tool calls are async I/O problems. Blocking request-per-request designs will miss latency and throughput targets.

### Card 11 — Websockets vs REST
**Q:** Where do websockets fit relative to the Messages REST API?
**A:** REST = request/response (including HTTP streaming). Websockets = persistent bidirectional channel for some app architectures. Do not confuse with MCP stdio/SSE (Domain 8).

### Card 12 — Prompt caching vs check-pointing
**Q:** Distinguish prompt caching from cache check-pointing.
**A:** Prompt caching reuses a stable prompt **prefix** to cut cost/latency. Check-pointing is placing **cache boundaries** so volatile suffixes do not invalidate the cached prefix. Both are cost tools; they are not the same step.

### Card 13 — Agent cost multiplier
**Q:** Why do agents blow cost models that were tuned on single Messages calls?
**A:** Each tool round-trip adds input (full history + tool results) and output tokens, possibly with thinking. Cost is **turns × tokens**, not one completion.

### Card 14 — Delegation vs model tier
**Q:** A task is safety-critical and must be exact. Is “use Opus” the right first move?
**A:** First **Delegate** correctly: if a program can do it deterministically, do not use a model. If you need a model, pick tier/thinking for residual judgment — then **validate** output (Domain 6/4).

### Card 15 — Capabilities vs limitations
**Q:** Name two generative-AI limitations the fluency section expects you to remember.
**A:** No inherent live/private data access without tools/RAG; finite context; confident errors; susceptibility to untrusted instructions. Capabilities (language, vision, tools, thinking) do not cancel those.

### Card 16 — Overnight cost job vs smaller model
**Q:** For 10k non-urgent overnight docs, is switching to Haiku the primary cost optimization?
**A:** No. Sample item: use **Message Batches API** (24h, reduced cost). Blindly shrinking the model ignores the realtime-vs-batch tradeoff and can destroy quality.

---

## ABCD questions

**Q1.** A product needs interactive chat with tight latency. A nightly job re-analyzes the same chats for analytics and can wait until morning. Which pairing is best?
- A) Opus + extended thinking for both paths so quality is consistent
- B) Haiku fast mode for chat; Message Batches API with an appropriate model for the nightly job
- C) Sonnet realtime in a tight parallel loop for the nightly job to finish faster
- D) One pinned Opus deployment; drop `max_tokens` at night to save money

**Answer:** B  
**Why:** Interactive path wants low latency (fast/Haiku-class). Nightly non-urgent volume wants **batch** (24h window, reduced cost). Parallel realtime (C) does not buy the batch discount; `max_tokens` (D) is not a batch strategy.

**Q2.** You must choose between Opus, Sonnet, and Haiku for a customer-support router that classifies intent into 12 labels, 200 QPS, then hands hard tickets to a deeper agent. Which is the sound split?
- A) Opus for the router so labels are perfect; Haiku for the deep agent
- B) Haiku (or fast/cheap tier) for routing; a higher tier + thinking for the deep agent
- C) Adaptive thinking on Opus for every request including routing
- D) Smallest model for both, because routing and resolution have the same cost-of-error

**Answer:** B  
**Why:** Router is high-QPS, low cost-of-error per call → cheap/fast tier. Deep agent is high cost-of-error → stronger model/thinking. Inverting tiers (A) wastes money; adaptive Opus on everything (C) ignores the split; equal downsizing (D) ignores quality/latency/cost.

**Q3.** After a model upgrade, JSON tool arguments change shape and a parser starts failing. What does the blueprint expect you to have done?
- A) Raised temperature so outputs vary less
- B) Relied on “latest” so you always get improvements
- C) Pinned the model version and run evals before promoting the new release
- D) Moved all instructions from system to user so the new model cannot drift

**Answer:** C  
**Why:** Domain 5 explicitly includes **breaking behavior changes across model releases**. Pin + evaluate. Temperature and prompt placement do not replace version discipline.

**Q4.** A team caches a 20k-token policy manual plus a per-user question. Cache hit rate is near zero. What is the most likely design error named by the cost skill?
- A) Using multi-shot examples
- B) Not placing a **cache checkpoint** / stable prefix: user-specific or unordered content is breaking the cacheable prefix
- C) Temperature is too low for cache reads
- D) They should have used websockets instead of REST

**Answer:** B  
**Why:** Prompt caching needs a **stable prefix**. Check-pointing is how you bound that prefix. Temperature and websockets are unrelated to cache hits.

**Q5.** Which statement about SDKs is correct under Domain 5 Technical Fundamentals?
- A) The Python SDK replaces REST; HTTP status codes no longer apply
- B) SDKs wrap REST APIs; JSON, auth, and async HTTP still matter
- C) Websockets are required for all Messages API calls
- D) Token accounting only exists inside Claude Code, not the API

**Answer:** B  
**Why:** The skill is “integrating with SDKs that wrap REST APIs, websockets.” Wrapping ≠ replacing. Streaming/Messages remain HTTP-family; websockets are an additional pattern, not a requirement for every call.
