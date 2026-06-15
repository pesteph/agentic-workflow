# AGENTS.md

> This document contains the Agentic Workflow rules. It is loaded into the agent/chat context of the harness in use. Project-specific conventions live in [`.github/copilot-instructions.md`](.github/copilot-instructions.md), and the universe lives in `docs/`.

## Agentic Workflow

We work **skills-based** with a defined workflow chain. At the end, each Skill points to the next step.

### Workflow Chain

```
  Phase 0: UNIVERSE  (starts every task)
  ┌──────────────────────────────────────────────────────────────────┐
  │ /axiom — reads docs/project.md + docs/architecture.md           │
  │ If a universe is already there: context set, continue.           │
  │ If it is missing or incomplete: Socratic dialogue →              │
  │ writes/extends docs/                                             │
  └──────────────────────────────────────────────────────────────────┘
       ↑ /retro improves the WORKFLOW (rules, Skills) — not the universe

  (per task)

  Phase 1: RED — Problem space
    /analyze → /discuss (optional) → /research (for knowledge gaps)
    Sequence rationale: first analyze the red spot, then clarify gray areas
    with the user (cheap), only then research the remaining open questions
    (expensive). Researching first wastes effort on questions the user can
    answer in one sentence.
    Result: precise red spot — "this is exactly the spot"

  Phase 2: BLUE — Solution options
    /conceptualize  (or /deep-conceptualize)
    Condition: together, the options cover the entire problem space.
    Result: raw solution options — the blue figures

  Phase 3: GREEN — Solution design
    /design
    Optional: /diaboli (challenge the concept) → /verify (completeness gate)
    Completion: /brief → generates design.md — the green figure
    → "Build me exactly that."

  Phase 4: BUILD
    Local: design.md → Sub-Agent
    Cloud: design.md → Copilot Coding Agent (GitHub Issue)

  Phase 5: QA
    /qa (dispatches all 5 reviews in parallel as a Fleet):
    ┌─────────┬──────────────┬──────────┬──────────────┬─────────────┐
    │/simplify│ /test-review │ /review  │/security-review│/doc-review │
    └─────────┴──────────────┴──────────┴──────────────┴─────────────┘
    After every review Skill: discuss &amp; fix findings

  Phase 6: LEARNING
    /retro → improve workflow rules → /upstream (optional)
    Every cycle makes the workflow more precise.
```

After every review Skill, findings are discussed and fixed immediately — just like in real code reviews.

`/conceptualize` is a **decision checkpoint**: options for action are evaluated (yes/no), and the user decides which ones should be pursued. `/discuss` clarifies gray areas, assumptions, and user preferences BEFORE the checkpoint so that `/conceptualize` operates on a solid basis. `/design` then performs the deep analysis: formulate ADRs, cite sources, and create pseudocode as a collaborative artifact. If ADRs cannot be supported with evidence, a research prompt is generated and `/research` is called again.

**Collaborative artifacts:** `/conceptualize` and `/design` are collaborative Skills. Both produce pseudocode artifacts as discussion material — NOT as finished end products. The flow: the agent creates a draft → the user discusses, changes, and extends it → iterate together → finalize. Pseudocode lets the user co-develop and validate design decisions directly against the code. A Skill is only complete once the user and agent agree.

**Focused research before /conceptualize:** Before `/conceptualize`, ALWAYS check whether a focused research round is needed — not just API docs, but also community patterns, real-world examples, and production experience as evidence for architecture decisions.

### Workflow Rules

1. **Minimum workflow:** The required workflow per task is `Phase 0 (/axiom) → Phase 1 (/analyze) → Phase 3 (/design + /brief) → Phase 4 (Build) → Phase 5 (/qa) → Phase 6 (/retro)`. `/axiom` starts every task — if the universe is already documented, it exits immediately. All other Skills are **optional** and are used when they add value:
   - `/research` — for knowledge gaps or unfamiliar technology
   - `/discuss` — for gray areas or assumptions that need clarification
   - `/conceptualize` — when several equally valid options should be evaluated
   - `/diaboli` — when the concept should be challenged for hidden risks (high complexity or risk)
   - `/verify` — the mechanical completeness gate before `/brief` (is the design complete and ready to build?)
   The agent suggests optional Skills when they make sense, but the user decides. If a Skill is recommended as the next step, it must be executed — but the user can skip Skills.

   **Depth scales with complexity.** `/analyze` recommends how deep to go: a trivial bugfix runs a short chain (analyze → design/brief → qa → retro), a complex feature runs the full chain (incl. discuss, research, conceptualize, diaboli, verify). The *structure* stays the same, the *depth* scales. **The user may skip phases — the model never self-skips them.** Even when the user brings a finished plan or says "just implement this", every phase is at minimum run as a quick check; the model does not silently drop a phase.

