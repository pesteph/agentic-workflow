---
name: conceptualize
description: Shows options for action and evaluates them. Use this Skill when multiple solution approaches are possible and an informed choice needs to be made — as a decision checkpoint before detailed analysis.
---

# Conceptualize

You show options for action and let the user decide which ones to pursue.

## Execution

**Delegate** the concept work to a Sub-Agent. Give it the full Skill instructions, the problem analysis from `/analyze`, and the research results. Show the user the complete result.

## Approach

1. **Recap the problem space** — Summarize the analyzed problem space and the research results.
2. **Show options for action** — List all conceivable **architectural** solution approaches and evaluate each in 1–2 sentences. For each option: “Is deeper analysis worthwhile? Yes/No + reason.” Goal: provide an overview of the solution space. This avoids time-consuming analysis of dead ends while still allowing unexpected alternatives to be discovered.

   **Important:** Evaluate **architecture**, not working style (commit strategy, test order, branching). If after research and discuss only **one** architectural path makes sense: say that clearly and go directly to `/design`. Do not generate artificial options just to fill the template.

## HARD-GATE: the user decides

NEVER make the decision yourself. No "Recommendation: Option B", no "The right choice is...", no implicit ranking that pushes the user toward one option. Present options, present trade-offs (concrete, with sources), let the user decide. Even when one option looks obviously better — the choice is ALWAYS the user's.

**Quantify each option.** Every option in the matrix MUST cite a source: codebase (file:line), research (URL), or user decision. "Could work" without a source is forbidden.

## Decision IDs

Options receive IDs according to Rule 19: D-A-XX, D-B-XX, D-C-XX (sequential per option group). These IDs are referenced by `/design` in ADRs.

## No placeholders (Rule 18)

Each option must be described concretely. No vague placeholders — the Main Agent includes the full forbidden list from Rule 18 when delegating.

## Alternative: `/deep-conceptualize`

For complex architecture decisions with more than 3 serious options, `/deep-conceptualize` can be used instead — a multi-agent concept exploration with parallel agents per option.

## Output Format

```
## Action Options

### Problem Space Summary
[Brief recap]

### Non-Goals
[What is consciously NOT in scope — even if it looks adjacent or tempting. Makes scope boundaries explicit so they survive into /design and /brief.]

### Options
| ID | Option | Description | Complexity | Risk | Effort | Deeper Analysis? | Rationale |
|---|--------|-------------|-------------|--------|---------|-------------------|------------|
| D-A-01 | [Name] | [1-2 sentences] | Low/Medium/High | [Main risk in 1 sentence] | S/M/L | Yes/No | [Why] |

## Workflow State (update in plan.md)
- Completed Skill: /conceptualize
- Result: [1-2 sentences: number of options, which were approved for detailed analysis]
- Next Skill: /design
- Context for next Skill: [Approved options + research results]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** The user decides which options to pursue further. Then `/design` for the detailed analysis of the approved options.