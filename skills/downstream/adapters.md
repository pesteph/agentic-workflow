# Harness Adapters

> Single source of truth for **where** and **how** the workflow is rendered into each harness's **user scope**. `/downstream`, `/upstream`, and `/retro` all read this file — do not duplicate these tables into the Skill files.

The canonical sources are `skills/<name>/SKILL.md` (the Skill bodies) and `AGENTS.md` (the workflow rules). Both harnesses use the **same `SKILL.md` format**, so rendering a Skill is a 1:1 copy — there is no per-harness conversion. The only harness-specific knowledge is: target paths, the skip-list, and a short instruction-file preamble. That is this file.

## Why user scope

The methodology is installed **once into the user scope** and is then available in **every** project on that machine. There is no per-project install. Project-specific conventions live in the project's own `AGENTS.md`/`CLAUDE.md`; the user scope holds only what applies to all projects.

## Target paths

| What | Claude Code | GitHub Copilot CLI |
|---|---|---|
| Skills | `~/.claude/skills/<name>/SKILL.md` | `~/.copilot/skills/<name>/SKILL.md` |
| Instruction file | `~/.claude/CLAUDE.md` | `~/.copilot/copilot-instructions.md` |
| Invocation | `/<name>` | `/<name>` |

Notes:
- Copilot Code uses **Skills** (`SKILL.md`), not Agents — same directory/frontmatter format as Claude. (`~/.agents/skills/` is an accepted alternative location; this repo targets `~/.copilot/skills/`.)
- `/downstream` never writes the user's `settings.json` (permissions are user-managed). If a Skill needs extra permissions, `/downstream` lists them in its summary as a suggestion — it does not apply them.

## Skip-list — which Skills are NOT rendered for which harness

A Skill is skipped when the harness already provides a semantically equivalent built-in. Skipping is **more important in user scope**: a user-scope Skill with the same name would *override* the harness built-in.

| Skill | Claude | Copilot | Reason |
|---|---|---|---|
| `research` | render | **skip** | Copilot has a built-in `/research`; Claude has none |
| `review` | **skip** | **skip** | Both harnesses provide a code-review built-in |
| `security-review` | **skip** | render | Claude provides built-in `/security-review` (same name) |
| `simplify` | **skip** | render | Claude provides built-in `/simplify` (same name) |
| `downstream` | render | render | The install/upgrade path itself (see below) |
| all others | render | render | No built-in equivalent |

`/upstream`, `/retro`, `/next`, and the rest are rendered to both.

## Instruction-file rendering

The user-scope instruction file = **harness preamble (below) + the full `AGENTS.md` rules**, concatenated. `AGENTS.md` is the single source for the rules; the preamble carries only the small harness-specific framing.

### Claude preamble (`~/.claude/CLAUDE.md`)

```markdown
# Agentic Workflow (user scope)

## Language
Respond in the language the user writes in. If the repository defines a preferred language in `docs/project.md`, use that. Default: English.

## Documentation
Specifications and documentation live in the `docs/` directory. Docs must be dual-purpose: compact enough for humans, rich enough as agent context.

## Built-in Skills used by the workflow
These dimensions are covered by Claude Code built-ins (no workflow Skill rendered): `/code-review`, `/security-review`, `/simplify`. `/qa` orchestrates them; `/qa` is the single authority on the QA mechanism.

## Permissions
Permissions live in your own `~/.claude/settings.json` and are yours to manage; `/downstream` does not touch them.

<!-- the AGENTS.md rules are appended below this preamble -->
```

### Copilot preamble (`~/.copilot/copilot-instructions.md`)

```markdown
# Agentic Workflow (user scope)

## Language
Respond in the language the user writes in. Default: English.

## Documentation
Specifications and documentation live in the `docs/` directory. Docs must be dual-purpose: compact enough for humans, rich enough as agent context.

## Built-in Skills used by the workflow
These dimensions are covered by Copilot CLI built-ins (no workflow Skill rendered): `/research`, `/review`.

<!-- the AGENTS.md rules are appended below this preamble -->
```

## Bootstrapping

`/downstream` is the single entry point. This distribution repo registers `/downstream` as a runnable Skill (in `.claude/skills/` and `.github/skills/`) so you can run it from a fresh clone. After the first run, all workflow Skills live in your user scope and are available in every project; re-running `/downstream` upgrades them.