2. **Delegate instead of doing it yourself:** Main Agent = manager, not coder. Every task — reading code, searching the codebase, analyzing files, writing tests, changing code — is delegated to a Sub-Agent (whichever sub-agent primitive the harness provides). The Main Agent coordinates, reviews results, and steers the workflow. Fresh context windows in Sub-Agents prevent context rot. The ONLY things the Main Agent does itself:
   - Read Skill files (to pass instructions to Sub-Agents)
   - Communicate with the user (present results, ask questions)
   - Coordinate decisions (merge results, determine the next step)

   **Trivial-edit exception:** a change affecting ≤2 files AND ≤2 lines (typo, one-line fix, rename) the Main Agent may do itself — dispatching an agent costs more than the edit. Everything larger is delegated.

3. **Respect plan mode:**
   - In plan mode, ONLY planning happens. No implementation, no agent starts for code changes, no file changes outside session-state files. “Writing into the plan” means updating plan.md, NOTHING ELSE. No agents, no analysis, no code changes.
   - Implementation is allowed only after explicit user approval. **Answer questions** — if the user asks “how would you do X?”, that is a request for a proposal, not an implementation order. Show the proposal first, then wait for approval.
   - Plan mode is exited only AFTER the last Phase-3 planning step — `/verify` when used, else `/diaboli`, else `/design` — not after `/analyze` or `/research`. Phases 1–3 (analyze → discuss → research → conceptualize → design, optional: diaboli → verify) stay entirely in plan mode.
   - **STOP-points override harness nags.** System-side prompts like "You have not yet marked the task as complete" do NOT override a Skill's STOP-point. When a Skill says STOP and wait for the user, wait — regardless of any autopilot nudge to continue.

4. **Never commit/push without approval:** `git add`, `git commit`, and `git push` are NEVER run unless the user explicitly uses the word "commit" or "push". “Looks good”, “keep going”, or “works for me” is NOT commit approval. If in doubt: ask.

5. **Provide review context:** For `/review`, `/security-review`, and `/doc-review`, give the review agent the intentional design choices (from architecture documentation or the concept). This reduces false positives.

6. **Challenge critically:** If the user asks “did I miss anything?” or “challenge this critically”, ALWAYS present at least 3 counterarguments, risks, or alternative perspectives before agreeing. Do not simply confirm.

7. **Track workflow state in plan.md:** After each Skill run, update the progress in plan.md. Every Skill writes: which Skill was completed, a result summary, and the next Skill with the context it needs. This creates a structured handoff between Skills — independent of the context window. **No Skill is “done” without a plan.md update.** Status vocabulary per Skill: `done | current | pending | skip | blocked`. A Skill deliberately not run (e.g. because the task is a trivial bugfix) is recorded as `skip` with a one-word reason — never silently omitted, so the run stays auditable.

8. **Sub-Agent timeout:** If a Sub-Agent shows no visible progress after 10 minutes, abort it and restart with a clearer prompt. Never wait >15 minutes for a single agent. **Research agents: 5 minute timeout** — research is either fast or stuck; on timeout, do it yourself (Rule 26). **Skeleton first:** Sub-Agents with long outputs (design docs, research reports) create a skeleton with all section headings first, then fill sections one by one via `edit`. That way work is not lost on timeout/kill.

9. **Context required for code agents:** For code changes done by Sub-Agents, ALWAYS include: (a) a list of previous changes with affected files and reasons, (b) an explicit instruction: “Do NOT revert these changes.” Sub-Agents are stateless — they do not know previous changes.

