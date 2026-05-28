# CLAUDE.md

This is the **agentic-workflow** skill repository. Its only purpose is to distribute the agentic-workflow methodology into your projects.

## What this repo provides

- **`/init <target-path>`** — installs the agentic-workflow methodology into a target project. This is the **only slash command** you should call when working IN this repo.

The workflow Skills (`/axiom`, `/analyze`, etc.) are intentionally NOT registered as runnable here — they belong in your target projects, not in this distribution repo. Their canonical sources live under [`skills/`](skills/); `/init` is the mechanism that deploys them.

## Learn the methodology

The full workflow rules and Skill overview live in [`AGENTS.md`](../AGENTS.md). Read it to understand what `/init` will deploy.

## Available Skills here

| Skill | Purpose |
|---|---|
| `/init` | Install / upgrade the agentic-workflow methodology in a target project |
