# CLAUDE.md

## Language

Respond in the language the user writes in. If the repository defines a preferred language in `docs/project.md`, use that language. Default: English.

## Documentation

Specifications and documentation live in the `docs/` directory. Docs must be dual-purpose:

- **Compact enough** for humans to read
- **Rich enough** to serve as context for other agents

## Agentic Workflow

This project uses the **agentic-workflow** methodology. Workflow rules, the Skill overview, and the full workflow chain are documented in [`AGENTS.md`](AGENTS.md) at the project root — read it before running any workflow Skill.

Workflow Skills live under `.claude/skills/<name>/SKILL.md` and are invoked as slash commands (e.g. `/axiom`, `/analyze`, `/next`). The Skills are harness-agnostic — they use generic terminology ("Main Agent", "Sub-Agent") that maps to Claude Code's subagents and tooling.

## Built-in Skills Used by the Workflow

Some workflow dimensions are covered by Claude Code's own built-in skills (no separate workflow Skill installed):

- **`/code-review`** — covers the code-review dimension
- **`/security-review`** — covers the security dimension
- **`/simplify`** — covers the simplification dimension

How `/qa` orchestrates these dimensions (which it dispatches as sub-agents vs. delegates to a built-in) is defined in the `/qa` Skill itself — it is the single authority on the QA mechanism.

If you want deeper coverage on a specific change, you can run **`/code-review ultra`** separately — note that ultra is billed in Anthropic usage credits ($15–25 per review) **outside** your Claude Code subscription. The workflow's `/qa` does NOT auto-trigger ultra; that decision is yours.

## Permissions

`.claude/settings.json` ships with a minimal allowlist. Extend it for your project's build/test commands.