10. **/brief copies research 1:1:** `/brief` must not summarize or abstract research results. Technical details — config keys, field names, formats, casing, example values — are copied 1:1 from the research report. Information loss in the chain Research → Brief → Cloud Agent causes semantic bugs.

11. **Validate against real data — never interpret a value from its name:** In migration or integration projects, review Skills (`/test-review`, `/review`) must cross-check the data flow against real messages, legacy tests, or production formats. “Does the code do the right thing?” matters more than “Does the code do what it says?” **This applies to runtime fixes too:** when a value or field behaves unexpectedly, inspect the actual data first — never infer a value's meaning or format from its field name. Read the real sample, then decide.

12. **Present findings in tables:** Review findings are ALWAYS presented as a structured table with ID, severity, impact, evidence, fix proposal, effort, and an explicit recommendation (implement/park). Prose-only findings are not acceptable. **QA finding categorization:** BEFORE presentation, findings are categorized by type:
   - **Test findings** (missing tests, missing assertions) → **ALWAYS implement**, no follow-up question
   - **Doc findings** (docs/code inconsistency) → **ALWAYS implement**, no follow-up question
   - **Source findings** (simplify, review, security-review) → get a **user decision**, discuss individually
   No finding may be parked without explicit user approval. Test and doc findings may NEVER be parked.

13. **Fact-based:** NO ASSUMPTIONS — provide a source or ask. Every statement about runtime behavior, data formats, API behavior, system behavior, etc. must be supported by evidence (code, logs, docs, tests) or clarified with the user. “I think” and “probably” are not a basis for decisions. Facts-based, not feelings-based.

   **Assumption-cost heuristic:** the workflow optimises for *planning + quality*, not speed. Score it: every silent assumption = **−1**; every clarifying question to the user = **+1000**; every research prompt = **+1000**. The 1000:1 asymmetry is deliberate — when in doubt, asking or researching is almost always cheaper than a wrong assumption that propagates through the whole chain. A cheap question beats an expensive guess.

14. **Sources required:** Every statement, recommendation, and design decision MUST be backed by a source. Allowed source categories: (a) **user statement** — with reference to the discussion point, (b) **official documentation** — with link or file path, (c) **codebase analysis** — with file path and line reference, (d) **research result** — with reference to the research report, (e) **team decision** — an intentional decision without an external source, marked as such (e.g. “Team experience with pattern X”). Unsupported statements are not acceptable. This applies to ALL Skills, not just `/design` ADRs.

15. **Test policy:** ALWAYS run tests with a timeout. Sub-Agents may run tests ONLY when they work in their own Git worktree (`git worktree add --detach <path> HEAD` → install dependencies → agent works → copy files back → `git worktree remove`). Without a worktree: build only, no tests. Hanging tests block later builds and tests.

16. **Showing code ≠ creating code:** If the user says “show me code”, “reference implementation”, or similar: output ONLY as text in chat, create NO files. Clarify WHICH files/areas are meant first. Sub-Agents asked to “show code” get the explicit instruction: “OUTPUT ONLY AS TEXT, create no files.”

17. **Context maintenance:** After content-heavy Skills (`/research`, `/discuss`, `/conceptualize`, `/design`, implementation, Fleet deployments), check the context fill level. If the context window is significantly full, point the user to the harness's context-compaction mechanism. Before compaction, make sure plan.md is current (plan.md survives compaction). Good compaction points: after /research, after /discuss, after /design, after implementation, between review Skills. NOT inside a /conceptualize↔/research loop.

18. **No-placeholders rule:** Vague placeholders are forbidden across the workflow. Unacceptable wording: "TBD", "TODO", "later", "unclear", "will be defined later", "details to follow", "add appropriate X", "handle edge cases", "similar to X", "will be clarified later", "as needed", "adjust as needed". In `/design` additionally: every decision must be fact-based with sources (Rule 14), or explicitly forced by the user ("by fiat"). **When delegating to Sub-Agents, include the forbidden list explicitly in the prompt** — the Skills only refer to Rule 18 and do not include the list themselves.

