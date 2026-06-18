---
name: retro
description: Conducts a workflow retrospective. Identifies what went well, what can be improved, and suggests concrete improvements. Use this Skill at the end of a workflow run.
---

# Retro

You conduct a retrospective to continuously improve the workflow.

## Execution

This Skill is executed by the Main Agent **itself** — not delegated to a Sub-Agent. The Main Agent experienced the workflow and can reflect on it. A Sub-Agent has no context about what the run actually felt like.

**Data sources:**
- **Own experience** — What went well? What was cumbersome? Where was time lost?
- **plan.md** — workflow state as a memory aid for steps, results, findings
- **retro_notes SQL table** — on-the-fly collected improvement notes from the running workflow. Query: `SELECT * FROM retro_notes ORDER BY id`

## Approach

### 1. Recap the workflow run

- **plan.md as a data source**: Read plan.md to reconstruct the actual run — which Skills ran, which were skipped, and what results they had.

Go through the completed steps:
- `/analyze` → `/discuss` → `/research` → `/conceptualize` → `/design` → implementation/`/brief`
- `/simplify` → `/test-review` → `/review` → `/security-review` → `/doc-review`

### 2. Evaluate each step

For each Skill that ran:
- What worked well?
- What was cumbersome or missing?
- How long did the step take (subjectively: fast/appropriate/too long)?
- Was the output helpful for the next step?

### 3. Overall evaluation

- Was the original problem solved?
- Was the workflow run efficient?
- Were there steps that could have been skipped?
- Were there missing steps?

### 4. Improvement proposals

Formulate concrete, actionable suggestions:
- Changes to Skill definitions
- New Skills that are missing
- Changes to the workflow order
- Improvements to the user-scope instruction file (`~/.claude/CLAUDE.md` / `~/.copilot/copilot-instructions.md`)

### 4b. Instructions/Skills best-practice audit (every retro — no exception)

The retro improves not only the workflow's *behavior* but also the *structure* of the Skill and instruction files themselves:

1. **Structure scan** — for every workflow file in scope: the user-scope Skills (`~/.claude/skills/*/SKILL.md`, `~/.copilot/skills/*/SKILL.md`), the user-scope instruction files (`~/.claude/CLAUDE.md`, `~/.copilot/copilot-instructions.md`), and the project instruction files (`AGENTS.md`/`CLAUDE.md`): line count, number of distinct topics per file.
2. **Length check** — for a *focused single-topic file* (one skill, one instruction file) over 80 lines is a warning, over 120 a problem: rules drown in the mass. **Exception:** a central numbered rules reference or index (e.g. `AGENTS.md`) is naturally longer because it aggregates many atomic rules — judge it not by line count but by whether each entry stays atomic and there are no duplicates or contradictions. The real smell is a *single* rule growing long, or a topic file mixing concerns — not the aggregate length of a deliberate rules list.
3. **Focus check** — does each file have ONE clear topic, or is it a grab-bag?
4. **Duplicate rules** — same rule stated in multiple files → consolidate.
5. **Contradictions** — rules that conflict → resolve.
6. **Compliance check** — for every rule that was VIOLATED this session: WHY? Too hidden? No HARD-GATE? Too abstract? Wrong file?
7. **Split proposals** — for over-long files, propose concretely which sections move where.

A rule that gets ignored is often not a discipline problem but a structure problem — it was buried where nobody reads it.

### 5. Upstream proposals

Identify improvements that are relevant not only to this project, but to **all consumers** of the Skills. Formulate them as diff-ready changes to the Skill files.

Format:
```
### Upstream Proposals (for /upstream)
| File | Change | Reason |
|-------|----------|------------|
| skills/brief/SKILL.md | [What to change] | [Why] |
```

The user can then call `/upstream` to send these proposals back to the original Skill repo as a PR.

### 6. IMPLEMENT the proposals

Retro is only “done” once proposals are implemented. Flow:

1. Discuss each proposal with the user (implement / park / adjust)
2. Implement approved proposals IMMEDIATELY (in the **user scope** — changes are global at once):
   - Adjust the user-scope instruction file (`~/.claude/CLAUDE.md` / `~/.copilot/copilot-instructions.md`)
   - Adjust user-scope Skill files or create new Skills
   - Update workflow diagrams and tables
   - Generic improvements that help all consumers go back to the repo via `/upstream`
3. Verify the implementation (check files, consistent?)
4. Only AFTER THAT is the retro complete

**Retro without implementation is not a retro.**

## Output Format

```
## Retrospective

### What went well 👍
- [Point]

### What can improve 🔧
- [Point]

### Concrete Improvement Proposals
1. [Proposal with rationale]
2. [Proposal with rationale]

### Metrics
- Workflow steps run: [X of Y]
- Skipped steps: [List]
- Findings per review Skill: [/simplify: X, /test-review: X, /review: X, /security-review: X, /doc-review: X]
- Research loop iterations: [Number]
- Changed files: [Number]
- Dynamic /research calls: [Which Skills used /research?]
- Overall rating: [⭐⭐⭐⭐⭐]

## Workflow State (update in plan.md)
- Completed Skill: /retro
- Result: [1-2 sentences: overall rating, most important improvement proposal]
- Next Skill: /analyze (next workflow run)
- Context for next Skill: [Improvements incorporated into the user-scope instruction file]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Implement the improvement proposals. For upstream proposals: call `/upstream` to send the changes back to the original Skill repo as a PR.