# 05 — Agents and Workflows

**Exam:** Domain 1 (14.7%)  
**Skills:** Agent Architecture 4.5% · Agent Construction with Claude 5.3% · Agent Patterns and Frameworks 4.9%  
**Course map:** Agents and workflows (parallelization, chaining, routing, agents vs workflows, environment inspection); RAG and agentic search; Agent SDK / hooks; 4D Delegation.

---

## Study brief

### Workflow vs agent (the decision the exam wants)

| | **Workflow** | **Agent** |
|---|---|---|
| Control | **You** predefine the graph: step A then B then C | **Model** decides next actions in a **tool-use loop** |
| Predictability | High: known steps, easier eval | Lower: path emerges at runtime |
| Use when | Known procedure, SLAs, compliance, cheap | Open-ended goals, unknown tool sequence, research |
| Failure mode | Brittle if the world doesn’t match the graph | Loops, cost blowups, drift, unsafe tool calls |

**Rule:** if you can draw the steps and they do not depend on model judgment between them, it is a **workflow**. If the model must **inspect, choose tools, and iterate**, it is an **agent**. “Agent” is not a compliment; it is a control-plane choice.

**4D Delegation:** humans delegate *goals* to agents and *steps* to workflows. Over-delegating a determined pipeline to an agent wastes cost and adds injection surface.

### Workflow patterns (course)

| Pattern | Structure | Use |
|---|---|---|
| **Chaining** | Output of step 1 is input to step 2 (sequential) | Pipelines: extract → transform → write; each step a focused prompt |
| **Parallelization** | Fan-out independent calls; fan-in / merge | Map over docs; independent reviewers; speed when work does not share state |
| **Routing** | Classifier/router sends the request to a specialized prompt, model, or sub-graph | Intent-based support; Haiku router → Opus specialist |

**Gotcha:** parallelization requires **independence**. If step B needs step A’s output, that is chaining. Routing is not parallelization: it **selects one path**, it does not run all paths (unless you deliberately ensemble).

### Architecture: manager / supervisor / subagents

- **Manager / supervisor hierarchy:** a parent plans, delegates, integrates results. The parent may be a workflow (fixed fan-out) or an agent (dynamic delegation).
- **Subagents:** specialized workers with **their own context** (context isolation, Domain 6). They improve execution by shrinking the problem and keeping tools/prompts focused.
- Subagents are not “smaller models” by definition; they are **isolated task executors**. You may still pick Haiku vs Opus per subagent (Domain 5).

### Agent construction with Claude

Named in the blueprint:

| Piece | Role |
|---|---|
| **Claude Agent SDK** | Platform/toolkit for constructing agents (loop, tools, sessions) rather than ad-hoc `while` soup |
| **Custom agent loops / harnesses** | You write the dispatch loop (Messages + tools until stop). Same idea as “agentic harness dispatch” (Domain 8) |
| **Managed deployment: self-hosted vs Anthropic-hosted** | Where the loop runs and who operates it. Self-hosted: you own VPC, keys, scaling. Anthropic-hosted: less ops, more vendor constraint. Pick from **requirements** (Domain 2), not hype |
| **Hooks for deterministic actions** | Code that **always** runs on events (before tool, before write). Use for policy the model must not “forget” |

**Environment inspection:** agents should **look at the environment** (files, APIs, repo) instead of assuming. Course topic next to agents-and-tools.

### Agent patterns and frameworks

**Common patterns:**

- **Tool-use loops** — the core agent.
- **Sub-agents** — isolate and specialize.
- **Memory** — what you persist across sessions (notes, Agent Memory in Claude Code). Distinct from the raw context window.
- **Context-window management** — prune, compact, isolate (Domain 6). Agents die from bloat first.

**Abstraction frameworks named:** **Strands**, **LangGraph**, **PydanticAI** — graph/typed-agent frameworks for multi-step tasks. Exam: know they exist as **agentic abstraction frameworks**; you still must know workflow-vs-agent and tool loops underneath.

### RAG and agentic search (course)

Not its own exam domain; it is how agents get knowledge.

| Piece | Role |
|---|---|
| **Chunking** | Split docs so retrieval units fit the window and stay coherent |
| **Embeddings** | Semantic similarity search |
| **BM25** | **Lexical** (keyword) search; complementary to vectors |
| **Multi-index pipeline** | More than one index (e.g. BM25 + vector, or per-corpus indexes) then merge |
| **Contextual retrieval** | Enrich chunks with context so they retrieve better than raw slices |
| **Full RAG flow** | Query → retrieve → **put chunks in the prompt as data** → generate. Optionally **cite** (module 02) |

**Agentic search:** the model **issues queries/tools** (search, then refine) instead of a single retrieve-then-read. That is an **agent** (or a routing/chaining workflow if the query sequence is fixed).

**Gotcha:** retrieved text is **untrusted** (sample 2). RAG without isolation is an injection feature.

---

## Flashcards

### Card 1 — Workflow vs agent
**Q:** What is the decision criterion for workflow vs agent?
**A:** If **you** can predefine the step graph, use a **workflow**. If the **model** must choose tools/steps at runtime in a loop, use an **agent**.

### Card 2 — Why not always agent
**Q:** Why not make every pipeline an agent?
**A:** Agents add cost (multi-turn tokens), non-determinism, and a larger tool-abuse/injection surface. Workflows are better for known, auditable procedures.

