---
name: neural.interview
description: "[Neural SDD] Socratic interview that creates concise CONTEXT.md and optional ADRs inside .neural/wip/<feature>/. Part of the neural plugin — invoke via /neural.interview"
keep-coding-instructions: true
---

# Neural Interview

## Rules

- Respond in the user's language.
- Ask one question at a time.
- Include your recommended answer when you can.
- If code/docs can answer the question, inspect them instead of asking.
- Cross-check user claims against code. If code contradicts the user, surface it before continuing.
- Challenge vague terms, hidden assumptions, scope creep, and contradictions with code.
- Update `CONTEXT.md` as decisions crystallize. Keep it small.
- Create ADRs only for decisions that are hard to reverse, surprising without context, and real trade-offs.

## Setup

1. Check git silently: `git rev-parse --is-inside-work-tree 2>/dev/null`.
2. Ask feature name. Normalize to kebab-case.
3. Create `.neural/wip/<feature>/`.
4. Ask for the raw feature description.
5. Scan relevant code/docs before the first hard question:
   - `CONTEXT.md`, `CONTEXT-MAP.md`
   - `docs/adr/`
   - related source files/tests
   - `.neural/wip/*/CONTEXT.md`, `.neural/archive/*/CONTEXT.md` when relevant

## Interview

Resolve these in risk order:

1. Problem: why this exists.
2. Scope: included behavior and explicit boundaries.
3. Language: canonical domain terms, avoided aliases.
4. Relationships: ownership, cardinality, lifecycle.
5. Scenarios: normal flow, edge cases, failure states.
6. Constraints: technical/business limits.
7. Decisions: chosen approach, rejected alternatives.
8. Non-goals: what not to build.
9. Acceptance: concrete done criteria.

Use pressure only when useful:

- Evidence: "Give me a concrete case."
- Assumption: "What is that based on?"
- Boundary: "Where does this stop?"
- Essence: "Is that the root problem or a symptom?"

When terms conflict, stop and resolve the term before moving on.

## CONTEXT.md

Write only useful sections:

```md
# <Feature>

**Git:** yes|no
**Branch:** <branch>

## Problem
<2-3 sentences>

## Scope
- <included>

## Language

**<Term>**:
<one-sentence definition>
_Avoid_: <aliases>

## Relationships
- A **Term** belongs to exactly one **Other Term**

## Decisions
- Decision: <specific choice>

## Constraints
- <limit>

## Scenarios
- <flow or edge case>

## Non-goals
- <excluded>

## Decision Boundaries
- Agent may decide: <low-risk choices>
- Ask user before: <high-risk choices>

## Acceptance Criteria
- [ ] <testable outcome>

## Open Items
- <unresolved item>
```

Do not add generic programming terms to `Language`.

## ADRs

Path: `.neural/wip/<feature>/docs/adr/0001-short-title.md`

Format:

```md
# <Decision>

<1-3 sentences: context, decision, why.>
```

Skip ADRs unless they earn their keep.

## Finish

Before finalizing, list inferred assumptions. If the user corrects any, update `CONTEXT.md`.

If on a stable branch (`main`, `master`, `develop`, `stage`, `staging`, `production`, `release`), ask whether to create `feature/<slug>`, `enhancement/<slug>`, `fix/<slug>`, `hotfix/<slug>`, or stay.

Report:

```text
Interview complete for <feature>
Context: .neural/wip/<feature>/CONTEXT.md
ADRs: <count>
Open items: <count>
Next: /neural.plan
```
