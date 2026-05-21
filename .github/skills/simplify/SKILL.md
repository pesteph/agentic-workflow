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

```
## Simplification suggestions

### [File:Line] — [Short description]
**Current:**
[Code block]

**Simplified:**
[Code block]

**Rationale:** [Why is this simpler? What does not change?]

## Workflow state (update in plan.md)
- Completed Skill: /simplify
- Result: [1-2 sentences: number of simplification suggestions, biggest improvement]
- Next Skill: /test-review
- Context for next Skill: [PR number or file paths of the simplified files]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** After the simplification, check the changes with `/test-review`.