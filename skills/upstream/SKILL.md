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

### 1. Gather upstream candidates

The staging buffer now lives in **project files and the user scope**, not in per-project Skill copies — so scan THREE sources:
- **Retro suggestions** — from plan.md (section "Upstream Suggestions") or the chat context
- **User-scope Skills vs. canonical** — Skills the user hand-edited in their user scope (`~/.claude/skills/`, `~/.copilot/skills/`) that differ from the repo's `skills/<name>/SKILL.md`
- **Project `AGENTS.md`/`CLAUDE.md`** — project-specific rules that are generic enough to promote into the canonical `AGENTS.md` or a Skill

The filter is always: *is this generic enough to help all consumers, or is it project-specific?* Project-specific adaptations can still have general value — decide together, do not auto-filter.

### 2. Prepare the Skill repo

- Navigate to the Skill repo (locally or clone it)
- `git pull` to fetch the latest version
- Identify the affected Skill files in the repo

### 3. Create a change overview

Before discussing diffs in detail — first provide an overall overview:

- Compare the user-scope Skills (`~/.claude/skills/<name>/SKILL.md` and/or `~/.copilot/skills/<name>/SKILL.md`, per `skills/downstream/adapters.md`) with the canonical `skills/<name>/SKILL.md` in the Skill repo
- Both harness copies derive from the same canonical — if they diverge from each other, flag it as a separate finding before upstreaming
- Also surface the project-`AGENTS.md`/`CLAUDE.md` rules flagged in step 1 as upstream candidates
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
- If a new Skill should be skipped on a particular harness (because of a built-in equivalent), update the skip-list table in `skills/downstream/adapters.md`
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