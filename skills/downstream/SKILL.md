---
name: downstream
description: Pulls the latest version of the Skills from the original Skill repo and merges changes into the project. Use this Skill to bring Skills up to date.
---

# Downstream

You pull the latest version of the Skills from the original Skill repo and merge changes into the project.

## Execution

**Delegate** the implementation to a Sub-Agent. Give it the full Skill instructions and the path to the Skill repo.

## Prerequisites

The user must provide:
1. **Skill repo path** — local path or GitHub URL of the original Skill repo

If the user does not provide a path, ask for it.

## Approach

### 1. Update the Skill repo

- Navigate to the Skill repo (local or clone it)
- Run `git pull` to fetch the latest version
- Identify all Skill files in the repo

### 2. Create a diff

#### a) Skill files

The Skill repo's canonical source for every workflow Skill is at `skills/<name>/SKILL.md` (in the Skill repo). Locally, in this consumer project, Skills live at:
- `.github/skills/<name>/SKILL.md` for Copilot
- `.claude/skills/<name>/SKILL.md` for Claude

Detect which of these directories exist locally — diff against whichever is present. For dual-harness targets, both should be identical; if not, treat the mismatch as a separate finding.

For each canonical Skill file in the Skill repo:

- Compare with the matching local copy (Copilot path and/or Claude path)
- Create a clear diff per file
- Categorize changes:
  - **New in the Skill repo** — changes that do not yet exist locally
  - **Locally adapted** — places changed locally for project-specific reasons
  - **Conflicts** — places changed both locally and upstream

Skip rules: some Skills are intentionally not installed for a given harness (because the harness provides a built-in equivalent). The skip-list is documented in the Skill repo's `/init` Skill. Do not "pull" a skipped Skill into a path where it is not supposed to live.

#### b) Workflow rules (`AGENTS.md`)

- Compare the canonical `AGENTS.md` in the Skill repo with the local `AGENTS.md` (at the consumer project's root)
- Check for **new rules** added upstream
- Check for **changed rules** (same number, different content)
- Check the workflow chain, model selection table, and Skill overview for differences
- New general rules MUST be presented to the user — they affect all Skills

### 3. Diff review with the user

For each Skill file with differences:

- Show the diff clearly
- Mark what comes **new from the Skill repo** and what is a **local adaptation**
- Discuss with the user: what should be adopted?
- Local adaptations can intentionally be project-specific — do not overwrite automatically
- Skill repo changes can also flow into local adaptations (smart merge)

### 4. Apply changes

- Apply only the jointly agreed changes to the local Skill files
- Preserve project-specific adaptations that should remain
- In case of conflicts: show both versions and let the user decide

### 5. Detect new Skills

- Check whether the Skill repo contains new Skills that do not yet exist locally
- Offer to adopt new Skills
- Check whether new Skills need to be added in `copilot-instructions.md` (Skill overview)

### 6. Update local docs

- Check whether harness entry files need to be updated (`AGENTS.md` at root, `.github/copilot-instructions.md`, `CLAUDE.md`)
- Update Skill descriptions if steps or behavior changed due to the merge
- Goal: local docs and Skill files must not drift apart

## Output Format

```
## Downstream

### Diff Overview
| File | Skill Repo Version | Local Version | Difference | Status |
|-------|-------------------|---------------|-------------|--------|
| [Path] | [Commit/date] | [Commit/date] | New/Changed/Conflict | ⬇️/⚠️/✅ |

### Diff Review
#### [Skill Name]
[Diff with markers: NEW / LOCAL / CONFLICT]

### Decisions
| File | Change | Decision |
|-------|----------|--------------|
| [Path] | [What] | adopt/keep/merge |

### New Skills
| Skill | Description | Adopted |
|-------|-------------|------------|
| [Name] | [Purpose] | Yes/No |

## Workflow State (update in plan.md)
- Completed Skill: /downstream
- Result: [1-2 sentences: number of updated Skills, new Skills]
- Next Skill: [context-dependent]
```

---

**Next step:** Check whether the updated Skills are consistent with the project. Update the harness entry files (`AGENTS.md`, `.github/copilot-instructions.md`, `CLAUDE.md`) if needed.