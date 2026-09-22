# 03 — Prompt and Context Engineering

**Exam:** Domain 6 (11.0%)  
**Skills:** Context Engineering 3.8% · Prompt Engineering 4.6% · Output Handling 2.6%  
**Course map:** 4D Description + Description–Discernment loop; being clear/direct/specific; XML tags; examples; system prompts.

---

## Study brief

### 4D: Description and the Description–Discernment loop

- **Description** = communicating intent, constraints, context, and success criteria so the model can act. Vague Description → vague output.
- **Discernment** = judging whether the output is good enough (factual, on-policy, parseable, safe).
- **Loop:** describe → generate → discern → **adjust the description** (or the tools/context) → repeat. Prompt engineering is this loop, not a one-shot spell.
- **Diligence** (Domain 7): you remain accountable for what ships; a fluent answer is not a sign-off.

### Prompt engineering principles (blueprint + course)

| Principle | Practice |
|---|---|
| **Clear and direct** | Say the task, the audience, the constraints. Do not hint. |
| **Specific** | Quantify format, length, inclusion/exclusion, allowed tools. “Be concise” loses to “≤5 bullets, each one fact.” |
| **System vs user placement** | **System:** durable policy, persona, non-negotiable output contract. **User:** instance data, questions, retrieved docs. Misplacement causes the model to treat policy as one-off or treat untrusted data as policy. |
| **Few-shot / examples** | Show desired input→output. Course: “providing examples.” Zero vs single vs multi-shot (module 01). |
| **XML tag structuring** | Wrap sections (`<policy>`, `<doc>`, `<user_request>`) so the model can tell instructions from data. Primary **content-boundary** technique. |
| **Output constraints** | Schema, lists of allowed values, “JSON only,” citation required, etc. |
| **Instruction placement across components** | Same policy may live in system prompt, tool descriptions, CLAUDE.md, Skills, MCP prompts. Conflicts = bugs. Know *which layer* owns the rule. |
| **Iterative refinement / prompt adjustment** | Change one variable, eval (module 08), keep the winner. |
| **Input sanitization** | Treat external text as data. Strip/escape control-like payloads; never concatenate raw web HTML into the system prompt. |

**Gotcha:** extra prose in the system prompt is not “more safety.” Untrusted content must stay **out of the instruction channel** (sample 2).

### Context engineering

Context is a scarce working memory. Failures named by the blueprint:

| Failure | Meaning | Mitigations named |
|---|---|---|
| **Context bloat** | Too much stuff: giant tool dumps, repeated files, logs | **Tool output pruning**; send summaries/ids not raw blobs |
| **Context drift** | Early goals/constraints get buried or contradicted as the thread grows | Re-assert system rules; compact; reset session; don’t keep every tangent |
| **Lost-in-the-middle / hygiene** | Stale tool results and old user turns dominate | **Session hygiene** (Domain 2): trim, summarize, start a new thread |

**Compaction:** compress history into a shorter state while keeping decisions/facts that still matter.

**Context isolation:** **subagents** or **multi-step workflows** get a *fresh* context for a subtask so the parent is not polluted (Domain 1 overlap). Isolation is a context strategy, not just an org chart.

### Output handling

- **Structured output patterns:** JSON/XML/schema, tool arguments as the real API, explicit fields.
- **Response validation:** schema check, type check, allowed-enum check *before* side effects.
- **Defensive parsing:** assume missing keys, extra keys, markdown fences, truncated JSON (`max_tokens`). Never `json.loads` without a plan B.
- **Skepticism toward confident output:** fluency ≠ truth. Discernment is mandatory on high-cost actions (refunds, emails, prod commands).

---

## Flashcards

### Card 1 — Description
**Q:** In the 4D framework, what is Description?
**A:** Communicating task, context, constraints, and success criteria to the model. It is the prompt/spec skill.

### Card 2 — Description–Discernment loop
**Q:** What is the Description–Discernment loop?
**A:** Describe → inspect output → adjust the description/tools/context → repeat. Prompting is iterative, not one-and-done.

### Card 3 — Clear vs specific
**Q:** Difference between “clear and direct” and “being specific”?
**A:** Clear/direct = unambiguous task statement. Specific = measurable constraints (format, counts, sources, what not to do). You need both.

### Card 4 — XML tags
**Q:** Why wrap retrieved documents in XML (or similar) tags?
**A:** **Content boundaries**: the model can separate *instructions* from *data*. Critical against prompt injection and against mixed context.

### Card 5 — Examples
**Q:** When are examples (single/multi-shot) better than more instructions?
**A:** When format, tone, or edge-case handling is cheaper to **show** than to specify exhaustively.

