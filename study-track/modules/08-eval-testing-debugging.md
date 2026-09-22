# 08 — Eval, Testing, and Debugging

**Exam:** Domain 4 Debugging and Error Handling **2.6%**  
**Course map:** Prompt evaluation (typical workflow, test datasets, running evals, model-based grading, code-based grading); 4D Discernment; output handling overlap.

Small weight. Items will be **origin isolation** and **which grader/recovery**. Do not skip.

---

## Study brief

### Discernment is the skill

You cannot ship on vibe. Eval is how Description–Discernment becomes an engineering practice: **fixed cases**, **repeatable grades**, **regression on model/prompt change**.

### Typical eval workflow (course)

1. **Define the behavior** (spec, not “make it nicer”).
2. **Generate a test dataset** — realistic inputs, labeled or with a checker. Include edge cases, injection-like inputs, truncated docs, multilingual if needed.
3. **Run the eval** — same model pin, same prompt version, log traces.
4. **Grade** — code-based and/or model-based.
5. **Iterate the prompt/tools** (Description loop). Do not change five things at once.

**Dataset generation** can use Claude to *draft* cases; humans still audit labels. Garbage labels → garbage eval.

### Two graders

| | **Code-based grading** | **Model-based grading** |
|---|---|---|
| How | Deterministic program: parse JSON, exact match, regex, schema, unit tests, AST | A model scores/rubrics the output |
| Strength | Repeatable, cheap, CI-friendly | Captures **quality** that is fuzzy (tone, helpfulness, partial credit) |
| Weakness | Brittle if valid paraphrases exist; cannot see “good essay” | **Non-deterministic**, cost, can be gamed; needs its own prompt eval |
| Use | Structured output, tool args, citations present, forbidden strings, HTTP mock assertions | Rubric for summaries, tutoring, messy language |

**Exam trap:** using a model judge for `{"status": "ok"}` JSON — that’s code. Using exact string match for a free-form summary — that’s the wrong grader.

You often **combine**: code checks the schema; model grades the prose inside a field.

### Debugging and error handling (the scored skill)

Blueprint verbs:

1. **Error type identification** — classify what broke.
2. **Recovery strategy selection** — retry, backoff, fallback model, fail closed, human, degrade feature.
3. **Trace analysis** — inspect the **message trail** (prompts, tool_use, tool_result, thinking, stream chunks).
4. **Problem origin isolation** — **integration layer vs model output**.

**Origin isolation (highest-yield distinction):**

| Origin | Symptoms | You fix |
|---|---|---|
| **Integration layer** | 4xx/5xx, auth, timeouts, dropped **tool_result**, wrong block types, truncated client JSON, cache always miss because you shuffled bytes, SDK misuse | Client/server code, retries, schema, session |
| **Model output** | 200 OK, well-formed messages, but wrong answer, bad tool choice, ignored system prompt, hallucinated args | Prompt, examples, tools, model tier/thinking, evals |

If the parser throws, do not “increase Opus.” If Opus returns valid JSON with the wrong business value, do not “catch Exception and retry HTTP.”

**Traces:** you need logs of **each turn**: system hash/version, tools, user, assistant blocks, tool results, token usage, stop reason. Without traces you cannot isolate origin.

**Recovery (pick to match the error type):**

- Transient API/network → retry/backoff.
- Rate/overload → retry with backoff or queue; batch if the job is latency-tolerant.
- Invalid model JSON → defensive parse, **retry with repair prompt**, or fallback to a stricter tool schema.
- Wrong tool used → better descriptions / fewer tools / approval hook — not infinite retry.
- Safety/injection attempt → **fail closed**, do not retry the same untrusted payload into a privileged tool.
- Persistent quality miss → eval + prompt/model change, not a hot retry loop.

**Gotcha:** retrying a non-deterministic quality failure can look like “it works on retry” and hide a prompt bug.

---

## Flashcards

### Card 1 — Eval workflow order
**Q:** Typical prompt-eval workflow?
**A:** Spec → **test dataset** → run (pinned model/prompt) → **grade** (code and/or model) → iterate one change at a time.

### Card 2 — Dataset
**Q:** What makes a test dataset useful?
**A:** Realistic inputs **and** a grading target (label or checker). Include edges. Generated sets still need human/spec audit.

### Card 3 — Code-based grading
**Q:** When is code-based grading the right grader?
**A:** When correctness is **machine-checkable**: schema, exact fields, allowed enums, “tool X was called,” unit tests.

### Card 4 — Model-based grading
**Q:** When is model-based grading warranted?
**A:** When the spec is **rubric/quality** (coherence, tone, completeness) that a program cannot cheaply score. Pin and eval the judge too.

### Card 5 — Combined grading
**Q:** Sensible hybrid?
**A:** Code validates structure/safety constraints; model grades remaining subjective quality.

### Card 6 — Origin isolation
**Q:** First question when debugging a Claude feature?
**A:** Is this **integration** (transport, auth, blocks, our parser) or **model output** (200 + legal transcript, wrong behavior)?

### Card 7 — Traces
**Q:** What must a trace contain to isolate failure modes?
**A:** Ordered turns: prompts/versions, **content blocks**, tool results, usage, errors. Not just the final user-visible string.

### Card 8 — Recovery match
**Q:** Match recovery to error type: timeout vs bad JSON vs injection vs wrong answer.
**A:** Timeout → retry/backoff. Bad JSON → defensive parse/repair or schema tighten. Injection → fail closed/guardrail. Wrong answer → prompt/model/eval, not blind retry.

### Card 9 — Discernment
**Q:** How does 4D Discernment show up in this domain?
**A:** Systematic judgment of outputs via evals and traces — not “it sounded confident.”

### Card 10 — Integration masquerading as model
**Q:** Client dropped `tool_result` and the model “hallucinated” the API. Origin?
**A:** **Integration layer** (broken tool protocol). The model never got the result. Fix the client, then re-eval.

---

## ABCD questions

**Q1.** A structured extraction prompt returns JSON. Product wants ≥99% valid parse in CI. Primary grader?
- A) Model-based rubric on eloquence
- B) **Code-based** schema validation (and maybe golden fields)
- C) User surveys
- D) Temperature 1 plus human vibe check only

**Answer:** B  
**Why:** Course split: code-based for deterministic structure. Model grading is for fuzzy quality, not “is this JSON.”

**Q2.** Traces show HTTP 200, correct `tool_result` with `balance: 0`, but the assistant tells the user they have $4M. Origin and fix?
- A) Integration timeout; retry
- B) **Model output** (it ignored tool data) → prompt/tools/eval; do not first rewrite the HTTP client
- C) Missing API key
- D) Need StreamableHTTP

**Answer:** B  
**Why:** Isolation skill: the integration worked; the **model output** is wrong. Recovery is prompt/eval, not transport.

**Q3.** Intermittent `tool_use` with no matching result in your logs, then a parser crash. Origin?
- A) Adaptive thinking
- B) **Integration**: you are not persisting/returning **message blocks**; fix the harness, then re-run evals
- C) BM25
- D) Enterprise Skills settings

**Answer:** B  
**Why:** Debugging = traces + origin. Missing tool_result is a client protocol bug (module 02/08).

**Q4.** Eval scores drop after a “silent” model upgrade. First operational move?
- A) Delete the dataset so scores look stable
- B) **Pin versions**, compare traces against the last good prompt/model pair, treat as a **breaking behavior change**
- C) Disable all tools
- D) Switch graders from code to model so the new prose “passes”

**Answer:** B  
**Why:** Domain 5 breaking changes + Domain 4 traces. Changing the grader to hide a regression is not recovery.
