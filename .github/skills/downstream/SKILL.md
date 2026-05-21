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

For each Skill file in the Skill repo:

- Compare the version in the Skill repo with the local version in the consumer repo (`.github/skills/<name>/SKILL.md`)
- Create a clear diff per file
- Categorize changes:
  - **New in the Skill repo** — changes that do not yet exist locally
  - **Locally adapted** — places changed locally for project-specific reasons
  - **Conflicts** — places changed both locally and upstream

#### b) Workflow rules (agents.instructions.md)

- Compare the workflow rules in the Skill repo (e.g. `.github/instructions/agents.instructions.md` or `README.md`) with the local `.github/instructions/agents.instructions.md`
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

- Check whether `copilot-instructions.md` needs to be updated (Skill overview, workflow rules)
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

**Next step:** Check whether the updated Skills are consistent with the project. Update `copilot-instructions.md` if needed.