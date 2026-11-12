# Day-before exam — review list

Ordered by **exam weight × your mock gaps**. Goal: distinctions, not rereading everything.

**Sources:** blueprint weights (D2 33% → D4 3%) + misses from all hard mocks (75% → 86%).

**Rules for today**
- Cover the answer; say the distinction out loud.
- If you already know it cold, skip — don’t “comfort reread.”
- Last 90 min: light only. Sleep beats one more obscure SDK trivia item.

---

## Suggested schedule (~5–6 hours, or compress to 3)

| Block | Time | Focus |
|---|---:|---|
| A | 75 min | **D2 Messages / apps** (highest weight + many misses) |
| B | 45 min | **D1 Agents** |
| C | 40 min | **D8 Tools / MCP** |
| D | 35 min | **D6 Prompt / context / output** |
| E | 30 min | **D5 Models / cost** |
| F | 25 min | **D7 Security** |
| G | 30 min | **D3 Claude Code** (low weight, fresh misses) |
| H | 15 min | **D4 Eval / release** |
| I | 40–60 min | Cold ABCD: `mock-miss-drills.md` reds only |
| J | 20 min | Cram sheet hard-mock traps + 20-second chooser |

**3-hour compress:** A → B → C → F → I (reds) → J.

---

## A — D2 Applications & Integration (~33%) — PRIORITY

### Messages API surface (your early misses)
- [ ] Stateless: **resend history + tools** each turn
- [ ] Stream finale: **`message_delta`** → `stop_reason` + `usage` (not `content_block_start`)
- [ ] UX snappiness: **TTFT**, not retries/hour
- [ ] Spend attribution: **`metadata.user_id`** opaque, no PII — not custom headers
- [ ] Fit check: **token-counting endpoint** on **full** request (system + tools + images)
- [ ] Retry **429 / 529**; fix **400 / 401**
- [ ] Raw HTTPS: key + **`anthropic-version`** + JSON
- [ ] `stop_reason`: `end_turn` / `tool_use` / `max_tokens` / `stop_sequence` (know what each means)

### Features you missed on hard mocks
- [ ] Thinking UI: **`thinking_delta`** + **`signature_delta`** (not scrape `text_delta`)
- [ ] Thinking **display** = what you see (summarized / omitted), separate from thinking on/off
- [ ] PDFs: **documents before** instructions; **logical/viewer** pages
- [ ] Layout vision: send **image block**; OCR-only loses badges/buttons
- [ ] Overnight cost: stable prefix + **cache breakpoint** + **Batches** (~½); streaming ≠ cheaper output
- [ ] Fast mode ↔ cache: **separate** prefixes — toggling speed misses cache
- [ ] Structured outputs **400**: strip `minLength` / `maxLength` / `minimum`; keep `email`; validate in **app**
- [ ] Prefill **400** on newer models: drop prefill; “no preamble” in **system** prompt
- [ ] Files API = `file_id` reference; works with Messages/Batches; still point at the right file

### App / SDK (mock 3)
- [ ] Version **CLAUDE.md + project settings with application source** (same repo/PRs as the app)
- [ ] Agent SDK: register tools; **SDK harness** owns loop + state (don’t reimplement the loop)
- [ ] Skip undici ProxyAgent trivia — if proxy appears: client fetch/dispatcher, **not** `baseURL`

**Open:** cram Messages block · `02` cards 6b–6f · mock drills Q5, Q6, Q8, Q10–Q12, Q21–Q23, Q32/Q41 patterns

---

## B — D1 Agents & Workflows (~15%)

- [ ] Workflow (you graph) vs agent (model loop)
- [ ] Chain / parallel / route
- [ ] Must-never-fail → **hook / code gate**, not prompt or router
- [ ] Primary stop = **`end_turn`**; turn/token **cap = safety rail only**
- [ ] Loop: while `tool_use` → run tools → send **`tool_result`**
- [ ] Managed Agents: Anthropic = **loop + event history**; you = **sandbox + memory**
- [ ] Stale tool results: **context editing** +/or **subagent** (caching ≠ freshness)
- [ ] Context awareness: model already gets **budget/remaining** — no DIY token notes

**Open:** `05` brief · mock drills Q1, Q2, Q9, Q14, Q15

---

## C — D8 Tools & MCP (~11%)

