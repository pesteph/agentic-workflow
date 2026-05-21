---
name: verify
description: Completeness check, plan verification, and self-review. Use this Skill when the completeness and consistency of the plan should be ensured before implementation — after /design.
---

# Verify

You verify the design output for completeness, consistency, and fidelity to scope — before implementation resources are used.

## Execution

**Delegate** the verification to a Sub-Agent. Give it the full Skill instructions, the design result from `/design`, the original problem analysis from `/analyze`, and the user decisions from `/discuss`. Show the user the full result.

**Cross-check principle:** The Sub-Agent has a fresh context window and was NOT involved in the design. This is intentional — self-review does not find blind spots.

## Verification dimensions

### Dimension 1: Requirement Coverage & Scope Fidelity
Every requirement from the problem analysis (`/analyze`) must be assigned to at least one implementation step.
- Read the requirements from `/analyze`
- Check every implementation step from `/design` against the requirement list
- Mark requirements without an assigned step as a **gap**
- Check whether requirements were omitted without explicit user approval (Decision ID) — these are **scope reductions**
- Check whether features were simplified without documenting it
- **Every scope reduction that is not documented as an intentional decision (with Decision ID) is a finding**

### Dimension 2: Task Completeness
Every implementation step must contain: (a) affected file(s), (b) concrete action (what will be changed/created), (c) verification criterion (how to tell that the step was successful).
- Check every step for these 3 elements
- Steps without all 3 elements are **incomplete**

### Dimension 3: Dependency Correctness
Are the dependencies between implementation steps correct?
- Are there circular dependencies?
- Are there steps that use artifacts that are only created in later steps?
- Is the order logical?

### Dimension 4: Key Links Planned
Are the artifacts wired correctly?
- Are new files imported/registered/referenced in the right places?
- Are configuration entries planned where needed?
- Are routing/wiring steps present?

### Dimension 5: Scope Sanity
Does the size match the original task?
- Is the plan too large for a single implementation? (more than 15 files → warning)
- Is there scope creep — steps that go beyond the original requirement?
- Is the plan too small — are obvious aspects missing?

### Dimension 6: Verification Derivation
Are the acceptance criteria user-observable?
- Are verification criteria externally verifiable (not only internal)?
- Can the user confirm completion without reading code?

### Dimension 7: Context Compliance
Are user decisions from `/discuss` respected?
- Read all D-G-XX and D-U-XX decisions from `/discuss`
- Check whether the implementation plan respects every decision
- Decisions ignored by the plan are **compliance violations**

### Dimension 8: Self-Review (quality check of the design document)
- **Placeholder scan:** Search for forbidden phrases according to Rule 18: "TBD", "TODO", "later", "unclear", "will be defined", "details follow", "add appropriate X", "handle edge cases", "similar to X", "will be clarified", "as needed", "adjust as needed"
- **Internal consistency:** Do ADRs contradict each other? Does the implementation plan contradict the ADRs?
- **Ambiguity check:** Are there phrases that can be interpreted ambiguously? (e.g. "configure appropriately" — what exactly?)

## Output format

```
## Verify result

### Review matrix

| # | Dimension | Status | Findings |
|---|-----------|--------|----------|
| 1 | Requirement Coverage & Scope Fidelity | ✅/⚠️/❌ | [Number of gaps + reductions] |
| 2 | Task Completeness | ✅/⚠️/❌ | [Number of incomplete steps] |
| 3 | Dependency Correctness | ✅/⚠️/❌ | [Number of errors] |
| 4 | Key Links Planned | ✅/⚠️/❌ | [Number of missing connections] |
| 5 | Scope Sanity | ✅/⚠️/❌ | [Assessment] |
| 6 | Verification Derivation | ✅/⚠️/❌ | [Number of non-observable criteria] |
| 7 | Context Compliance | ✅/⚠️/❌ | [Number of violations] |
| 8 | Self-Review | ✅/⚠️/❌ | [Number of placeholders/inconsistencies/ambiguities] |

### Findings (if any)

| ID | Dimension | Severity | Finding | Recommendation |
|----|-----------|----------|---------|----------------|
| V-01 | [Dim] | High/Medium/Low | [What is missing/incorrect] | [Concrete fix] |

### Overall assessment
- **PASS** — Plan is ready for implementation
- **PASS WITH FINDINGS** — Plan is ready for implementation, findings are improvement suggestions
- **FAIL** — Plan has gaps that must be closed before implementation

## Workflow state (update in plan.md)
- Completed Skill: /verify
- Result: [PASS/FAIL, number of findings by severity]
- Next Skill: [Implementation (PASS) | /design (FAIL, revision)]
- Context for next Skill: [List of findings for FAIL, or implementation plan for PASS]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** For PASS → implementation (local via Sub-Agent or `/brief` for cloud). For FAIL → back to `/design` for revision.