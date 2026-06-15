# Quickstart: Agentic Workflow

**Get to your first AI-supported workflow run in under 2 minutes.** Works with GitHub Copilot CLI and Claude Code.

> *"If you wish to make an apple pie from scratch, you must first invent the universe."*
> — Carl Sagan

## Prerequisites

- **One of:** GitHub Copilot CLI **or** Claude Code (or both — dual-harness is supported)
- `git` available locally

## Setup (3 steps)

The workflow installs **once into your user scope** — then it's available in **every** project. No per-project install.

### 1. Clone this repo

```bash
git clone https://github.com/pesteph/agentic-workflow.git ~/tools/agentic-workflow
```

You can clone it anywhere — `~/tools/`, `~/src/`, wherever you keep tooling.

### 2. Open this repo in your harness and run `/downstream`

```
/downstream
```

Optional: `--harness=claude|copilot|both` (default `both`).

`/downstream` installs the methodology into your user scope:
- Workflow rules → `~/.claude/CLAUDE.md` (Claude) and/or `~/.copilot/copilot-instructions.md` (Copilot)
- Workflow Skills → `~/.claude/skills/` (Claude) and/or `~/.copilot/skills/` (Copilot)
- Skips Skills your harness already provides as built-ins

The same command installs and upgrades — re-run it after `git pull` to update every project at once.

### 3. Open any project and run `/axiom`

Because the Skills live in your user scope, they're available in **every** project. Open any project and run:

```
/axiom
```

`/axiom` guides you through a Socratic dialogue: stack, architecture, constraints, NFAs, out-of-scope. The result is stored in that project's `docs/` (`project.md`, `architecture.md`, `domain.md`).

## Your first workflow run

In any project:

```
/analyze "I want to build a CSV import for customer data"
# → Agent makes the red spot precise

/next    # → /discuss  — clarifies gray areas with you
/next    # → /research — researches remaining open questions
/next    # → /conceptualize — blue figures: solution options, you decide
/next    # → /design   — green figure: solution design + /brief → design.md
# → Phase 4: design.md to Sub-Agent (local) or cloud coding agent
# → Phase 5: /qa Fleet (review dimensions in parallel)
# → Phase 6: /retro
```

After each step the agent tells you what comes next. `/next` runs it.

## The workflow chain at a glance

```
UNIVERSE:   /axiom  (defined once, read on every run)
🔴 RED:     /analyze → /discuss → /research
🔵 BLUE:    /conceptualize
🟢 GREEN:   /design → /diaboli (optional, challenge concept) → /verify (completeness gate) → /brief → design.md
BUILD:      design.md → Sub-Agent (local) or cloud coding agent
QA:         /qa Fleet (5 review dimensions)
LEARNING:   /retro → /upstream
```

## 3 things you need to know

1. **Universe first** — `/axiom` defines the universe in `docs/` before the first task begins. It is read on every follow-up run. Undefined universe in — undefined output out.

2. **`/next` always runs the next Skill** — the workflow state is tracked in `plan.md`. You do not need to know which Skill is next.

3. **You steer, the agent executes** — no commit without your "commit", no implementation without your approval. The agent asks, you decide.

## Keeping skills up to date

```
/downstream    # from a clone of this repo: install + upgrade your user scope (re-run after git pull)
/upstream      # send your improvements back as a PR
```

## Further reading

- **[README.md](README.md)** — Complete documentation of all Skills, rules, and architecture
- **[AGENTS.md](AGENTS.md)** — Workflow rules and Skill overview (canonical)
