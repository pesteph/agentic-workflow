---
name: analyze
description: Analyzes a GitHub issue, opens up the problem space, and generates a /research prompt. Use this Skill when an issue or task needs to be analyzed.
---

# Analyze

You analyze a GitHub issue or a task and open up the problem space.

## Execution

**Delegate** the analysis to a Sub-Agent. Give it the full Skill instructions and the user context. Show the user the complete result.

## Approach

1. **Read and understand the issue/task** — Read the issue completely, including comments and linked resources.
2. **Establish context** — Examine the affected code, the architecture, and relevant dependencies.
3. **Open up the problem space** — Identify:
   - What is the core problem?
   - Which components are affected?
   - Which boundary conditions and constraints exist?
   - Which risks and unknowns exist?
   - How large is the impact? (number of affected files/services/APIs, local vs. cross-cutting)
   - Complexity estimate: S/M/L/XL based on affected components and unknowns
4. **Formulate the problem definition** — Summarize the problem precisely.
5. **Generate a research prompt** — Create a **fully written out** research prompt for `/research`. The prompt must be copy-paste ready: as prose in a code block, no keyword list, no table. The user should be able to use the prompt for `/research` directly without rework. The prompt includes: project context (stack, version from project files such as `.csproj`, `package.json`, etc.), precise numbered questions with concrete information needs, and the desired evidence type (doc links, NuGet statistics, GitHub examples).

**Note:** The requirements and risks identified here are given decision IDs (Rule 19) in `/discuss` and `/conceptualize`.

## Output Format

```
## Problem Analysis

### Core Problem
[Precise description]

### Affected Components
[List of components with file paths]

### Boundary Conditions
[Constraints and dependencies]

### Risks & Unknowns
[Open questions, potential pitfalls]

### Impact & Complexity
- Impact: [Local / cross-module / system-wide]
- Complexity: [S/M/L/XL]
- Affected files: [estimated number]

## Recommended Research Prompt

> /research [generated prompt]

## Workflow State (update in plan.md)
- Completed Skill: /analyze
- Result: [1-2 sentences: core problem + number of affected components]
- Next Skill: /discuss (clarify gray areas with the user) — or directly /research if no gray areas remain
- Context for next Skill: Problem analysis + reference to research prompt (persisted in `files/research-prompt.md`) for the eventual /research step

**Scope ambiguity:** If the user statement allows multiple architectural interpretations, do NOT lock in an architecture. Instead: list the interpretations explicitly and recommend `/discuss` as the next step. Incorrect scope assumptions in /analyze cause architecture errors that only become visible in /discuss or /design — then research and design work is lost.
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** `/discuss` to clarify gray areas, hidden assumptions, and user preferences with the user. Only after that, run the generated `/research` prompt so research focuses on questions the user truly cannot answer.