### Card 3 — Chaining
**Q:** Define a chaining workflow.
**A:** Sequential stages: each step’s output is the next step’s input. One path, ordered dependencies.

### Card 4 — Parallelization
**Q:** When is parallelization valid?
**A:** When subtasks are **independent** (no data dependence). Fan-out then merge. If B needs A, chain instead.

### Card 5 — Routing
**Q:** What does a routing workflow do?
**A:** A router (often a cheap model) **selects** a specialist prompt/model/tool-graph. It is path **selection**, not by default “run all specialists.”

### Card 6 — Supervisor vs subagent
**Q:** Manager/supervisor vs subagent?
**A:** Supervisor **plans and delegates**. Subagents **execute isolated subtasks** with their own context/tools and return results.

### Card 7 — Why subagents improve execution
**Q:** How do subagents improve task execution (architecture skill)?
**A:** Isolation: focused tools/prompts, less bloat/drift, parallel specialist work, narrower failure domains.

### Card 8 — Agent SDK vs custom harness
**Q:** Claude Agent SDK vs a custom agent loop?
**A:** Both construct agents. SDK = supported toolkit. Custom harness = you own Messages + tool dispatch. Managed hosting is a separate axis (self vs Anthropic-hosted).

### Card 9 — Self-hosted vs Anthropic-hosted
**Q:** What is the real tradeoff in managed agent deployment models?
**A:** **Self-hosted:** control, integration with your VPC/compliance, more ops. **Anthropic-hosted:** less ops, platform constraints. Choose from requirements, not branding.

### Card 10 — Hooks in agents
**Q:** Why does agent construction list hooks?
**A:** Hooks run **deterministic** code on lifecycle events (e.g. block a tool). Policy that must not depend on the model “remembering.”

### Card 11 — Memory vs context window
**Q:** Is agent memory the same as the context window?
**A:** No. The window is the tokens on **this** call. Memory is **persisted** state you choose to reload. Unmanaged history is bloat, not memory.

### Card 12 — Named frameworks
**Q:** Name the agentic abstraction frameworks in the blueprint.
**A:** **Strands**, **LangGraph**, **PydanticAI** — used to build agents/workflows for multi-step tasks.

### Card 13 — BM25 vs embeddings
**Q:** BM25 vs embedding search in RAG?
**A:** BM25 = **lexical** keyword matching. Embeddings = **semantic** similarity. A **multi-index** pipeline often uses both.

### Card 14 — RAG vs agentic search
**Q:** Single-shot RAG vs agentic search?
**A:** RAG: retrieve then generate in a **fixed** flow (workflow). Agentic search: model **iteratively** queries/tools. Retrieved docs remain untrusted data.

### Card 15 — Environment inspection
**Q:** What is “environment inspection” next to agents and tools?
**A:** The agent should **observe** the actual environment (repo, files, APIs) rather than hallucinate state. Tools exist to look, then act.

---

## ABCD questions

**Q1.** Invoice PDF → extract fields → validate against a schema → write to DB. Steps never vary. Best architecture?
- A) A long-running Opus agent with all tools and extended thinking
- B) A **chaining workflow** with validation as code between steps
- C) Parallelization of extract, validate, and write
- D) Routing to a random model per invoice

**Answer:** B  
**Why:** Known, ordered procedure → **workflow**, specifically **chaining**. Parallelize only independent work; a free-form agent is the wrong control plane.

**Q2.** A support bot must handle refunds, tracking, and password resets with different tools and prompts. Traffic is mixed. Best pattern?
- A) One agent with every tool and a 20-page system prompt
- B) **Routing** to specialized workflows/agents; cheap classifier first
- C) Parallelize refund + tracking + reset on every ticket
- D) Message Batches API because volume might be high

**Answer:** B  
**Why:** Routing matches specialized paths. Parallelizing all three on every ticket is wasteful; one mega-agent is bloat; batch is a latency/cost API choice, not a dialogue architecture.

**Q3.** A research task needs unknown GitHub issues, docs, and web pages, with stop when “enough evidence.” Workflow or agent?
- A) Pure chaining with a fixed 2-step retrieve
- B) An **agent** (tool-use loop) with hooks/approvals on sensitive tools; optionally subagents for isolated lookups
- C) Stateless MCP HTTP only
- D) Zero-shot Haiku with no tools

**Answer:** B  
**Why:** Unknown step sequence and stopping criterion → **agent**. Subagents isolate lookups; hooks constrain tools. Fixed 2-step RAG is too rigid here.

**Q4.** Why add a subagent to a supervisor instead of stuffing another 15 tools into the parent?
- A) Subagents automatically use Message Batches
- B) **Context isolation** and a smaller tool surface improve execution and reduce drift/bloat
- C) The blueprint forbids parents from using tools
- D) Subagents remove the need for evals

**Answer:** B  
**Why:** Architecture skill: subagents improve execution via isolation. Evals still required; batch/API hosting are unrelated.

**Q5.** Your RAG stack has embedding search only and misses tickets that share exact error codes. Course-aligned fix?
- A) Disable chunking
- B) Add **BM25 lexical** search in a **multi-index** pipeline
- C) Raise temperature on generation
- D) Move the index into the system prompt as an MCP prompt primitive

**Answer:** B  
**Why:** Course: BM25 lexical + embeddings + multi-index. Temperature does not retrieve; MCP prompts are not an index.
