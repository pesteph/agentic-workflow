---
name: doc-review
description: Checks whether the documentation matches the code and the specs. Use this Skill with a PR number to ensure documentation consistency.
---

# Doc-Review

You check whether the documentation is consistent with the code and the specifications.

## Execution

**Delegate** the doc review to a Sub-Agent. Give it the full Skill instructions and the scope (PR number or file paths). Show the user the complete result.

## Approach

### 1. Capture the changes

- Read the PR diff or the specified files
- **Map commit authors:** For branch diffs, ALWAYS run `git log --format='%h %an %s' -- <file>` to attribute changes to the correct author/PR. Do not attribute the entire branch diff to a single source — a branch can contain commits from multiple authors/PRs.
- Identify which functionality was changed/added
- Check whether relevant documentation exists

### 2. Consistency check

**IMPORTANT: Every finding MUST be verified before reporting it.** Before reporting a finding such as “File X is missing entry Y”, actually read the file and confirm with grep/search that the entry is truly missing. Unsupported findings are false positives and waste time.

- **Code ↔ docs:** Does the documentation match the actual implementation?
- **API signatures:** Are parameters, return values, and exceptions documented correctly?
- **Behavior changes:** Are changed behaviors reflected in the docs?
- **New features:** Is new code also documented?
- **Deleted code:** Has documentation for removed code also been removed?
- **ADR traceability:** Are the ADRs from `/design` reflected in the project documentation?
- **Changelog:** Is the change documented in the changelog (if present)?

### 3. Dual-purpose review

The docs must serve two audiences:
- **Humans:** Are the docs compact and understandable enough to read?
- **Agents:** Do the docs contain enough context for an agent to understand the code and work with it?

### 4. Completeness

- README is up to date
- Architecture decisions are documented
- Configuration options are described
- Examples are runnable and current

## Output Format

```
## Doc-Review

### Consistency
| Change | Docs available | Current | Status |
|----------|---------------|---------|--------|
| [What]    | Yes/No       | Yes/No | ✅/❌  |

### Missing Documentation
- [What is missing and where it should go]

### Outdated Documentation
- [What is no longer correct]

### Dual-Purpose Assessment
- Human readability: [Good/Needs improvement]
- Agent context: [Sufficient/Insufficient]

## Workflow State (update in plan.md)
- Completed Skill: /doc-review
- Result: [1-2 sentences: number of inconsistencies, overall status]
- Next Skill: /retro
- Context for next Skill: [Summary of the workflow run for the retrospective]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Discuss the findings with the user and fix them. Then run `/retro` to reflect on the workflow.