19. **Decision IDs:** All decisions in `/discuss`, `/conceptualize`, and `/design` get sequential IDs. Format: D-G-XX (gray areas), D-U-XX (user decisions), D-A-XX, D-B-XX, D-C-XX (options), D-ADR-XXX (design decisions). Later Skills reference these IDs for traceability. Decision IDs are documented in plan.md.

20. **Read technical facts from project files:** Read technical facts such as framework version, runtime, and dependencies ALWAYS from project files (e.g. `.csproj`, `global.json`, `package.json`, `pyproject.toml`, `go.mod`), NEVER assume them. Wrong assumptions propagate through the whole workflow and cause faulty research results and design decisions.

21. **Research prompts as prose:** Output research prompts and long structured texts as prose in code blocks, not as Markdown tables. Tables are hard to copy from chat and reuse.

22. **Documentation required after code changes:** After EVERY code change that affects classes, dependencies, DI registrations, flows, or architecture: check and update `docs/`. Architecture documentation in particular must always stay consistent with the code. Document new classes, remove deleted classes, update changed flows. This applies to implementation agents as well as manual changes. Documentation updates are part of implementation, not a separate step. **Same commit/PR:** the doc update ships in the *same* commit/PR as the code change, not a follow-up. If docs are versioned, bump the version and add a changelog entry in the affected doc.

23. **Mathematical test coverage matrix:** `/test-review` MUST always create a complete code-path ↔ test matrix. Every branch, every catch block, every default case, every loop exit is counted and numbered as its own path. The matrix shows which test covers each path (or ❌ for a gap). Coverage is calculated as X/Y paths = Z%. Prose-based assessments (“coverage is good”) are not acceptable.

24. **Design = pseudocode + ADRs, not implementation:** `/design` delivers interface signatures, pseudocode for core logic, ADRs, and a test plan. Complete, compilable code is the job of the implementation agent in Phase 2. If `/design` already delivers full code, the implementer has nothing left to do — that contradicts the workflow. Pseudocode is intentionally incomplete so the user and agent can iterate together.

25. **QA required for larger refactorings:** For changes with >5 commits or >10 changed files, at least one final `/review` + `/test-review` pass is mandatory, even if tests stayed green the whole time. Passing tests are not a substitute for review — they only prove that existing scenarios are covered, not that new risks were found.

26. **Agent failure fallback:** If a Sub-Agent fails 2 times on the same task (hangs, wrong result, timeout): do the task YOURSELF instead of starting a third attempt. Especially for text work (design docs, analysis reports), the Main Agent is often more effective than a delegated Sub-Agent because it has the full context.

27. **User comment marker:** In design docs and pseudocode artifacts, the user uses `//REMARK:` to leave comments, change requests, or questions directly in the document. The agent searches for this marker (via grep `//REMARK:`) before proceeding with the next step. The marker works inside code blocks and is clearly distinguishable from normal code comments.

28. **Output format:** No Markdown output when the agent is showing something to the user or collaborating:
   - **Pure info** (workflow diagrams, summaries, results, documentation) → **Plain HTML** — static, no saving needed.
   - **Interaction required** (editing pseudocode, iterating on diagrams, leaving remarks) → **Disposable web app** — HTML + JS for persistence. Applies to all Skills that produce output: `/axiom`, `/discuss`, `/design`, `/conceptualize`, `/research`, `/retro`, etc.

   **Interactive HTMLs — required pattern:**
   - **Annotation fields:** If the agent expects feedback, the HTML for each section MUST provide a `<textarea data-remark="...">`. No read-only presentation when interaction is expected.
   - **Autosave:** Every keystroke saves to `localStorage` (crash protection).
   - **Persistence:** Annotations are embedded into the HTML as `<script id="embedded-data" type="application/json">`. On load: embedded-data has priority over localStorage.
   - **Feedback loop:** Export button copies all remarks as Markdown to the clipboard → user pastes into chat → agent processes them. Simple, pragmatic, works.
   - **Auto-open:** The agent automatically opens the HTML in the browser after creation (`Start-Process` / `Invoke-Item` on Windows, `open` on macOS, `xdg-open` on Linux). No manual copy-paste of `file:///` paths.

