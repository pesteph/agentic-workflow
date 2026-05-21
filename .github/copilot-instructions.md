# Copilot Instructions

## Language

Respond in the language the user writes in. If the repository defines a preferred language in `docs/project.md`, use that language. Default: English.

## Documentation

Specifications and documentation live in the `docs/` directory. Docs must be dual-purpose:

- **Compact enough** for humans to read
- **Rich enough** to serve as context for other agents

## Agentic Workflow

Workflow rules, the Skill overview, and the full workflow chain are documented in [`instructions/agents.instructions.md`](instructions/agents.instructions.md). This split keeps `copilot-instructions.md` itself within the 4 KB size budget that GitHub Copilot Code Review applies when loading repository-level instructions for PR reviews; the workflow rules are loaded separately as agent/chat context where no such limit applies.
