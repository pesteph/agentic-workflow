---
name: simplify
description: Simplifies code in a pull request. Reduces complexity, improves readability, and removes unnecessary abstractions. Use this Skill with a PR number or file paths.
---

# Simplify

You simplify code in a pull request or in the specified files.

## Execution

**Delegate** the analysis and simplification to a Sub-Agent. Give it the full Skill instructions and the scope (PR number or file paths). Show the user the full result.

## Approach

1. **Determine scope** — Read the PR diff or analyze the specified files.
2. **Identify complexity** — Find:
   - Unnecessary abstractions
   - Superfluous indirections
   - Overly nested control flows
   - Duplicated code
   - Overcomplicated patterns where simpler ones are sufficient
3. **Suggest simplifications** — For each location:
   - Show the current code
   - Explain why it is complex
   - Show the simplified version
   - Explain what changes because of the simplification (and what does not)
4. **Check side effects** — Ensure that simplifications do not change behavior.

## Dynamic context (optional)

If the framework or language used is unknown:
1. Identify the framework and language version
2. Use `/research` to research idiomatic patterns of the language/framework
3. Evaluate simplifications against these idiomatic patterns

## Principles

- **Less code is better** — but not at the expense of readability
- **Explicit over implicit** — obvious is better than clever
- **Flat structures** — less nesting, early returns
- **Prefer the standard library** — no external dependencies for trivial things
- **Clarity over brevity** — explicit code is often better than compact code
- **Measure complexity** — calculate cyclomatic complexity before and after simplification where possible

## What should NOT be simplified

- Explicit error-handling chains (clarity > brevity)
- Intentional redundancy for readability
- Framework-idiomatic code (even if it could be made "simpler")

## Output format

Each finding is one mechanical line. No prose, no hedging:

```
L<line(s)>: <tag>: <what>. → <replacement>.
```

The tag is from a **closed vocabulary**; each tag dictates the replacement field:

- `delete:` — dead / unnecessary code → `remove` (replacement is nothing)
- `stdlib:` — reinvents a standard-library function → name the exact function
- `native:` — reinvents a native platform/framework feature → name the feature
- `yagni:` — abstraction with a single implementation / speculative generality → the concrete inlined form
- `shrink:` — verbose form with a shorter equivalent → show the shorter form

Highest-impact findings first. End with one forcing metric:

```
net: -<N> lines possible
```

If nothing is worth changing, output exactly the following and stop — do **not** manufacture nits:

```
Lean already. Ship.
```

### Calibration

❌ `L42: this could maybe be simplified by using a helper, consider refactoring`
✅ `L42: stdlib: hand-rolled max-of-list loop. → max(scores).`

## Workflow state (update in plan.md)

```
- Completed Skill: /simplify
- Result: [1-2 sentences: number of findings, biggest improvement, net lines saved]
- Next Skill: /test-review
- Context for next Skill: [PR number or file paths of the simplified files]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** After the simplification, check the changes with `/test-review`.