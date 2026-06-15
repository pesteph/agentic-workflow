---
name: analyze
description: Analyzes a GitHub issue, opens up the problem space, and generates a /research prompt. Use this Skill when an issue or task needs to be analyzed.
---

# Analyze

You analyze a GitHub issue or a task and open up the problem space.

## Execution

The Main Agent performs `/analyze` **itself** — framing the problem benefits from the full conversation context (Rule 2). It may dispatch read-only Sub-Agents for broad codebase exploration, but the analysis and its synthesis stay with the Main Agent.

## Approach

1. **Read and understand the issue/task** — Read the issue completely, including comments and linked resources.
2. **Establish context** — Examine the affected code, the architecture, and relevant dependencies.
3. **Open up the problem space** — Identify:
   - What is the core problem?
   - Which components are affected?
   - Which boundary conditions and constraints exist?
   - Which risks and unknowns exist?
   - How large is the impact? (number of affected files/services/APIs, local vs. cross-cutting)
   - Complexity estimate: S/M/L/XL — apply the rubric below mechanically, do not guess.
4. **Formulate the problem definition** — Summarize the problem precisely.
5. **Generate a research prompt** — Create a **fully written out** research prompt for `/research`. The prompt must be copy-paste ready: as prose in a code block, no keyword list, no table. The user should be able to use the prompt for `/research` directly without rework. The prompt includes: project context (stack, version from project files such as `.csproj`, `package.json`, etc.), precise numbered questions with concrete information needs, and the desired evidence type (doc links, NuGet statistics, GitHub examples).

**Note:** The requirements and risks identified here are given decision IDs (Rule 19) in `/discuss` and `/conceptualize`.

## Complexity Rubric

Pick the highest size whose threshold any signal triggers (worst signal wins).

| Size | Affected files/components | Open unknowns | Blast radius / reversibility | New deps / architecture |
|---|---|---|---|---|
| S | 1–2, one component | none | local, trivially reversible | none |
| M | 3–5, one module | 1–2 minor | module-local, easily reversible | none |
| L | 6–15, multiple modules | several, or 1 major | cross-module, costly to undo | maybe a new dependency |
| XL | 15+ or cross-service | many / fundamental | system-wide, hard to reverse | yes — new arch or deps |

## Right-Size Recommendation

Per Rule 1, depth scales with complexity. Map the size to a recommended phase chain. The minimum chain is `/axiom → /analyze → /design + brief → /qa → /retro`; `/discuss`, `/research`, `/conceptualize`, `/diaboli` are optional and added as size grows.

| Size | Recommended chain |
|---|---|
| S | analyze → design (lightweight) + brief → qa → retro (skip discuss/research/conceptualize/diaboli) |
| M | analyze → discuss → design + brief → qa → retro (skip research/conceptualize/diaboli unless an unknown demands it) |
| L | analyze → discuss → research → conceptualize → design + brief → qa → retro (add diaboli if risk is high) |
| XL | full chain incl. discuss → research → conceptualize → diaboli → design + brief → qa → retro |

**Critical (Rule 1):** the model only *recommends* this right-size path — the **user decides**. Never silently self-skip a phase; if you believe a phase is unnecessary, say so and let the user confirm.

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
- Complexity: [S/M/L/XL] — [the rubric signal(s) that set this size]
- Affected files: [estimated number]
- Right-size recommendation: [recommended chain for this size]; phases skipped: [list]. You decide — this is a recommendation, not a self-skip.

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