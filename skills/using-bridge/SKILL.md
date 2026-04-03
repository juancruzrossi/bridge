---
name: using-bridge
description: "[Bridge SDD] Session hook — detects complex tasks, suggests the SDD workflow, and resumes in-progress features from .bridge/wip/"
---

# Bridge SDD Framework

Bridge is a Spec-Driven Development framework. It provides a structured workflow that turns vague ideas into verified implementations through disciplined phases.

## Available Commands

| Command | Phase | Purpose |
|---------|-------|---------|
| `/bridge:interview` | Clarification | Socratic interview → generates BRIEF.md |
| `/bridge:plan` | Planning | Implementation plan with adversarial review → generates PLAN.md |
| `/bridge:execute` | Implementation | Wave-based parallel execution with fresh subagents |
| `/bridge:review` | Verification | Plan vs implementation + goal-backward verification |
| `/bridge:address-review` | Remediation | Fix blocking issues and warnings from REVIEW.md |
| `/bridge:quick` | Fast-path | Mini-interview + inline plan + execute + light review (no files) |
| `/bridge:debug` | Diagnosis | Root-cause investigation for bugs and failures |
| `/bridge:status` | State | Show progress of all features in .bridge/wip/ |
| `/bridge:archive` | Cleanup | Move completed feature from wip/ to archive/ |

## The Workflow

```
interview → plan → execute → review → archive
                                ↕         ↑
                              debug   address-review
```

## When to Suggest Bridge

Evaluate every user request. If the task is **complex, ambiguous, or multi-step** (new feature, architectural change, multi-file refactor), suggest starting with `/bridge:interview`.

Do NOT suggest bridge for:
- Simple bug fixes with obvious solutions
- Single-line changes or typo fixes
- Questions about existing code
- Tasks the user explicitly wants done quickly without process

## State Detection

On session start, check if `.bridge/wip/` exists in the current working directory. If it does:

1. List all feature directories in `.bridge/wip/`
2. For each, check which artifacts exist (BRIEF.md, PLAN.md, REVIEW.md)
3. Announce: "Bridge SDD: Found in-progress feature(s)" and suggest the next step

Example:
```
Bridge SDD: Found in-progress feature "auth-system"
  ✓ BRIEF.md (interview complete)
  ✓ PLAN.md (plan complete)
  ✗ REVIEW.md (not yet reviewed)
  → Suggest: /bridge:execute or /bridge:review
```

## Artifact Location

All Bridge artifacts live in `.bridge/` at the project root:

```
.bridge/
├── wip/<feature-name>/
│   ├── BRIEF.md      ← interview output
│   ├── PLAN.md       ← plan output
│   └── REVIEW.md     ← review output
└── archive/<feature-name>/
    ├── BRIEF.md
    ├── PLAN.md
    └── REVIEW.md
```
