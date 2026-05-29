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

### 5. Promote session artifacts to repo knowledge (optional but recommended)

Workflow session artifacts (in `files/` or wherever the workflow stored them) are working documents that otherwise decay with the session. The valuable parts belong in the permanent repo docs. Review what exists and propose promotions — do NOT copy 1:1; polish and fit them into the target structure:

| Session artifact | Promote to |
|------------------|------------|
| design output / ADRs from `/design` | Architecture docs / `docs/decisions/` (ADR format) |
| analysis from `/analyze` | Research/architecture doc, if it holds lasting insight |
| decisions from `/discuss` | ADR, when architecture-relevant |
| concept options from `/conceptualize` | Decision background in architecture docs |

Present the promotion proposals to the user and let them decide what gets carried over. When promoting into versioned docs, bump the version and add a changelog entry if the project uses one. This step is a proposal step — apply promotions only after explicit user approval.

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