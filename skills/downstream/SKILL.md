---
name: downstream
description: Install or upgrade the agentic-workflow methodology into your USER SCOPE (~/.claude, ~/.copilot) so every project gets it. The single setup/update path — run it from a clone of the agentic-workflow repo. Detects existing installs and asks per user-modified Skill.
---

# Downstream

You render the canonical workflow into the user's **user scope**, so the Skills and rules are available in **every** project on the machine. This is the single install **and** upgrade path — the first run installs, later runs upgrade. There is no per-project install.

## Execution

The Main Agent performs `/downstream` **itself** — it reads the canonical sources, shows the plan, asks per user-modified file, and renders into the user scope. Mechanical file work plus interactive decisions; not delegated (Rule 2).

## Invocation

```
/downstream [--harness=claude|copilot|both]
```

- `--harness` — default: detect the harness you're in; `both` renders for both. Restrict with `claude` or `copilot`.

## Prerequisites

1. **Canonical source** — the agentic-workflow repo. Normally you run `/downstream` from a clone of it. If run elsewhere, ask the user for the repo path (or to `git clone` it), then `git pull` for the latest.
2. Confirm it is the real repo: it must contain `skills/`, `AGENTS.md`, and `skills/downstream/adapters.md` at its root. If not, stop and report.

## Source-of-truth (read from the repo)

| Source | Used as |
|---|---|
| `skills/<name>/SKILL.md` | canonical Skill bodies — 1:1 copy (both harnesses use the same format) |
| `AGENTS.md` | the workflow rules (appended to the rendered instruction file) |
| `skills/downstream/adapters.md` | target paths, skip-list, instruction-file preamble — **the single authority; do not hard-code these here** |

## Detection — classify the current user scope

Read `adapters.md` for the target paths, then scan the user scope and classify every relevant path:

1. **Each Skill** (at the harness's `skills/` path from `adapters.md`, honoring the skip-list):
   - **NEW** — not present yet · **identical** — matches canonical · **outdated** — differs, canonical newer · **user-modified** — differs and looks hand-edited · **orphan** — present in user scope but not in canonical sources.
2. **Instruction file** (`~/.claude/CLAUDE.md` / `~/.copilot/copilot-instructions.md`): NEW / matches the rendered output / differs.

When unsure between outdated and user-modified, classify as **user-modified** — the user decides.

## Plan output (BEFORE any changes)

Show ONE consolidated plan and ask for explicit confirmation:

```
/downstream plan — user scope
Harness: both | claude | copilot

NEW (created):     ~/.claude/skills/ (N) · ~/.claude/CLAUDE.md
                   ~/.copilot/skills/ (N) · ~/.copilot/copilot-instructions.md
UPDATE (canonical newer):  ~/.claude/skills/axiom/SKILL.md · ...
REQUIRES DECISION (user-modified):  <file> → diff, then keep yours / canonical / skip
SKIPPED (harness built-in):  Claude: review, security-review, simplify · Copilot: research, review
ORPHANS (kept, listed only):  ...
WILL NOT TOUCH:  settings.json / permissions · any project files

Proceed? [y/N]
```

If the user says no — stop, no changes.

## Execution order

After confirmation:

1. **NEW + UPDATE Skills** — copy `skills/<name>/SKILL.md` 1:1 to the harness target path (honor the skip-list). Create missing directories first.
2. **Instruction file** — render = harness preamble (from `adapters.md`) + the full `AGENTS.md` rules → write to the harness instruction path.
3. **REQUIRES-DECISION (user-modified)** — per file: show the diff, ask `[k]eep yours / [c]anonical / [s]kip`, apply.
4. **Orphans** — never auto-delete; list in the summary, the user decides.

## What `/downstream` does NOT do

- Does NOT touch any **project** files — the user scope is global; project-specifics belong in the project's own `AGENTS.md`/`CLAUDE.md`.
- Does NOT write the user's `settings.json` / permissions (lists needed permissions as a suggestion only).
- Does NOT commit. The user reviews and commits if they track their dotfiles.
- Does NOT auto-run other workflow Skills.

## Output Format

```
## /downstream Result — user scope
Harness: <claude|copilot|both>

### Installed / Updated (N)
- ~/.claude/skills/ (X) · ~/.claude/CLAUDE.md
- ~/.copilot/skills/ (Y) · ~/.copilot/copilot-instructions.md

### Your decisions (user-modified)
| File | Decision |

### Skipped (built-ins) / Orphans (kept)
- ...

## Workflow State (update in plan.md)
- Completed Skill: /downstream
- Result: [N skills installed/updated, harness]
- Next Skill: /axiom (in your target project)
```

---

**Next step:** Open any project and run `/axiom` — the workflow is now available everywhere. Re-run `/downstream` after a `git pull` to upgrade.
