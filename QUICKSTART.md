# Quickstart: Agentic Workflow

**Get to your first AI-supported workflow run in under 2 minutes.** For teams that want to use GitHub Copilot in a structured way.

> *“If you wish to make an apple pie from scratch, you must first invent the universe."*
> — Carl Sagan

## Prerequisites

- GitHub Copilot (Business or Enterprise)
- VS Code with Copilot Chat **or** GitHub Copilot CLI

## Setup (2 steps)

### 1. Copy the `.github/` folder

```bash
cp -r .github/ /path/to/your/project/.github/
```

Everything you need is in `.github/` — workflow rules, Skills, instructions.

### 2. Define the universe

```
/axiom
```

The Skill guides you through a Socratic dialogue: stack, architecture, constraints, NFAs, out-of-scope. The result is stored in `docs/` (project.md, architecture.md, domain.md) — readable by any agent tool.

## Your first workflow run

```bash
/analyze "I want to build a CSV import for customer data"
# → Agent makes the red spot precise

/next    # → /discuss  — clarifies gray areas and assumptions with you
/next    # → /research — researches remaining open questions
/next    # → /conceptualize — blue figures: solution options, you decide
/next    # → /design   — green figure: solution design + /brief → design.md
# → Phase 4: design.md to Sub-Agent (local) or Copilot Coding Agent (cloud)
# → Phase 5: /qa Fleet (all 5 reviews in parallel)
# → Phase 6: /retro
```

After each step, the agent tells you what comes next. `/next` starts it.

## The workflow chain at a glance

```
UNIVERSE:   /axiom  (defined once, read on every run)
🔴 RED:     /analyze → /discuss → /research
🔵 BLUE:    /conceptualize
🟢 GREEN:   /design → /brief → design.md
BUILD:      design.md → Sub-Agent (local) or Copilot Coding Agent (Cloud)
QA:         /qa Fleet (/simplify /test-review /review /sec-review /doc-review)
LEARNING:   /retro → /upstream
```

## 3 things you need to know

1. **Universe first** — `/axiom` defines the universe in `docs/` before the first task begins. It is read on every follow-up run. Undefined universe in — undefined output out.

2. **`/next` always runs the next Skill** — the workflow state is tracked in `plan.md`. You do not need to know which Skill is next.

3. **You steer, the agent executes** — no commit without your "commit", no implementation without your approval. The agent asks, you decide.

## Further reading

- **[README.md](README.md)** — Complete documentation of all Skills, rules, and architecture