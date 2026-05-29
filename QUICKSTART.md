# Quickstart: Agentic Workflow

**Get to your first AI-supported workflow run in under 2 minutes.** Works with GitHub Copilot CLI and Claude Code.

> *"If you wish to make an apple pie from scratch, you must first invent the universe."*
> — Carl Sagan

## Prerequisites

- **One of:** GitHub Copilot CLI **or** Claude Code (or both — dual-harness is supported)
- `git` available locally

## Setup (3 steps)

### 1. Clone this repo

```bash
git clone https://github.com/pesteph/agentic-workflow.git ~/tools/agentic-workflow
```

You can clone it anywhere — `~/tools/`, `~/src/`, wherever you keep tooling.

### 2. Open this repo in your harness and run `/install-workflow`

```
/install-workflow /path/to/your/project
```

Optional: `--harness=copilot|claude|both` (default `both`).

`/install-workflow` installs the methodology in your target project:
- Workflow rules → `AGENTS.md` at your project root
- Workflow Skills → `.github/skills/` (Copilot) and/or `.claude/skills/` (Claude)
- Harness entry points (`copilot-instructions.md` / `CLAUDE.md` / `settings.json`)
- Skips Skills your harness already provides as built-ins

### 3. Define the universe

Open your target project and run:

```
/axiom
```

`/axiom` guides you through a Socratic dialogue: stack, architecture, constraints, NFAs, out-of-scope. The result is stored in `docs/` (`project.md`, `architecture.md`, `domain.md`).

## Your first workflow run

In your target project:

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
🟢 GREEN:   /design → /diaboli (optional, challenge + completeness) → /brief → design.md
BUILD:      design.md → Sub-Agent (local) or cloud coding agent
QA:         /qa Fleet (5 review dimensions)
LEARNING:   /retro → /upstream
```

## 3 things you need to know

1. **Universe first** — `/axiom` defines the universe in `docs/` before the first task begins. It is read on every follow-up run. Undefined universe in — undefined output out.

2. **`/next` always runs the next Skill** — the workflow state is tracked in `plan.md`. You do not need to know which Skill is next.

3. **You steer, the agent executes** — no commit without your "commit", no implementation without your approval. The agent asks, you decide.

## Keeping skills up to date

After your first cycle, from your target project:

```
/downstream    # pull latest Skills from agentic-workflow repo
/upstream      # send your improvements back as a PR
```

## Further reading

- **[README.md](README.md)** — Complete documentation of all Skills, rules, and architecture
- **[AGENTS.md](AGENTS.md)** — Workflow rules and Skill overview (canonical)
