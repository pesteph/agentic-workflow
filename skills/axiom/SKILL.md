---
name: axiom
description: Defines the universe of the project through Socratic dialogue. Writes the result into docs/. Use this Skill once at the start of a project or when the stack/constraints fundamentally change.
---

# Axiom — Read or Define the Universe

> *“If you wish to make an apple pie from scratch, you must first invent the universe."*
> — Carl Sagan

You read or define the project's **universe**: architecture, stack, constraints, NFAs, out-of-scope.

**Every task begins with `/axiom`.** The flow:
1. Read `docs/project.md` + `docs/architecture.md` + `docs/domain.md`
2. If a complete universe is already there → context is set, the Skill ends immediately with a short confirmation
3. If the universe is missing or incomplete → Socratic dialogue, then record it

The universe belongs to the project, not the task. It changes only when the stack or constraints fundamentally change — not through `/retro` (which improves the workflow) and not through individual tasks.

## Documentation Architecture

The universe lives in `docs/` — not in a harness-specific instruction file. This keeps it readable by any tool.

```
docs/
├── project.md             ← What + Why (Goals, Constraints, Scope, NFAs, Out-of-scope)
├── architecture.md        ← How it is built (Stack, components, patterns, folders)
├── domain.md              ← Domain terms, business rules, glossary
└── decisions/             ← ADRs in MADR format (filled by /design)
    └── 0001-*.md
```

**Responsibilities:**

| File | Question it answers | Who writes it |
|------|----------------------|---------------|
| `docs/project.md` | What does the system do and why? | `/axiom` |
| `docs/architecture.md` | How is it built? | `/axiom` (foundation), `/design` (detailed updates) |
| `docs/domain.md` | What do the domain terms mean? | `/axiom` (foundation), tasks (extend with new concepts) |
| `docs/decisions/` | Why was X decided? | `/design` (ADRs) |

**Relationship to harness files:**
- `.github/copilot-instructions.md` stays minimal — language, doc reference to `docs/`, workflow reference to `instructions/`. No universe content.

## Execution

This Skill is executed by the **Main Agent itself** — a Socratic dialogue requires direct contact with the user. No Sub-Agent for the dialogue part.

**Fast path:** If `docs/project.md` already exists AND is complete (constraints, NFAs, scope defined) → output a short confirmation ("Universe loaded: [stack], [N] constraints, [N] NFAs") and end the Skill. No dialogue needed.

Writing the docs can be delegated to a Sub-Agent — with explicit instructions about which files should be created/updated.

## Model

Opus — Socratic dialogue requires deep understanding of system relationships.

## Approach

### Step 1: Gather facts

Read from project files (Rule 20 — NEVER assume technical facts):
- `package.json`, `*.csproj`, `go.mod`, `pyproject.toml`, `Cargo.toml` → stack, runtime, version
- `README.md`, `docs/` → existing documentation
- `.github/copilot-instructions.md` → harness instructions (if present: do NOT change)
- Codebase structure (top-level directories, main components)

### Step 2: Socratic dialogue

Have a focused dialogue with the user about **gaps** you cannot derive from the facts. Maximum 3–4 questions per round. Topics:

**System boundaries (what is IN scope):**
- What does this application do? What is its core purpose?
- Which subsystems / services belong to it?
- Which data sources / external systems are connected?

**Constraints (hard boundaries):**
- Which constraints are absolutely non-negotiable? (compliance, scaling, latency, data storage)
- Which technologies / patterns are explicitly FORBIDDEN?
- Which dependencies may NOT be changed?

**NFAs (non-functional requirements):**
- Scaling: stateless / single-instance / horizontal scaling?
- Latency requirements?
- Privacy / GDPR / compliance?
- Deployment environment (cloud/on-prem/hybrid)?

**Out of scope:**
- What should the agent explicitly NOT touch?
- Which areas are “legacy baggage” that should not be changed?
- Which features are intentionally excluded from scope?

**Conventions:**
- Which coding conventions apply (naming, file structure)?
- Build & test commands?
- Commit conventions?

**Dialogue rules:**
- Open questions the user cannot answer → document explicitly as UNKNOWN (no placeholder!)
- If the user says “that is still unclear” → ask whether it is an intentional decision or something to be defined later
- No assumptions (Rule 13) — every statement needs a source (Rule 14)

### Step 3: Output as Plain HTML

Show the user the documented universe as **Plain HTML** (Rule 28) — for review before it is saved. The HTML contains:
- Summary of all findings
- All constraints visually highlighted
- Open points / UNKNOWNs explicitly marked
- Preview of file assignment (which content goes into which file)

### Step 4: Write docs

After user approval: create/update the `docs/` structure.

**If `docs/` does not yet exist:** create the full structure (all 3 files + `decisions/` directory).
**If `docs/` already exists:** only add missing/incomplete sections, keep existing content.

#### `docs/project.md`

```markdown
# Project: [Name]

*Defined with /axiom on [Date]. Last updated: [Date].*

## Vision & Goals

- Primary goal: [Core purpose]
- Quality goals (top 3): [...]

## Scope

### In Scope
- [...]

### Out of Scope
- [...]

## Constraints (hard boundaries — NOT negotiable)

- [Constraint 1]
- [Constraint 2]

## Non-Functional Requirements

| Area | Requirement | Source |
|---------|-------------|--------|
| Scalability | [e.g. stateless, horizontally scalable] | User |
| Latency | [e.g. < 200ms p99] | User |
| Compliance | [e.g. GDPR, SOC2] | User |

## Stakeholders

| Who | Expectation |
|-----|-----------|

## Open Points / UNKNOWN

- [What is not yet defined — no placeholder, explicitly marked as UNKNOWN]
```

#### `docs/architecture.md`

```markdown
# Architecture

*Last updated: [Date]*

## Stack

[Read from project files — framework, runtime, version, key dependencies]

## System Context (C4 L1)

[Mermaid diagram or ASCII: external actors, system boundary]

## Component Map (C4 L2)

[Key modules/services and their relationships]

## Key Patterns & Decisions

- Pattern: [Name] → used in [Where] because [Why]

## Data Flow

[Critical paths as numbered steps]

## Folder Structure

[Annotated directory tree — top-level only]
```

#### `docs/domain.md`

```markdown
# Domain Knowledge

*Last updated: [Date]*

## Core Concepts

[Key domain entities with definitions]

## Business Rules

[Rules that influence implementation decisions]

## Glossary

| Term | Definition | Context |
|---------|-----------|---------|

## External Systems & Integrations

[What is integrated and why]
```

## Output

```
## Workflow State (update in plan.md)
- Completed Skill: /axiom
- Result: [Universe documented in docs/ — N constraints, N NFAs, N out-of-scope]
- Next Skill: /analyze (if a task already exists) or wait for user input
- Context for next Skill: Universe is in place — docs/ as context for all subsequent Skills
```

💡 Context maintenance: After the dialogue, the context window is full. Consider context compaction before the first task — plan.md must be up to date first.

---

**Next step:** The universe is now defined. Start the first task: `/analyze "[Task description]"`