# 07 — Claude Code

**Exam:** Domain 3 Claude Code Operation **3.1%** (small weight, sharp distinctions)  
**Also:** Domain 1 hooks; Domain 2 CLAUDE.md/settings/plugins; Domain 7 Claude Hooks 1.0%; Domain 8 Skills vs tools vs MCP  
**Course map:** Claude Code setup/in action; MCP enhancements; steer long sessions; CLAUDE.md; verification skills; permission modes; hooks; routines/headless; GitHub Actions/code review; plugins; Skills curriculum; Computer Use as Anthropic app.

---

## Study brief

### Core components (memorize the set)

| Component | What it is | Not |
|---|---|---|
| **Rules** | Persistent constraints (often via CLAUDE.md / rule files) | Not a live API |
| **Skills** | Packaged procedures: `SKILL.md` + optional scripts/resources; **triggered by description match**; **progressive disclosure** | Not MCP; not always-on like all of CLAUDE.md |
| **Commands** | Slash commands: **built-in** and **custom** | Not tools unless they wrap them |
| **Agents / subagents** | Delegated isolated workers | Not Skills |
| **Agent Memory** | Persisted notes across sessions | Not the raw terminal log |

### Features

| Feature | Point |
|---|---|
| **Session management** | Resume, compact, new session — long-session steering |
| **Built-in vs custom slash commands** | Built-in = product; custom = team workflows |
| **Headless mode** | Non-interactive / CI / automations (“Routines and Headless”) |
| **Streaming mode** | Incremental output (UX/logs) |
| **Auto-mode** | Reduced prompting for permission/continuation — higher blast radius |
| **Repository initialization** | Make the repo Claude-ready (CLAUDE.md, settings, structure) |
| **settings.json** | Permissions, hooks, model — config not prose |

**Permission modes:** control what Claude Code may do without asking (filesystem, network, bash). Tighter in prod/CI for untrusted issues; looser for a local spike. **Least privilege** (Domain 7).

**Steering long sessions:** re-assert goals, compact, don’t let drift eat the window; verification skills check the agent’s work instead of rubber-stamping.

### CLAUDE.md hierarchy

CLAUDE.md is loaded from a **hierarchy** (user / project / org / enterprise-managed — treat as layered, more specific winning or merging per product rules). Exam: there **is** a hierarchy; put **repo norms** in project CLAUDE.md; put **personal** prefs at user level; **enterprise managed settings** for org-wide Skills/policies.

**A CLAUDE.md that follows:** short, imperative, testable rules. Not a novel. If Claude ignores it, it is too long, conflicting, or overridden by a Skill/hook/permission.

### Skills (curriculum in `../../resource/resource.md`)

Skills teach Claude **how your team works** once, instead of restating it every session.

**Vs other customization:**

| | Always in context? | Live side effects? | Trigger |
|---|---|---|---|
| **CLAUDE.md** | Largely yes (hierarchy) | No | Always-on instructions |
| **Skill** | **Progressive disclosure** (frontmatter/description first; body on match) | Optional **scripts** that run **without stuffing context** | Description match |
| **Hooks** | N/A (code) | **Deterministic** intercept | Events |
| **Subagents** | Isolated | Via their tools | Delegation |
| **MCP** | Via host | Yes (tools/resources) | Connected server |

**Build:**

- Directory with **`SKILL.md` frontmatter** (name, **description** — description is the trigger; write it so matching is reliable).
- Body: procedure. Keep context cheap via **progressive disclosure**.
- **`allowed-tools`:** restrict what the skill may use (least privilege).
- **Scripts:** execute without dumping their source into the window.

**Share:** git repo → **plugins** → **enterprise managed settings** org-wide.

**Wire into custom subagents** for isolated expert delegation.

**Troubleshoot:** skill won’t trigger (weak/wrong **description**), **priority conflicts** (CLAUDE.md vs skill vs other skills), **runtime errors** (scripts/tools).

### Hooks

Hooks = **deterministic** programs on events (pre-tool, pre-commit, stop). Use as **guardrails**: block `rm -rf`, force tests, prevent secrets from leaking.

Domain 7: hooks are a **safety control**, not a Skill. The model cannot “forget” a hook the way it can ignore a sentence in CLAUDE.md.

### Headless, CI, verification

- **Headless:** automations, **GitHub Actions**, code review bots.
- **Verify unsupervised runs:** never trust a green-looking agent; run tests, linters, evals, human review for high impact. Course: “Trust It: Verifying Unsupervised Runs.”
- **Computer Use:** separate Anthropic **app** for **UI automation** (screens, clicks). Not the same as Claude Code (repo/CLI agent). Both can be combined with MCP.

---

## Flashcards

### Card 1 — Component set
**Q:** List Claude Code’s named core components.
**A:** **Rules, Skills, Commands, Agents, Agent Memory.**

