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

### 3. Dual-Purpose Assessment (mechanical)

The verdict is **derived** from these checks, not asserted. Answer each yes/no and attach the evidence that proves it (grep/path). The verdict is `Pass` only if all are yes; otherwise `Fail` and each `no` becomes a finding (see grammar below).

- [ ] Every public API/flow named in the code is documented somewhere — evidence: grep the symbol/route name in docs.
- [ ] No doc names a symbol/file/path that no longer exists — evidence: grep each referenced name in the code.
- [ ] Examples reference real, current paths and signatures — evidence: the cited path/signature resolves.
- [ ] A doc reader could act (build/run/call) without reading the code — evidence: the entry-point doc states the command/signature, not just prose.

### 4. Findings grammar (closed tags, grep-verified)

Every finding uses one of these tags. Format: `<file>:<loc>: <tag>: <what>. evidence: <grep/path>.`

- `stale:` doc describes something the code no longer does → cite doc location + the contradicting code path.
- `missing:` code behavior/flow/API with no doc → cite the code location.
- `drift:` doc and code disagree on a detail (name/format/value) → cite both.
- `unrunnable:` example/command that would fail as written → cite why.

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

### Findings (closed tags, each grep-verified)
- path/to/file:loc: stale: [what]. evidence: [grep/path].
- path/to/file:loc: missing: [what]. evidence: [grep/path].
- path/to/file:loc: drift: [what]. evidence: [grep/path].
- path/to/file:loc: unrunnable: [what]. evidence: [grep/path].

### Dual-Purpose Assessment (derived)
- Every public API/flow documented: Yes/No — [evidence]
- No doc references a dead symbol/file/path: Yes/No — [evidence]
- Examples reference real, current paths/signatures: Yes/No — [evidence]
- Reader can act without reading the code: Yes/No — [evidence]
- **Verdict: Pass/Fail** (Pass only if all four are Yes)

## Workflow State (update in plan.md)
- Completed Skill: /doc-review
- Result: [1-2 sentences: number of inconsistencies, overall status]
- Next Skill: /retro
- Context for next Skill: [Summary of the workflow run for the retrospective]
```

💡 Context maintenance: Consider context compaction for long output. Update plan.md first.

---

**Next step:** Discuss the findings with the user and fix them. Then run `/retro` to reflect on the workflow.