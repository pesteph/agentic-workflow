---
name: discuss
description: Reveal gray areas, make assumptions explicit, and capture user preferences before the concept. Use this Skill when unclear points or implicit assumptions need to be clarified before an architecture decision.
---

# Discuss

You reveal gray areas, make implicit assumptions explicit, and capture user preferences — BEFORE the concept is created.

## Execution

The Main Agent performs `/discuss` **itself** — do NOT delegate it to a Sub-Agent. This Skill depends on direct interaction with the user via `ask_user`. A Sub-Agent cannot interact with the user.

## Why a separate Skill?

`/analyze` opens up the problem space. Between the analysis and any external research, the user holds knowledge that no search can find: hidden assumptions, unstated preferences, and implicit decisions. `/discuss` surfaces those **first** — gray areas, unresolved questions, missing user preferences — so the subsequent `/research` round only asks questions the user genuinely cannot answer, and `/conceptualize` works on a solid foundation.

## Approach

### Phase 1: Identify gray areas

Read the research results and the problem analysis from `/analyze`. For each topic, identify the **gray areas** — places where several sensible paths exist and the user needs to decide.

**Gray-area types by topic:**

| Topic area | Typical gray areas |
|---|---|
| Architecture | Where do new components live? Which abstraction level? Which coupling? |
| API/interfaces | Response format, error handling, versioning, breaking changes |
| UI/visual | Layout, density, interaction, mobile vs. desktop |
| Workflow/process | Order, required vs. optional, automation vs. manual |
| Data model | Schema design, normalization, migration, defaults |
| Scope | What is included, what is outside? MVP vs. full feature? |

### Phase 2: Reveal assumptions

Go through the results so far and identify **implicit assumptions** — things treated as given without explicit confirmation.

For each assumption, check:

| Dimension | Question |
|---|---|
| **Evidence** | Is there a source that supports it? (code, docs, user statement) |
| **Consequence** | What happens if the assumption is FALSE? |
| **Calibration** | High (proven), medium (plausible), low (guessed) |

**Assumptions with calibration "low" or with high consequences if false → MUST be clarified with the user.**

### Phase 3: Socratic clarification

Clarify gray areas and assumptions with the user. **Rules:**

1. **One question per message** — Do not bundle multiple questions. Wait for the user's answer before posing the next question. The user leads, the model follows.
2. **Prefer multiple choice** — Use `ask_user` with `choices`. No catch-all options like "Other"
3. **Show trade-offs** — For each option, briefly explain what is gained and what is lost
4. **Provide context** — The user must understand WHY the question matters (1 sentence)
5. **Challenge answers instead of just accepting them** — This is a DISCUSS, not a questionnaire. After each user answer: present **at least 3 concrete counter-arguments**, each with a source (codebase file:line, research URL, or prior user decision). Only when the counterarguments are weak or the user consciously accepts them is the decision considered clarified.
6. **Respect the final decision** — Once the user has heard the counterarguments and sticks with the choice: accept it and move on. Do not circle endlessly.

**Question order:** Scope questions first (what is in/out?), then architecture, then details.

### Phase 4: Structure the result

Summarize all results. This is the input for `/conceptualize`.

## Decision IDs

All results receive decision IDs according to Rule 19:
- Gray areas: **D-G-XX** (sequential, e.g. D-G-01, D-G-02)
- User preferences: **D-U-XX** (sequential, e.g. D-U-01)
These IDs are referenced by `/conceptualize` and `/design`.

## Output Format

```
## Discuss Result

### Clarified Gray Areas
| # | Topic | Question | User Decision | Rationale |
|---|-------|----------|---------------|-----------|
| D-G-01 | [Topic] | [What was unclear?] | [Decision] | [Why this was decided] |

### Reviewed Assumptions
| # | Assumption | Evidence | Calibration | Consequence if wrong | Status |
|---|------------|----------|-------------|----------------------|--------|
| A-01 | [Assumption] | [Source or "none"] | High/Medium/Low | [Impact] | ✅ confirmed / ❌ disproven / ⚠️ open |

### Captured User Preferences
| # | Area | Preference | Source |
|---|------|------------|--------|
| D-U-01 | [Area] | [What the user wants] | [Reference to discussion point] |

### Open Points (if any)
[Points that could not be clarified by the user → are handed to /research as open questions, or to /conceptualize if no research is needed]

## Workflow State (update in plan.md)
- Completed Skill: /discuss
- Result: [Number] Gray Areas clarified, [number] assumptions reviewed, [number] preferences captured
- Next Skill: /research (if open factual questions remain) — otherwise /conceptualize
- Context for next Skill: Discuss result as the decision basis; remaining open factual questions as input for /research
```

**No placeholders (Rule 18):** All decisions and results must be worded concretely. No vague placeholders — the Main Agent includes the full forbidden list from Rule 18 when delegating.

💡 Context maintenance: `/discuss` can be token-intensive when there are many questions. If the question set is large: work in blocks (scope first, then architecture, then details) and consider context compaction between blocks.

---

**Next step:** `/research` if open factual questions remain after the discussion — otherwise jump directly to `/conceptualize` to evaluate options based on the clarified gray areas and user preferences.