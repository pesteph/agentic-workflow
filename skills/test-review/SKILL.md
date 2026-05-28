---
name: test-review
description: Calculates test coverage mathematically, performs logic tracing, and checks test conventions. Use this Skill to evaluate the test quality of a change.
---

# Test Review

You analyze test coverage and test quality through mathematical calculation and logic tracing.

## Execution

**Delegate** the analysis to a Sub-Agent. Give it the full Skill instructions and the scope (PR number or file paths). Show the user the full result.

## Approach

### 0. Determine scope

Determine the scope of the change from the context (issue, PR description, branch name, commit messages):

- **PoC/Spike** — Only happy-path coverage is expected. Edge cases and error paths are debt.
- **MVP** — Happy path + most important error paths. Edge cases are optional.
- **Production** — Full coverage is expected: happy path, error paths, edge cases, concurrency.

State the detected scope in the assessment and adjust the coverage expectation accordingly.

### 1. Code path analysis

- Identify ALL code paths (branches, conditions, loops, exception handlers, default cases) in the changed code
- **Number each path individually** (e.g. P-Class-1, P-Class-2, ...) — no path may remain unnamed
- In particular, capture **error-in-error paths**: What happens if error handling itself fails? (exception in catch, abandon failure, dispose failure, retry exhaustion)
- Create a **complete mathematical matrix**: every code path ↔ test that covers it

**Required matrix format:**
```
| Class | Code Path | Test | Status |
|--------|-----------|------|--------|
| MyClass | P-MC-1: Happy Path | MyTest_HappyPath | ✅ |
| MyClass | P-MC-2: Exception in Catch | ??? | ❌ GAP |
```

The matrix is the **core product** of the test review. Prose-based assessments ("coverage is good") are not acceptable.

### 2. Logic tracing

For each test:
- Trace the execution path through the production code
- Verify that assertions test the correct logic
- Check whether edge cases are covered:
  - Null/empty values
  - Boundary values
  - Error scenarios
  - Concurrent access (if relevant)
- **Mutation check** (Production scope): Check whether tests actually catch what they are supposed to catch — what happens if you invert a condition? Does a test fail?

### 2b. Identify paths that cannot be unit tested

Paths that create non-mockable SDK objects **internally** (e.g. `HttpClient` without a factory, `DbContext` without an in-memory provider) are **not unit-testable**. These paths:

- Mark as **"Integration Level"** (do not count as a gap)
- Mark in the matrix with `⚠️ Integration` instead of `❌ GAP`
- Show separately in the coverage calculation: `Unit: X/Y, Integration: A/B (not counted)`
- Check whether the **calling methods** (handlers, delegates) involved in these paths are tested separately

**Criterion:** Can the path be isolated through dependency injection and mocking? No → Integration Level.

### 3. Coverage calculation

```
Coverage = (Covered paths / Total paths) × 100%

Categories:
- Happy Path: [X/Y covered]
- Error Path: [X/Y covered]
- Edge Cases: [X/Y covered]
- Integration: [X/Y covered]
```

### 4. Conventions check

- Test naming follows project conventions
- Arrange-Act-Assert pattern is followed
- Tests are isolated and independent
- No test interdependencies

### 5. Tool detection

- Detect the project's test framework (e.g. xUnit, NUnit, Jest, pytest, JUnit)
- Mention it in the report
- Check whether framework-specific best practices are followed

### 6. Reality check (for migration/integration projects)

- Check whether test input data matches the real production format
- Compare field names, casing (PascalCase vs camelCase), and value formats with legacy tests or real messages
- "Does the code do the right thing?" is more important than "Does the code do what it is supposed to do?"

## Dynamic context (optional)

If the test framework is unknown or the testing conventions are unclear:
1. Identify the test framework from the project files
2. Use `/research` to research framework-specific testing patterns and best practices
3. Evaluate the tests against these patterns

## Output format

```
## Test Review

### Scope
[PoC/MVP/Production] — Detected from: [Source]

### Coverage matrix

| Code Path | Test | Status |
|-----------|------|--------|
| [Path]    | [Test] | ✅/❌ |

### Coverage
- Total: [X]%
- Happy Path: [X]%
- Error Path: [X]%
- Edge Cases: [X]%

### Gaps
- [Missing test / path]

### Conventions
- [Finding]

## Workflow state (update in plan.md)
- Completed Skill: /test-review
- Result: [1-2 sentences: coverage X%, scope, number of gaps]
- Next Skill: /review
- Context for next Skill: [PR number or file paths]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Run `/review` for a general code review.