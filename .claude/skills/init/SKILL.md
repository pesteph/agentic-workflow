---
name: init
description: Install the agentic-workflow methodology into a target project. Detects existing installs (also old layouts), supports clean installs and upgrades, asks the user about locally modified Skill files. Run this from inside the agentic-workflow repo.
---

# Init

You install the agentic-workflow methodology into a target project. This Skill is the ONLY user-facing Skill in this repo — it deploys the workflow into projects where it is actually used.

## Invocation

```
/init <target-path> [--harness=copilot|claude|both]
```

- **Required:** path to the target project (absolute or relative). If missing, ask the user for it.
- **Optional:** `--harness` — default `both`. Restrict to `copilot` or `claude` if requested.

## Pre-flight checks

1. The target path must exist and be a directory. Otherwise stop and report.
2. The target must NOT be the agentic-workflow repo itself (no self-install). Detect by checking whether the target contains BOTH `harnesses/` AND `skills/axiom/SKILL.md` at its root. If yes, refuse.
3. Read `--harness` argument; default to `both`.

## Source-of-truth paths in THIS repo

You read these as source material; the path is relative to the agentic-workflow repo root (i.e. relative to this SKILL.md it would be `../../../`):

| Source | Used as |
|---|---|
| `AGENTS.md` | Workflow rules, copied to target's `AGENTS.md` at the root |
| `skills/<name>/SKILL.md` | Canonical workflow Skill files |
| `harnesses/copilot/copilot-instructions.md` | Template for target's `.github/copilot-instructions.md` |
| `harnesses/claude/CLAUDE.md` | Template for target's `CLAUDE.md` (project root) |
| `harnesses/claude/settings.json` | Template for target's `.claude/settings.json` |

## Skip-list — which Skills are NOT installed for which harness

Because the harness already provides a semantically equivalent built-in:

| Skill | Copilot | Claude | Reason |
|---|---|---|---|
| `research` | skip | install | Copilot has built-in `/research`; Claude has none |
| `review` | skip | skip | Both harnesses provide a code-review built-in |
| `sec-review` | install | skip | Claude provides `/security-review` |
| `simplify` | install | skip | Claude provides `/simplify` |
| All other skills (axiom, analyze, discuss, conceptualize, deep-conceptualize, design, diaboli, brief, test-review, doc-review, qa, retro, upstream, downstream, next) | install | install | No built-in equivalent |

## Target paths after install

| Source file | Copilot target | Claude target |
|---|---|---|
| `AGENTS.md` | `<target>/AGENTS.md` | `<target>/AGENTS.md` |
| `skills/<name>/SKILL.md` | `<target>/.github/skills/<name>/SKILL.md` | `<target>/.claude/skills/<name>/SKILL.md` |
| `harnesses/copilot/copilot-instructions.md` | `<target>/.github/copilot-instructions.md` | — |
| `harnesses/claude/CLAUDE.md` | — | `<target>/CLAUDE.md` |
| `harnesses/claude/settings.json` | — | `<target>/.claude/settings.json` |

## Detection — classify the target's current state

Before doing anything, scan the target and classify every relevant path:

1. **Workflow-rules file location**:
   - **NEW** — neither `AGENTS.md` at root nor `.github/instructions/agents.instructions.md` exists
   - **UP-TO-DATE** — `AGENTS.md` exists and content matches canonical `AGENTS.md` in this repo
   - **OUTDATED** — `AGENTS.md` exists but content differs and target's is older / canonical newer
   - **OLD-LAYOUT** — `.github/instructions/agents.instructions.md` exists but no `AGENTS.md` at root → needs migration
   - **LOCALLY-MODIFIED** — target's `AGENTS.md` (or old-layout file) differs from canonical AND looks user-edited (unique content not in canonical history)

2. **For each Skill file in target** (search `.github/skills/<name>/` AND `.claude/skills/<name>/`):
   - **identical** — content matches canonical `skills/<name>/SKILL.md`
   - **outdated** — content differs, target is older
   - **locally-modified** — content differs, target looks user-edited
   - **orphan** — Skill exists in target but NOT in canonical sources (e.g. old `verify/`)

3. **Workflow-state files**:
   - Check for `<target>/plan.md`, `<target>/docs/`. These are NEVER touched by `/init`. Note their presence for the summary.

