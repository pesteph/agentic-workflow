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
- `/simplify` → `/test-review` → `/review` → `/sec-review` → `/doc-review`

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
- Improvements to `copilot-instructions.md`

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
2. Implement approved proposals IMMEDIATELY:
   - Adjust `copilot-instructions.md` (add/change rules)
   - Adjust Skill files or create new Skills
   - Update workflow diagrams and tables
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
- Findings per review Skill: [/simplify: X, /test-review: X, /review: X, /sec-review: X, /doc-review: X]
- Research loop iterations: [Number]
- Changed files: [Number]
- Dynamic /research calls: [Which Skills used /research?]
- Overall rating: [⭐⭐⭐⭐⭐]

## Workflow State (update in plan.md)
- Completed Skill: /retro
- Result: [1-2 sentences: overall rating, most important improvement proposal]
- Next Skill: /analyze (next workflow run)
- Context for next Skill: [Improvements that were incorporated into copilot-instructions.md]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Implement the improvement proposals. For upstream proposals: call `/upstream` to send the changes back to the original Skill repo as a PR.