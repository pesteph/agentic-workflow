# CLAUDE.md

This is the **agentic-workflow** skill repository. Its only purpose is to distribute the agentic-workflow methodology into your **user scope**, so it works in every project.

## What this repo provides

- **`/downstream [--harness=claude|copilot|both]`** — installs / upgrades the methodology into your user scope (`~/.claude/skills/` + `~/.claude/CLAUDE.md`, and/or `~/.copilot/skills/` + `~/.copilot/copilot-instructions.md`). This is the **only slash command** you call when working IN this repo. Run it once to install; re-run after a `git pull` to upgrade.

The workflow Skills (`/axiom`, `/analyze`, etc.) are intentionally NOT registered as runnable here — after `/downstream` they live in your user scope and are available everywhere. Their canonical sources live under [`skills/`](skills/); the per-harness paths and skip-list live in [`skills/downstream/adapters.md`](skills/downstream/adapters.md).

## Learn the methodology

The full workflow rules and Skill overview live in [`AGENTS.md`](AGENTS.md). Read it to understand what `/downstream` deploys.

## Available Skills here

| Skill | Purpose |
|---|---|
| `/downstream` | Install / upgrade the agentic-workflow methodology into your user scope |