You can determine "outdated vs locally-modified" pragmatically: if the file's structure (frontmatter `name:`, headings) matches canonical but content elsewhere differs in non-trivial ways, treat as **locally-modified**. When in doubt, classify as locally-modified — the user will decide.

## Plan output (BEFORE any changes)

Show ONE consolidated plan and ask for explicit confirmation. Example format:

```
/init plan for <target-path>
Harness: both | copilot | claude

NEW (will be created):
  AGENTS.md
  CLAUDE.md
  .claude/settings.json
  .claude/skills/  (N skills)
  .github/copilot-instructions.md  (only if not present)
  .github/skills/  (N skills)

UPDATE (canonical is newer):
  .github/skills/axiom/SKILL.md
  ...

MIGRATE (workflow change):
  .github/instructions/agents.instructions.md → AGENTS.md at root
  .github/skills/verify/  → REMOVE (merged into /diaboli)

REQUIRES DECISION (locally modified):
  .github/skills/analyze/SKILL.md
  ...
  → For each, I will show you the diff and ask: keep yours / take canonical / skip.

SKIPPED (harness provides built-in):
  Copilot: research, review
  Claude:  review, sec-review, simplify

WILL NOT TOUCH:
  docs/    (your universe)
  plan.md  (workflow state)
  any non-workflow files in <target>

Proceed? [y/N]
```

If the user says no — stop, no changes.

## Execution order

After confirmation:

1. **NEW and UPDATE files** — copy directly. Create missing directories first.
2. **MIGRATE** — copy the new file to the new path, then delete the old file/directory. For the `verify/` removal: just `rm -r` it. (Git is the safety net.)
3. **For each REQUIRES-DECISION file**:
   - Show the diff between target's current content and canonical content.
   - Ask: `[k]eep mine / [c]anonical / [s]kip (defer to manual review)`
   - Apply the decision.
4. **Orphans** (Skills in target that don't exist in canonical, except `/verify` which is the known migration case): just list them in the summary; do NOT delete them automatically. The user decides what to do with their own additions.

## Summary output

After execution:

```
## /init Result

Target: <path>
Harness: <copilot|claude|both>

### Created (N files)
- AGENTS.md
- .github/copilot-instructions.md
- .github/skills/  (X skills: axiom, analyze, ...)
- CLAUDE.md
- .claude/settings.json
- .claude/skills/  (Y skills: axiom, analyze, ...)

### Updated (N files)
- ...

### Migrated (N files)
- .github/instructions/agents.instructions.md → AGENTS.md

### Removed (N files)
- .github/skills/verify/SKILL.md  (merged into /diaboli)

### Locally Modified — Your Decisions
| File | Decision |
|------|----------|
| .github/skills/analyze/SKILL.md | kept yours |
| .github/skills/qa/SKILL.md      | took canonical |

### Skipped (X skills — harness provides built-in)
- Copilot: research, review
- Claude:  review, sec-review, simplify

### Found but not touched
- docs/        (your universe — N files)
- plan.md      (workflow state)
- Any user-added Skills not in canonical: <list> (kept as-is)

## Next step
Open <target> in your harness and run `/axiom` to define the universe.
```

## What `/init` does NOT do

- Does NOT touch `docs/` in the target — the universe belongs to the user's project.
- Does NOT touch `plan.md` — workflow state.
- Does NOT touch any non-workflow files in the target (CI configs, source code, build files, etc.).
- Does NOT make commits in the target — the user reviews changes and commits themselves. Git is the safety net.
- Does NOT auto-run other workflow Skills (`/axiom`, `/analyze`, …) — those are for the user to run in the target.
- Does NOT write `.gitignore` entries (e.g. for `settings.local.json`) — the user manages that.

## Important notes for the executor

- Read source files (`AGENTS.md`, `skills/...`, `harnesses/...`) from THIS repo, not from anywhere else.
- Use file tools (Read/Write/Glob/Grep, or harness equivalents) to manipulate the target. Use `git mv` or `git rm` only if the target is itself a git repo AND the user explicitly opts into git-tracked moves; otherwise plain file operations.
- Be explicit and conservative. When in doubt, ask the user, do not assume.
- This Skill is the only one in this distribution repo — do NOT chain into other workflow Skills here.