- [ ] Wrong tool → expand **descriptions** first (when / when not / formats)
- [ ] MCP primitives: **tools + resources + prompts** (not “model checkpoints”)
- [ ] Strict: API enforces tool **input** schema; optional params share **~24 across all strict tools** in one request
- [ ] Fix complexity: make fields **required** and/or drop strict on non-critical tools
- [ ] Large MCP output: spill to **file**; `anthropic/maxResultSizeChars` for inline
- [ ] Team MCP: **project scope** → commit `.mcp.json` + tokens via **`${ENV}`** (never shared token in git)
- [ ] Pre-tool hook = block/approve **before** side effects; post-tool = audit/sanitize **after**

**Open:** `04` · mock drills Q7, Q13, Q16, Q17 · MCP project/env pattern

---

## D — D6 Prompt & context (~11%)

- [ ] Format consistency: **2–4 few-shots** before post-process-as-primary
- [ ] Repeatable extraction: temperature **near 0**
- [ ] System = durable policy; user/retrieved = **untrusted data**
- [ ] Structured final answer → **`output_config` / structured outputs**; tool args → **`input_schema`** (+ strict)
- [ ] Skill ≠ schema enforcement (Skill = procedure)

**Open:** `03` · mock drills Q18, Q19, Q23

---

## E — D5 Models / optimization (~17%)

- [ ] Opus / Sonnet / Haiku chooser (quality vs cost/latency)
- [ ] Fast vs extended vs adaptive thinking
- [ ] Prompt caching = stable **prefix**; check-pointing = boundaries
- [ ] Agents cost **turns × tokens**
- [ ] Pin model versions

**Open:** cram Models block · `01` briefly — fewer of your misses here; don’t over-invest

---

## F — D7 Security (~8%)

- [ ] Injection: page as **`tool_result`/data** + **app allowlist/human gate**
- [ ] System prompt alone ≠ control for payments/deletes
- [ ] Layered: bounds + least privilege + hooks + IAM
- [ ] Secrets: never CLAUDE.md / frontend / git

**Open:** `09` · mock drill Q4

---

## G — D3 Claude Code (~3%) — your latest misses

Low weight, but you just missed these — **15–25 min max**:

- [ ] Nested `packages/.../CLAUDE.md` loads **on demand** (not always in `/context` at launch)
- [ ] Plugins: **`claude plugin details`** (tokens) + **`/plugin` → Not used recently** → disable
- [ ] CLAUDE.md = prose; `settings.json` = permissions/hooks/model — version both with the app
- [ ] Hooks deterministic; Skills procedural; MCP shared tools

**Skip deep:** Computer Use minutiae unless you still feel weak.

---

## H — D4 Eval / release (~3%)

- [ ] Canary: small **live %** + reviewed accuracy + **rollback thresholds** + stage widen
- [ ] Shadow *agreement* ≠ correctness; offline expand alone ≠ live risk
- [ ] False positives: log **`detected_pattern` / what fired**, not a confidence float
- [ ] Code grade vs model grade

**Open:** mock drills Q3, Q20

---

## I — Cold drill (do this)

1. `mock-miss-drills.md` — only items you ever got wrong  
2. Say one line: “I almost picked X because ___; correct is Y because ___.”  
3. Target: ≤2 fresh misses on reds  

---

## J — Night-before 20 lines (read once)

1. Constraint first (cost / rollback / untrusted / residency / who owns loop).  
2. Prefer **named control** over invented machinery.  
3. Must-never → **hook**.  
4. Agent stop → **`end_turn`**.  
5. Wrong tool → **descriptions**.  
6. MCP → tools / resources / **prompts**.  
7. Team MCP → **project `.mcp.json` + `${TOKEN}`**.  
8. Nested CLAUDE.md → **on demand**.  
9. Plugins → **details + Not used recently**.  
10. Stream finale → **`message_delta`**.  
11. Tokens fit → **counting endpoint**.  
12. Overnight $ → **cache + Batches**.  
13. Strict optional → **shared ~24**.  
14. Structured 400 → strip min/max/length; validate in **app**.  
15. Prefill 400 → drop; system “no preamble.”  
16. Injection → **data channel + app gate**.  
17. Vision layout → **image block**.  
18. PDF → **doc first + logical pages**.  
19. Config → version with **application source**.  
20. Agent SDK → **SDK owns harness**.  

---

## Do not burn time on

- Exact undici / Bun proxy API names  
- Memorizing every stream delta type beyond text / thinking / signature / input_json  
- Re-watching long course videos  
- New rabbit holes after dinner  

**You’ve already moved 75% → 86% on hard banks. Tomorrow: slow on the constraint line, then pick.**