### Card 2 — Headless vs auto-mode
**Q:** Headless mode vs auto-mode?
**A:** Headless = **no interactive TTY** (CI/routines). Auto-mode = **fewer permission pauses** (higher autonomy). You can have headless with tight permissions.

### Card 3 — CLAUDE.md hierarchy
**Q:** Why does “CLAUDE.md hierarchy” matter?
**A:** User vs project vs org/enterprise layers combine. Put repo law in **project**; personal prefs in **user**; org policy in **enterprise managed** settings — don’t fight the wrong layer.

### Card 4 — settings.json
**Q:** What belongs in settings.json rather than CLAUDE.md?
**A:** **Configuration**: permission mode, hook registration, model pin. Not long-form coding standards (those are CLAUDE.md/Skills).

### Card 5 — Skill trigger
**Q:** What makes a Skill fire?
**A:** A **description** that matches the task (frontmatter). Bad descriptions → “skill won’t trigger.”

### Card 6 — Progressive disclosure
**Q:** Why progressive disclosure in Skills?
**A:** Keep the **context window efficient**: load the full SKILL body (and resources) **when matched**, not every Skill in the org on every turn.

### Card 7 — allowed-tools
**Q:** What does `allowed-tools` do on a Skill?
**A:** Restricts tool access for that Skill — least privilege so a formatting skill cannot hit prod APIs.

### Card 8 — Scripts vs context
**Q:** What is special about Skill scripts in the curriculum?
**A:** They **execute without consuming context** the way pasting the script would. Logic stays out of the window.

### Card 9 — Skills vs CLAUDE.md vs hooks vs MCP
**Q:** One-line chooser?
**A:** Always-on norms → CLAUDE.md. Procedural playbook on match → Skill. Must-never-fail policy → **hook**. Live shared integration → **MCP**. Isolated expert → **subagent**.

### Card 10 — Sharing Skills
**Q:** How do you distribute Skills?
**A:** Commit to a **repo**, bundle as **plugins**, push org-wide with **enterprise managed settings**.

### Card 11 — Skill troubleshooting
**Q:** Three Skill failure classes in the course?
**A:** **Won’t trigger** (description), **priority conflicts**, **runtime errors**.

### Card 12 — Hooks vs model memory
**Q:** Why use a hook to block destructive bash instead of a CLAUDE.md sentence?
**A:** Hooks are **deterministic**. CLAUDE.md is a prompt — ignorable, injectable, easy to lose in long sessions.

### Card 13 — Unsupervised verification
**Q:** What does “verifying unsupervised runs” require?
**A:** Tests, review, traces — **do not** treat headless/CI agent output as correct because it sounded confident.

### Card 14 — Computer Use vs Claude Code
**Q:** Computer Use vs Claude Code?
**A:** Computer Use = **UI automation** (screen/desktop). Claude Code = **development agent** in the repo/CLI. Different Anthropic apps; both may use MCP.

### Card 15 — Custom slash commands
**Q:** Built-in vs custom slash commands?
**A:** Built-in = product features. Custom = team-defined shortcuts/workflows in the repo/plugin.

---

## ABCD questions

**Q1.** A team repeats the same 40-line “how we write Terraform” every session. They also have a hard rule: never apply without tests. Best split?
- A) Put both in auto-mode
- B) Terraform procedure as a **Skill** (description trigger, progressive disclosure); apply-without-tests blocked by a **hook**; short pointer in CLAUDE.md
- C) MCP resource for the English paragraph
- D) Only Agent Memory

**Answer:** B  
**Why:** Skills curriculum vs hooks vs CLAUDE.md. Playbook = Skill; must-never-fail = hook; CLAUDE.md stays small.

**Q2.** A Skill never runs on “generate changelog.” The body is excellent. First debug step?
- A) Rewrite the git history
- B) Fix **SKILL.md description** so it matches how users phrase the task; check **priority conflicts**
- C) Switch stdio to StreamableHTTP
- D) Enable Message Batches

**Answer:** B  
**Why:** Troubleshooting guide: trigger matching and priority conflicts, then runtime errors.

**Q3.** Claude Code in GitHub Actions should review PRs but must not push to main. Which controls?
- A) Raise temperature
- B) **Headless** run + **permission mode** / hooks denying push; verify with CI checks
- C) Put “please don’t push” only in a retrieved README
- D) Adaptive thinking off

**Answer:** B  
**Why:** Headless + permission modes + hooks + verifying unsupervised runs. Untrusted README is not a control (Domain 7).

**Q4.** Why not paste 15 Skill bodies into CLAUDE.md?
- A) CLAUDE.md cannot contain markdown
- B) You lose **progressive disclosure** → **context bloat**; Skills exist to load on match
- C) Enterprise settings forbid CLAUDE.md
- D) Skills only work with Computer Use

**Answer:** B  
**Why:** Skills curriculum: progressive disclosure keeps windows efficient. CLAUDE.md always-on is the wrong store for a library of playbooks.
