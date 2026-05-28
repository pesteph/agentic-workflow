# Copilot Instructions

This is the **agentic-workflow** skill repository. Its only purpose is to distribute the agentic-workflow methodology into your projects.

## What this repo provides

- **`/install-workflow <target-path>`** — installs the agentic-workflow methodology into a target project. This is the **only slash command** you should call when working IN this repo.

The workflow Skills (`/axiom`, `/analyze`, etc.) are intentionally NOT registered as runnable here — they belong in your target projects, not in this distribution repo. Their canonical sources live under [`skills/`](../skills/); `/install-workflow` is the mechanism that deploys them.

## Learn the methodology

The full workflow rules and Skill overview live in [`AGENTS.md`](../AGENTS.md). Read it to understand what `/install-workflow` will deploy.

## Language

Respond in the language the user writes in. Default: English.