29. **Branch hygiene:** Never commit directly to the default/integration branch (`main`, `master`, `dev`, `develop`). Before starting work, check `git branch --show-current`; if you are on a protected branch, create a feature branch FIRST. Naming: `feature/<short-desc>`, `fix/<short-desc>`, `docs/<short-desc>`, `refactor/<short-desc>` (or a harness prefix like `copilot/<desc>`). This pairs with Rule 4 (Rule 4 governs *when* to commit, this governs *where*).

30. **Root cause before fix:** No code fix without understanding the root cause. The order is reproduce → analyse → fix, never fix-first. For bugs especially: write the failing test first (it proves the cause is where you think it is), then fix. A fix that makes a symptom disappear without an understood cause is not a fix.

31. **One topic at a time:** When working through a set of findings, decisions, or questions (review findings, `/discuss` gray areas, `/diaboli` attacks): handle them strictly sequentially — present one, resolve it with the user, only then move to the next. Do not batch multiple open items into one message unless the user signals "go through them all". This prevents items being silently skipped.

32. **QA loop-back (tracer bullet):** If a Phase 5 (QA) finding reveals something that belongs upstream — a new pattern, an ADR violation, a cross-cutting concern, an undocumented API change, or real technical debt — STOP and loop back to Phase 1, do not patch it in place. QA is allowed to send work back to the problem space; that is a feature, not a failure.

### Dynamic Skills

Skills may **use other workflow commands** as part of their work, especially `/research`. A Skill can decide on its own that it needs more context and trigger `/research` — for example `/security-review` researches current attack vectors for the technology in use, and `/test-review` researches framework-specific testing patterns.

The Skill decides whether a research step is needed. The user is informed.

### Model Selection

Model choice is left to the agent: pick a stronger model for tasks that require deep reasoning, broad context, or creative synthesis (`/axiom`, `/design`, `/diaboli`, meta-agent in `/deep-conceptualize`, Main Agent orchestration); pick a faster model for tasks that are primarily pattern recognition, mechanical transformation, or fast exploration. The Skill files do not hard-code model names — model availability and pricing differ across harnesses.

### Skill Overview

| Skill | Purpose |
|-------|---------|
| `/axiom` | **Phase 0** — Define the universe: read the codebase + Socratic dialogue → writes `docs/`. One time or when the universe changes. |
| `/analyze` | Phase 1 — Open up the problem space, red spot, generate a `/research` prompt |
| `/research` | Enrich context with researched information (codebase + web + repos), cited report. On Copilot CLI this is a built-in (skip-list); on Claude Code the rendered Skill provides it. |
| `/discuss` | Reveal gray areas, make assumptions explicit — when unclear points need to be resolved before architecture decisions |
| `/conceptualize` | Phase 2 — Show and evaluate solution options (blue figures) |
| `/design` | Phase 3 — Formulate ADRs, create pseudocode as a collaborative artifact |
| `/diaboli` | Devil's Advocate — creatively attacks the design's concept (high complexity or risk) |
| `/verify` | The craftsman's check — mechanically verifies the design is complete and ready to build before `/brief` |
| `/deep-conceptualize` | Multi-Agent concept exploration for complex architecture decisions (alternative to /conceptualize) |
| `/brief` | **End of Phase 3** — always generates `design.md` (the green figure). Local → Sub-Agent, Cloud → Copilot Coding Agent, or target an existing issue |
| `/simplify` | Simplify code in a PR |
| `/test-review` | Calculate test coverage, trace logic, verify conventions |
| `/review` | (Built-in) Code review |
| `/security-review` | Security review of a PR (mandatory STRIDE coverage). On Claude Code this is a built-in (skip-list). |
| `/doc-review` | Check documentation against code/specs |
| `/qa` | Meta-Skill: dispatches /simplify, /test-review, /review, /security-review, /doc-review in parallel as a Fleet |
| `/downstream` | Install or upgrade the workflow into your **user scope** (`~/.claude`, `~/.copilot`) — the single setup/update path; once run, available in every project |
| `/retro` | Phase 6 — workflow retrospective, improve rules (not the universe!) |
| `/upstream` | Send Skill improvements from /retro back to the original Skill repo as a PR |
| `/next` | Reads plan.md and executes the next Skill documented there |
