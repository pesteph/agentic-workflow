---
name: brief
description: Generates design.md (the green figure) as the handoff artifact for Phase 4 (Build). Always use this Skill at the end of Phase 3 (/design).
---

# Brief

You generate `design.md` — the **green figure** — as the only handoff artifact from Phase 3 (Green/Solution Design) to Phase 4 (Build).

## Three Paths

| Path | What happens |
|------|--------------|
| **Local** | `design.md` is handed to a local Sub-Agent (implements directly) |
| **Cloud** | `design.md` content becomes the GitHub issue for the Copilot Coding Agent |
| **Existing issue** | An existing issue is treated as the green figure — `/brief` enriches it with design decisions |

`design.md` is always the only artifact handed over to Phase 4 — regardless of the path.

## Execution

**Delegate** the preparation to a Sub-Agent. Give it the full Skill instructions and the **design result from `/design`** (including additions from `/diaboli`). Show the user the complete result.

## Approach

1. **Include the design result** — Use the solution concept and pseudocode from `/design` as the foundation. Apply additions from `/diaboli` (accepted risks, design changes, completeness findings) 1:1.
2. **Carry over research 1:1** — Technical details from the research report are NOT summarized or abstracted. For each data field: config key, field name, casing (PascalCase/camelCase), format, example value — state them explicitly. Information loss causes semantic bugs.
3. **Build design.md** — Add all information the implementer needs:
   - Precise problem description
   - Chosen solution approach with rationale
   - Pseudocode / reference implementation of the core classes (1:1 from the design doc, no abstraction)
   - Affected files and components
   - Acceptance criteria
   - Test requirements
4. **Enrich context** — Reference relevant files, patterns, and conventions from the repo.
5. **Define constraints** — What should the implementer NOT do? Which boundaries exist?
6. **Completeness check** — Before handoff, verify: does design.md contain enough context for the implementer to work WITHOUT follow-up questions?

   **No placeholders (Rule 18):** Every implementation step and every acceptance criterion must be concrete. No vague placeholders.

7. **Write atomic acceptance criteria** — Each individual operation/feature becomes its own acceptance criterion. Break down summary terms like “CRUD” into separate criteria.

## Integrated Quality Chain

The implementer (local or cloud) runs the **entire quality chain themselves** after implementation — directly in the same session, before finishing the work.

### Quality Checks (in this order)

| Check | Verifies | Source |
|-------|----------|--------|
| **Simplify** | Unnecessary complexity, superfluous abstractions, readability | `/simplify` criteria |
| **Test-Review** | Test coverage, logic tracing, missing edge cases | `/test-review` criteria |
| **Code-Review** | Bugs, logic errors, pattern violations, naming | `/review` criteria |
| **Security-Review** | Injection, auth issues, secrets, insecure defaults | `/sec-review` criteria |
| **Doc-Review** | XML docs consistent with code, outdated comments | `/doc-review` criteria |

### Handling findings

The implementer categorizes EVERY finding:

1. **Safe to fix** → Fix directly. Document it in the findings table as "✅ fixed".
2. **Unclear / needs discussion** → Do NOT fix. Document it as "⏳ open" with the reason.

**Do NOT skip any findings.**

### Findings Table

```markdown
## Quality-Check Findings

| ID | Category | Severity | Description | Status |
|----|-----------|----------|-------------|--------|
| QC-01 | Simplify | Medium | [Concrete finding] | ✅ fixed |
| QC-02 | Test-Review | High | [Concrete finding] | ⏳ open — [Rationale] |
```

### Pass along intentional design choices

The implementer receives the **intentional design decisions**:
- All D-U-XX decisions (user decisions from `/discuss`)
- All D-ADR-XXX decisions (ADRs from `/design`)
- Accepted risks from `/diaboli`

### For the cloud path: link Skills with file paths

Every reference to a Skill MUST include the relative file path (e.g. `.github/skills/simplify/SKILL.md`). The Cloud Agent does not know Skills by name.

### Place the quality chain prominently

QC instructions belong at the BEGINNING of the document — not at the end. Implementers (local and cloud) stop reading after green tests.

## Output Format of design.md

```
## Universe Context
[Relevant constraints and NFAs from copilot-instructions.md that apply to this change]

## Problem Description
[Precise technical description of the red spot]

## Solution Approach (Green Figure)
[Concrete implementation with rationale — from /design]

## Quality Chain After Implementation
[QC instructions — place them prominently here, not at the end]

## Pseudocode / Reference Implementation
[Core classes 1:1 from the design doc — DO NOT abstract]

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

## Test Requirements
- [ ] [What must be tested?]

## Constraints
- [What should not be changed]

## Relevant Files
- [Path and short description]

## Intentional Design Decisions (DO NOT report as a finding)
[All D-U-XX, D-ADR-XXX, accepted Diaboli risks]
```

```
## Workflow State (update in plan.md)
- Completed Skill: /brief
- Result: [design.md generated, path: local/cloud/existing-issue, number of acceptance criteria]
- Next Skill: Phase 4 Build
- Context for next Skill: [Path to design.md or issue number]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Hand off design.md to Phase 4. Local: start a Sub-Agent with design.md. Cloud: create a GitHub issue with the design.md content and hand it to the Copilot Coding Agent. After implementation: discuss and fix any open findings from the quality-check table locally.