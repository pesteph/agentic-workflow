---
name: design
description: Detailed analysis of the approved options. Formulate ADRs, create pseudocode as a collaborative artifact. Use this Skill when architecture and technical details should be worked out — after /conceptualize or directly after /analyze for straightforward tasks.
---

# Design

You perform the detailed analysis of the options approved by the user, formulate ADRs, and create pseudocode as a collaborative artifact.

## Execution

**Delegate** the design work to a Sub-Agent. Give it the full Skill instructions, the approved options from `/conceptualize`, and the research results. Show the user the complete result.

## Approach

1. **Detail the solution approaches** — Work through the approved options with pros and cons.
   
   Evaluate the discarded alternatives in a short matrix:
   
   | Alternative | Maintainability | Complexity | Testability | Performance | Reason for rejection |
   |---|---|---|---|---|---|

2. **Postulate the solution** — Choose the best approach and describe it concretely:
   - Architecture decisions (ADRs)
   - Affected files and components
   - Technical implementation
   - Testability
3. **Formulate ADRs** — Every architecture decision is documented as an ADR.
4. **Check open questions** — Are there ADRs that cannot be supported by research/sources?
5. **Create pseudocode** — Create pseudocode/skeleton code for core classes (interfaces, signatures, core logic). Pseudocode is **collaborative discussion material**: the agent creates a draft → the user discusses, changes, extends → iterate together → finalize. Fully compilable code is the job of the implementation agent in Phase 2. For `/brief`, the pseudocode must be concrete enough for the Cloud Agent to implement it — but it does not need to compile. An abstract “implementation plan” (step 1, step 2, ...) is still NOT sufficient.
6. **Store the design output** — Save the design result as `files/design-output.md` in the session. The design document is the central reference artifact for `/verify`, `/diaboli`, and implementation. **plan.md remains the source of truth for workflow state** — session files are read-only reference artifacts.

## ADR Requirement

Every architecture decision MUST be formulated as an ADR:

| ID | Decision | Choice | Rationale | Source |
|---|---|---|---|---|
| D-ADR-XXX | What was decided? | Which option? | Why? | Research result, official docs, codebase analysis, or **team decision** |

**Allowed source categories:**
- **Research result** — facts gathered through `/research`
- **Official documentation** — docs of the framework/library
- **Codebase analysis** — existing patterns in the project
- **Team decision** — an intentional decision without an external source (e.g. “We use pattern X because our team has experience with it”). Must be marked as such.

## Rule References

**No placeholders (Rule 18):** Vague placeholders are forbidden. Every implementation step must be concrete: file, action, expected result. The Main Agent includes the full forbidden list from Rule 18 when delegating.

**Decision IDs (Rule 19):** All ADRs get sequential IDs: D-ADR-XXX (e.g. D-ADR-001, D-ADR-002). Reference user decisions from `/discuss` (D-G-XX, D-U-XX) and options from `/conceptualize` (D-A-XX) where relevant.

## Research Loop

If open **technical** questions remain after proposing the solution:

1. Identify all ADRs without sufficient source backing
2. Formulate a `/research` prompt for the open questions
3. `/design` is called again after `/research` answers those questions

**Emergency exit:** ADRs based on team experience, project conventions, or pragmatic reasons may be categorized as a **team decision**. Not every decision needs an external source. The research loop is only for technical questions, not preference decisions.

**Design is “done” once all ADRs are either backed by sources or consciously marked as a team decision.**

## Output Format

```
## Solution Concept

### Summary
[Brief description of the chosen solution]

### ASCII Architecture Diagram
[Shows the components, their relationships, and data flows as ASCII art]

### Short Matrix of Rejected Alternatives
| Alternative | Maintainability | Complexity | Testability | Performance | Reason for rejection |
|---|---|---|---|---|---|

### Architecture Decisions (ADRs)
| ID | Decision | Choice | Rationale | Source |
|---|---|---|---|---|

### Pseudocode / Reference Skeleton
[Interfaces, signatures, core logic as pseudocode — iterated collaboratively with the user]

### Test Plan
[How is the solution tested?]

### Open Questions (if present)
> /research [Prompt for unsupported ADRs]

## Workflow State (update in plan.md)
- Completed Skill: /design
- Result: [1-2 sentences: chosen solution + number of ADRs]
- Next Skill: [/research (if there are open questions) | /diaboli (optional) | /verify | /brief (Cloud)]
- Context for next Skill: [Research prompt OR reference implementation OR brief foundation]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** If there are open questions → `/research`. Otherwise: optionally `/diaboli` for creative attacks on design decisions, then `/verify` for the completeness check before implementation. Alternatively, go directly to `/brief` for cloud implementation.