---
name: deep-conceptualize
description: Multi-Agent concept exploration for complex architecture decisions. Alternative to /conceptualize — use this Skill when multiple options should be analyzed deeply in parallel.
---

# Deep-Conceptualize

You orchestrate a multi-agent analysis for complex architecture decisions. Instead of a flat list of options (like `/conceptualize`), each option is analyzed in depth by its own agent, complemented by oversight agents that reveal blind spots.

## Execution

The Main Agent orchestrates **itself** — but the analysis work is delegated to Sub-Agents:

1. **Option agents** (1 per option, Sub-Agent, in parallel): each agent analyzes ONE option in depth and argues FOR it.
2. **Oversight agents** (3 total, Sub-Agent, in parallel): each agent has ONE question: “What did the option agents miss?” — from the perspective of (a) maintainability, (b) scalability, (c) simplicity.
3. **Meta-agent** (1 total, Sub-Agent, sequentially after the others): consolidates all results into a comparison table.

**Parallelization:** Option agents and oversight agents are started at the same time (they are independent). The meta-agent starts only after all others are done.

## When to use `/deep-conceptualize` instead of `/conceptualize`

Use this Skill when:
- More than 3 serious options exist
- The decision is hard to reverse (architecture, data model, framework choice)
- The user explicitly requests it
- A simple pros/cons analysis is not enough

Use `/conceptualize` when:
- 2–3 clear options with obvious trade-offs exist
- The decision is easy to reverse
- Speed matters more than depth

## Approach

### Phase 1: Identify options

Read the results from `/discuss` and `/research`. Identify all serious options (minimum 2, maximum 6).

### Phase 2: Formulate agent prompts

For each **option agent**:
```
You analyze option [X]: [Name].
Context: [Problem analysis from /analyze, research results, discuss result]
Your task: Argue FOR this option. Analyze:
1. Technical feasibility — How is it implemented concretely?
2. Benefits — What do you gain?
3. Risks — What can go wrong? (Be honest, not only pro)
4. Effort — S/M/L with rationale
5. Dependencies — What must exist first?
Output as a structured report.
```

For each **oversight agent**:
```
You review the following options from the perspective of [Maintainability/Scalability/Simplicity]:
[List all options]
Context: [Problem analysis, research, discuss result]
Your task: What did the options NOT consider? Which aspects of your perspective ([Maintainability/Scalability/Simplicity]) are neglected in the options?
Output: Maximum 3 blind spots, each with a concrete consequence.
```

### Phase 3: Start agents

Start option agents and oversight agents **in parallel**. Collect all results.

### Phase 4: Consolidate with the meta-agent

Give the meta-agent ALL results and this assignment:
```
Consolidate the following agent results into a comparison table.
[All option agent results]
[All oversight agent results]
Create:
1. A multi-dimensional comparison table (see output format)
2. A synthesis of the oversight findings
3. A recommendation with rationale
```

### Phase 5: User decision

Present the consolidated analysis to the user. The user decides which option(s) to pursue further.

## Decision IDs

Options receive IDs according to Rule 19: D-A-01, D-B-01, D-C-01, etc.

## Output Format

```
## Deep-Conceptualize

### Options Comparison

| Dimension | D-A-01: [Option A] | D-B-01: [Option B] | D-C-01: [Option C] |
|-----------|-------------------|-------------------|-------------------|
| Technical feasibility | [Assessment] | [Assessment] | [Assessment] |
| Benefits | [Core benefit] | [Core benefit] | [Core benefit] |
| Risks | [Main risk] | [Main risk] | [Main risk] |
| Effort | S/M/L | S/M/L | S/M/L |
| Maintainability | [Oversight assessment] | [Oversight assessment] | [Oversight assessment] |
| Scalability | [Oversight assessment] | [Oversight assessment] | [Oversight assessment] |
| Simplicity | [Oversight assessment] | [Oversight assessment] | [Oversight assessment] |

### Oversight Findings (blind spots)

| # | Perspective | Finding | Affected Options | Consequence |
|---|-------------|---------|--------------------|-----------| 
| 1 | [Maintainability/Scalability/Simplicity] | [What was overlooked?] | [Which ones] | [What happens] |

### Recommendation
[Which option and why, based on the overall analysis]

## Workflow State (update in plan.md)
- Completed Skill: /deep-conceptualize
- Result: [Number of options analyzed, number of oversight findings, recommendation]
- Next Skill: /design
- Context for next Skill: [Approved options + multi-agent analysis as the basis for the decision]
```

💡 Context maintenance: `/deep-conceptualize` is token-intensive (multiple parallel agents). Recommend context compaction after completion. Update plan.md first.

---

**Next step:** The user decides which options to pursue further. Then `/design` for the detailed analysis of the approved options.