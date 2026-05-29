---
name: qa
description: Meta-Skill that dispatches all 5 QA reviews in parallel as a Fleet. Use this Skill after implementation to perform quality assurance efficiently.
---

# QA (Quality Assurance Fleet)

You dispatch all 5 review Skills in parallel as a Fleet and present the consolidated results.

## Execution

The Main Agent performs `/qa` **itself** — it coordinates the Fleet agents and consolidates the results. The individual reviews are delegated to Sub-Agents.

## Prerequisites

- Implementation complete
- Build green (run the project's build command — read from project files per Rule 20)
- Tests green (run the project's test command with a timeout per Rule 15)
- Intentional design choices documented (for review context, Rule 5)

## Approach

### 0. Pre-Flight (HARD-GATE — not negotiable)

Before any review work, verify the working state. Every check must pass; no "yes but we could still…".

**Git status:**
```shell
git status --short
git branch --show-current
```
- Detached HEAD (empty branch name) → **STOP**: "Detached HEAD. Switch to a branch."
- Uncommitted changes → **STOP**: "Uncommitted changes. Commit first, then /qa."
- On the default/integration branch (main/dev) → **STOP**: "Switch to a feature branch."

**Push status** (only if the project uses a remote + PR flow):
```shell
git fetch origin --quiet
git rev-list --left-right --count @{upstream}...HEAD
```
- No upstream → **STOP**: "Branch has no upstream. Push first."
- Unpushed commits (right number > 0) → **STOP**: "Push first, then /qa."

**PR (only if the project uses PRs):**
```shell
gh pr view --json number,title,headRefName,baseRefName,state,url,headRefOid
```
- No PR / PR not OPEN → **STOP** with the reason.
- **Derive `baseRefName` from the PR** — use `git diff {baseRefName}...HEAD` for scope. Do NOT hardcode `main`/`dev`.

For projects without a remote/PR flow, the git-status checks still apply; skip the push/PR checks.

### 1. Determine scope

Identify all changed files vs. the PR base (from Pre-Flight) — fall back to `main` only if no PR base was derived:
```shell
git --no-pager diff --stat {baseRefName}..HEAD
```

Split them into source files and test files. Read the Skill files of the 5 review Skills to know the current instructions.

### 2. Gather design context

Collect intentional design choices from the workflow so far (plan.md, architecture docs, discuss/design results). Give these to every review agent to avoid false positives (Rule 5).

### 3. Dispatch the Fleet

Start **all 5 reviews in parallel and immediately** as `general-purpose` agents in background mode. Do not wait for confirmation — the workflow expects parallel execution.

**⚠️ ALL 5 agents MUST use Sub-Agent.** Do not use `code-review` for /review — the code-review agent produces unstructured output (its internal reasoning process leaks into the report) instead of the expected findings table format.

| Agent | Skill | Scope | Model |
|-------|-------|-------|-------|
| qa-simplify | `/simplify` | Source files | faster model (review = pattern recognition) |
| qa-test-review | `/test-review` | Source + test files | faster model |
| qa-review | `/review` | Source files + diff | faster model |
| qa-security-review | `/security-review` | Source + dependencies | faster model |
| qa-doc-review | `/doc-review` | docs/ + source | faster model |

(Model choice per the Model Selection guidance in AGENTS.md — reviews are primarily pattern recognition, so a faster model fits. Never hard-code a specific model name; availability differs across harnesses.)

**Every agent prompt must contain:**
- Full Skill instructions (from the respective SKILL.md)
- List of all changed files with full paths
- Intentional design choices (marked as “NOT a finding”)
- Instruction: fix safe findings directly, report the rest as a table
- Instruction: run the project's build command after fixes (per Rule 20, read from project files)
- SQL inserts for findings tracking
- Forbidden list from Rule 18

**Additional instructions for the review agent:**
- If a design document exists (e.g. `files/design-output.md`): pass it to the review agent with the instruction “Compare the implementation against the design document. Every documented design decision (ADR) that is NOT reflected in the code = finding. Especially decisions that mean ‘remove something/do not do something’."

**Additional instructions for the test-review agent:**
- Explicit requirement for a mathematical code-path ↔ test matrix (Rule 23)
- All source AND test files in scope

### 4. Consolidate results

When all agents are done:

1. Read all agent results
2. Verify build + tests using the project's build/test commands (read the command from project files per Rule 20; follow the build/test execution policy of Rule 15 — tests only in a worktree, otherwise build only)
3. **Pre-categorization** (REQUIRED before presentation):
   - **Test findings** (missing tests, missing assertions) → automatically “implement”, do NOT discuss
   - **Doc findings** (docs/code inconsistency) → automatically “implement”, do NOT discuss
   - **Source findings** (simplify, review, security-review) → discuss individually with the user, user decides implement/park
   - No finding may be parked without explicit user approval
4. Create the consolidated report with the categorization

### 5. Implement open findings

If there are open findings the user wants fixed (e.g. missing tests):
- Start additional agents to implement the findings
- Verify build + tests after each fix

## Output Format

```
## QA Report

### Summary

| Review | Findings | Fixed | Open |
|--------|----------|-------|------|
| /simplify | X | Y | Z |
| /test-review | X | Y | Z |
| /review | X | Y | Z |
| /security-review | X | Y | Z |
| /doc-review | X | Y | Z |
| **Total** | **X** | **Y** | **Z** |

### Automatically Fixed
- [List of fixed findings with description]

### Open Findings

| ID | Severity | Review | File | Description | Recommendation |
|----|----------|--------|------|-------------|----------------|
| XX | 🔴/🟡/🔵 | [Skill] | [File] | [Description] | implement/park |

### Test Coverage (mathematical)
[Take over the matrix from test-review]

### Build & Tests
- Build: ✅/❌ (Warnings: X)
- Tests: X/Y passed

## Workflow State (update in plan.md)
- Completed Skill: /qa
- Result: [Total findings, fixed, open, test coverage %]
- Next Skill: /retro
- Context for next Skill: [QA results as retro input]
```

💡 Context maintenance: Fleet results are token-intensive. Consider context compaction after presenting them.

---

**Next step:** `/retro` — run the workflow retrospective.