---
name: next
description: Detects the current workflow state from plan.md and executes the next Skill documented there. Use this Skill to continue the workflow.
---

# Next

You read the workflow state from plan.md and execute the next Skill documented there.

## Execution

This Skill is executed by the Main Agent **itself** — it is pure coordination, not substantive work.

## Approach

1. **Read plan.md** — Find the last `## Workflow-State` entry in plan.md.
2. **Check whether plan.md is current** — Does the documented state match reality? Are all completed steps marked as ✅? If plan.md is outdated: **update it first**, then continue.
3. **Identify the next Skill** — Read the field “Next Skill” and “Context for next Skill”.
4. **Check open findings** — If the last Skill had findings: were they discussed and fixed? If not: point that out.
5. **Present the next step to the user:**
   - Which Skill comes next
   - With which context (from plan.md)
   - Whether prerequisites are met
6. **After confirmation:** Start the next Skill.
7. **Check context maintenance:** If several content-intensive Skills have run since the last context compaction (visible from plan.md workflow state), recommend context compaction to the user before the next Skill starts. Before compaction, make sure plan.md is current.

## If no workflow state is present

If plan.md does not contain a workflow state:
- Ask the user where they are in the workflow
- Or recommend `/axiom` as the entry point — so the project's universe is defined before the first task starts

## Output Format

```
## Workflow Progress

Last Skill: [Skill Name] ✅
Result: [Summary from plan.md]

Next Step: [Skill Name]
Context: [What the next Skill needs]

Should I start [Skill Name]?
```