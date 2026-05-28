---
name: upstream
description: Transfers Skill improvements from /retro to the original Skill repo as a PR. Use this Skill after /retro to share improvements with all Skill consumers.
---

# Upstream

You transfer Skill improvements from the Retro to the original Skill repo.

## Execution

**Delegate** the implementation to a Sub-Agent. Give it the full Skill instructions, the upstream suggestions from the Retro, and the path/URL of the Skill repo.

## Prerequisites

The user must provide:
1. **Skill repo path** — local path or GitHub URL of the original Skill repo
2. **Upstream suggestions** — from the latest `/retro` (in plan.md or directly in chat)

If the user does not specify a path, ask for it.

## Approach

### 1. Gather upstream suggestions

- Read the upstream suggestions from plan.md (section "Upstream Suggestions") or from the chat context
- Check whether the suggestions are generic enough for all consumers (filter out project-specific adaptations)

### 2. Prepare the Skill repo

- Navigate to the Skill repo (locally or clone it)
- `git pull` to fetch the latest version
- Identify the affected Skill files in the repo

### 3. Create a change overview

Before discussing diffs in detail — first provide an overall overview:

- Compare ALL Skill files between the consumer repo (local: `.github/skills/<name>/SKILL.md` or `.claude/skills/<name>/SKILL.md`) and the Skill repo (canonical: `skills/<name>/SKILL.md`)
- For dual-harness consumers, the two local copies should be identical — flag any divergence as a separate finding before upstreaming
- Show in a table: which Skills are identical, which are changed, which exist only locally/only in the repo
- For each changed file: one-sentence summary of what changed
- **Assessment per change:** Is the change suitable for upstream? Does it add value for all consumers?

Format:
```
| Skill | Status | Change | Suitable for upstream? |
|-------|--------|--------|------------------------|
| [Name] | Changed/Identical/Consumer-only | [1 sentence] | Yes/No + reason |
```

Only after this overview and user confirmation do you move on to the detailed diffs.

### 4. Diff review with the user

For each Skill file confirmed as suitable for upstream:

- Show the diff between the local version (consumer repo) and the Skill repo version
- Mark which changes are **generic** (suitable for upstream) and which are **project-specific**
- Discuss with the user: Which parts should be adopted?
- Project-specific adaptations can have general value — do not automatically filter them out; decide together

### 5. Apply changes

- Create a new branch: `retro/improvements-<date>` (e.g. `retro/improvements-2026-04-01`)
- Apply only the jointly approved changes to the Skill files in the Skill repo
- Check whether the changes are consistent with the existing structure

### 6. Update docs in the Skill repo

- Check whether the Skill repo has a README, docs, or a Skill overview (typically `README.md`, `AGENTS.md`, `QUICKSTART.md`)
- Update these files to match the changed Skills (e.g. new steps, changed descriptions, new Skills)
- If a new Skill should be skipped on a particular harness (because of a built-in equivalent), update the skip-list table in the Skill repo's `/init` Skill
- Goal: Skill repo docs and Skill files must not drift apart

### 7. Create a PR

- Commit the changes with a descriptive message
- Push the branch
- Create a pull request with:
  - Title: `retro: Skill improvements from workflow run`
  - Body: List of changes with rationale from the Retro
  - Reference to the project and issue that motivated the improvements

## Output format

```
## Upstream

### Diff review
| File | Change | Category | Decision |
|-------|--------|----------|----------|
| [Path] | [What] | generic/project-specific | adopt/discard |

### Applied changes
| File | Change | Status |
|-------|--------|--------|
| [Path] | [What] | ✅/❌ |

### PR
- Branch: [Name]
- PR URL: [Link]
- Status: [Created/Error]

## Workflow state (update in plan.md)
- Completed Skill: /upstream
- Result: [1-2 sentences: number of changes, PR URL]
- Next Skill: /analyze (next workflow run)
```

---

**Next step:** Review the PR in the Skill repo. After that, the next workflow run can be started with `/analyze`.