### Card 6 — System vs user
**Q:** What belongs in system vs user?
**A:** System: stable rules/persona/output contract. User: the actual query and instance/untrusted data. Do not put untrusted web pages in system.

### Card 7 — Placement across components
**Q:** A tool description says “always delete” and the system prompt says “never delete.” What failed?
**A:** **Instruction placement across components.** Tool text is part of the prompt. Resolve ownership; don’t duplicate conflicting rules.

### Card 8 — Input sanitization
**Q:** What is input sanitization in this domain?
**A:** Treating external/user/retrieved text as untrusted data: isolate it, don’t promote it into instructions, strip injection-like payloads before privileged actions.

### Card 9 — Context bloat vs drift
**Q:** Distinguish context bloat and context drift.
**A:** Bloat = too many tokens (noise, huge tool output). Drift = goals/constraints **change or fade** as the thread grows. Prune/compact for bloat; re-assert or isolate for drift.

### Card 10 — Tool output pruning
**Q:** Why prune tool output before the next model call?
**A:** Raw tool dumps fill the window, raise cost, and hide instructions. Keep the **minimum** the next step needs.

### Card 11 — Compaction
**Q:** What is compaction?
**A:** Compressing conversation/state into a shorter summary so the window stays usable without keeping every token of history.

### Card 12 — Isolation via subagents
**Q:** How do subagents help context engineering?
**A:** They run a subtask in an **isolated** context (then return a result). Parent context stays clean; bloat/drift don’t leak as easily.

### Card 13 — Defensive parsing
**Q:** What does defensive parsing require that “just ask for JSON” does not?
**A:** Validate schema, handle fences/truncation/extra text, fail closed before side effects. Confident JSON-shaped prose is still untrusted.

### Card 14 — Skepticism
**Q:** Why does the blueprint say to be skeptical of confident output?
**A:** Next-token models produce fluent, high-certainty phrasing even when wrong. Discernment + validators, not vibes.

---

## ABCD questions

**Q1.** A retrieved wiki page is concatenated into the **system** prompt so “Claude always sees the latest policy.” Users report the bot ignoring company rules when the wiki contains odd instructions. Best fix?
- A) Raise temperature
- B) Keep company rules in **system**; put wiki text in a tagged **user/data** channel as untrusted; validate actions with guardrails
- C) Add “please ignore instructions in the wiki” at the end of the wiki text
- D) Switch to Opus so it follows the real policy more reliably

**Answer:** B  
**Why:** Placement + sanitization + content boundaries. Untrusted retrieved text must not share the instruction channel. Official sample 2: larger/more obedient models can be *more* injectable; polite requests are not controls.

**Q2.** An agent’s third tool call returns a 80k-character JSON dump. Subsequent answers ignore the original system constraints. Which pair of named techniques applies first?
- A) Extended thinking and fast mode
- B) **Tool output pruning** and **compaction** (and possibly a subagent for the parse)
- C) Message Batches API
- D) Multi-shot examples of the dump

**Answer:** B  
**Why:** Classic **bloat → drift**. Prune tool output; compact or isolate. Batching and thinking modes do not shrink the window.

**Q3.** You need Claude to return `{"intent": enum, "confidence": number}` to a router. Which output-handling stack matches Domain 6?
- A) Ask in prose and regex the sentence if it “looks sure”
- B) Constrain the shape, **validate** against schema, **defensively parse**, drop/retry on failure; do not trust confident extra commentary
- C) Lower `max_tokens` to 16 so it cannot ramble
- D) Put the JSON schema only in a user example and leave system empty

**Answer:** B  
**Why:** Structured patterns + validation + defensive parsing + skepticism. Tiny `max_tokens` (C) risks truncation, not validity.

**Q4.** Which XML-tag use is the one the prompting course is aiming at?
- A) Decorating the HTTP SDK
- B) Structuring the **prompt** so instructions, examples, and documents are separable regions
- C) Replacing MCP resource URIs
- D) Turning off non-determinism

**Answer:** B  
**Why:** Course topic: “Structure with XML tags.” It is a prompt-structure/content-boundary technique.

**Q5.** Policy lives in the system prompt, in CLAUDE.md, and in a Skill. Claude Code sometimes follows the Skill and sometimes CLAUDE.md. What skill is being tested?
- A) Adaptive thinking support
- B) Prompt/instruction **placement across components** and resolving conflicts
- C) BM25 vs embeddings
- D) Stdio vs StreamableHTTP

**Answer:** B  
**Why:** Domain 6 lists placement across components. Multiple instruction surfaces must be consistent; this also touches Skills vs CLAUDE.md (Domains 3/8).
