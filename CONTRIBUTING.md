# Contributing

Thank you for wanting to contribute to Agentic Workflow! This document explains how the project is structured and how you can contribute effectively.

## Language

The project operates in **English** — code comments, documentation, commit messages, PRs, and issues. English terms are used where they are established (e.g. “Branch Protection", “Pull Request", “Skill”).

## The Upstream/Downstream Concept

This repo is the **upstream repo** for Agentic Workflow:

```
   ┌─────────────────────────────────────────┐
   │  pesteph/agentic-workflow  (Upstream)   │
   │  Authoritative Skills, rules, docs      │
   └─────────────────────────────────────────┘
            ▲                      │
            │ /upstream            │ /downstream
            │ (PR with improv.)    │ (fetch latest version)
            │                      ▼
   ┌─────────────────────────────────────────┐
   │  Your project repo  (Downstream)        │
   │  Uses Skills + project-specific         │
   │  adaptations                            │
   └─────────────────────────────────────────┘
```

**Specifically:**

- **Upstream** = this repo. The canonical versions of all Skills, workflow rules, and documentation live here under [`skills/`](skills/) and [`AGENTS.md`](AGENTS.md).
- **Downstream** = your machine / projects that use this workflow. The methodology is installed once into your **user scope** via `/downstream` (run from a clone of this repo); the same command upgrades it after `git pull`.
- **Contributing** = if you develop a Skill improvement in your downstream project, you use `/upstream` to submit it here as a PR. That way all users benefit.

## Who merges?

At the moment, [@pesteph](https://github.com/pesteph) is the sole maintainer and reviews all PRs. External contributors **must** submit changes via Pull Request — direct pushes to `main` are blocked.

## How to contribute?

### 1. Issue first (for larger changes)

For non-trivial changes (new Skills, rule changes, workflow adjustments), please open an [Issue](https://github.com/pesteph/agentic-workflow/issues/new/choose) first or start a [Discussion](https://github.com/pesteph/agentic-workflow/discussions). This helps us avoid duplicate work and discuss the approach up front.

For small fixes (typos, documentation clarifications), a PR directly is fine.

### 2. Fork & Branch

```bash
git clone https://github.com/<your-user>/agentic-workflow.git
cd agentic-workflow
git checkout -b feature/short-description
```

Branch naming convention:
- `feature/...` — new features or Skills
- `fix/...` — bug fixes
- `docs/...` — documentation-only changes
- `refactor/...` — restructurings without behavioral changes

### 3. Implement changes

Follow the workflow rules from [`AGENTS.md`](AGENTS.md). Especially relevant:

- **Rule 18 — No placeholders**: no `TBD`, `TODO`, `later`, `will be defined`, etc.
- **Rule 14 — Sources required**: back up statements with sources
- **Rule 13 — Fact-based**: no assumptions — ask or provide evidence
- **Rule 22 — Documentation required**: code changes require documentation updates

### 4. Commits

- Meaningful commit messages in English
- Small, focused commits (not “big bang”)
- If an agent (Copilot, Claude, etc.) was involved in the commit, feel free to add it as a `Co-authored-by:` trailer

Example:
```
Skill /analyze: Add pre-flight check for existing docs

Previously, /analyze analyzed the codebase without checking
whether docs/architecture.md already exists. That led to
duplicate work. Now /analyze reads docs/ first and
uses existing universe knowledge as a basis.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>
```

### 5. Open a Pull Request

- Fill out the PR template (go through the checklist)
- Description in English
- Reference linked issues (`Fixes #123`)
- For Skill changes: show a short example of what the Skill now does differently or better

### 6. Review

[@pesteph](https://github.com/pesteph) is automatically assigned as a reviewer via CODEOWNERS. Expect feedback — reviews are constructive and meant to ensure quality.

## Skill Structure

If you add a new workflow Skill, add it under the canonical directory:

```
skills/<skill-name>/
└── SKILL.md
```

(Not `.github/skills/` or `.claude/skills/` — those locations are reserved in this repo for exposing the `/downstream` meta-skill only. The canonical Skill bodies live in `skills/` and are rendered by `/downstream` into the user scope's `~/.copilot/skills/` or `~/.claude/skills/` as appropriate.)

`SKILL.md` must be self-contained and follow this structure (see existing Skills as a template):

1. **YAML frontmatter** with `name` and `description`
2. **Purpose** of the Skill (1–2 sentences)
3. **When to use** it (trigger conditions)
4. **Flow** (what the Skill specifically does)
5. **Output** (what comes out at the end)
6. **Next step** (which Skill typically follows)

Required frontmatter shape:

```yaml
---
name: <skill-name>          # lowercase, matches the directory name (e.g. "test-review")
description: <one sentence> # what the Skill does + when to use it; ends with a period
---
```

Both fields are mandatory. `name` must equal the directory name under `skills/`. `description` is what the harness shows when offering the Skill, so write it from the *user's* perspective ("Analyzes a GitHub issue and …") rather than the agent's.

If a new Skill should be SKIPPED for a particular harness (because that harness has a built-in equivalent), document the skip in the skip-list table — see [`skills/downstream/adapters.md`](skills/downstream/adapters.md).

## What we do NOT accept

- **Contributions that don't follow the English language convention of this project**
- **Skills without SKILL.md** or without a clear when/how/output
- **Workflow rule changes without rationale** (rules come from lessons learned, see `/retro`)
- **PRs with placeholders** (`TBD`, `TODO`, `will be added later`) — see Rule 18

## Code of Conduct

We expect respectful, constructive collaboration. Criticism should focus on content, not people. No harassment, no discriminatory behavior.

## License

By submitting a PR, you agree that your contribution will be published under the project's [MIT License](LICENSE).

## Questions?

[GitHub Discussions](https://github.com/pesteph/agentic-workflow/discussions) is the best place for questions and ideas.