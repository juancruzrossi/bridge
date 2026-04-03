---
name: bridge-review
description: "[Bridge SDD] Plan vs implementation verification with goal-backward analysis. Part of the bridge plugin — invoke via /bridge:review"
---

# Bridge Review — Plan vs Implementation Verification

You are running the bridge-review skill. Follow these steps exactly.

## Step 0: Setup

1. Determine the feature name from `$ARGUMENTS`. If empty, scan `.bridge/wip/` for a single directory — use it. If multiple exist, ask the user which feature to review.
2. Set `WIP=.bridge/wip/<feature>/`.
3. Read `$WIP/BRIEF.md` and `$WIP/PLAN.md`. If either is missing, abort with: "Missing BRIEF.md or PLAN.md. Run /bridge:plan first."

## Step 1: Code Quality Check (optional)

1. Attempt to invoke `Skill("simplify")` to check for code quality issues in files changed by this feature. If the skill is not available, skip this step silently and continue.
2. Note any findings — they will be included in the review report under "Code Quality".

## Step 2: Layer 1 — Plan vs Implementation (Completeness Check)

1. Parse every task from `PLAN.md`. Each task typically has: description, expected files, expected functions/components, expected tests.
2. For each task, verify:
   - **Files created/modified** — use Glob and Read to confirm the files exist and were changed.
   - **Functions/components added** — use Grep to confirm the expected symbols exist in the codebase.
   - **Tests written** — use Glob and Grep to confirm test files exist and cover the task.
3. Assign a status to each task:
   - `✓ completed` — all expected artifacts found and substantive.
   - `⚠️ partial` — some artifacts found but others missing or incomplete.
   - `✗ missing` — task not implemented at all.
4. Calculate completion score: `X/Y tasks completed` (partial counts as 0.5).

## Step 3: Layer 2 — Goal-Backward Verification

1. Read the **Problem** section of `BRIEF.md`.
2. Derive "observable truths" — concrete, testable statements that must hold if the problem is truly solved. Example: "A user can POST /api/orders and receive a 201 with an order ID."
3. For each observable truth, verify 4 levels:

   **Level 1: EXISTS**
   - The artifact (file, function, route, component) exists at the expected path.
   - Use Glob and Read to confirm.

   **Level 2: SUBSTANTIVE**
   - The artifact contains real implementation, not stubs or placeholders.
   - Detect anti-patterns:
     - Empty function/handler bodies
     - `TODO`, `FIXME`, `PLACEHOLDER`, `NotImplementedError`, `pass` as sole body
     - Hardcoded mock/dummy return values
     - Commented-out core logic
   - Use Grep and Read to inspect contents.

   **Level 3: WIRED**
   - The artifact is connected to the rest of the system.
   - Check: is it imported somewhere? Is the route registered? Is the component rendered?
   - Use Grep to search for imports, references, route registrations.
   - Flag orphaned code that exists but is never called.

   **Level 4: FUNCTIONAL**
   - The artifact actually works when invoked.
   - Run existing tests (`npm test`, `pytest`, `cargo test`, or whatever the project uses).
   - If no tests exist for this truth, note it as a gap.
   - Check test output for passes and failures.

4. Assign a status per truth:
   - `PASS` — all 4 levels verified.
   - `PARTIAL` — EXISTS + SUBSTANTIVE but not fully WIRED or FUNCTIONAL.
   - `FAIL` — any level fails.

## Step 4: Generate REVIEW.md

1. Create `$WIP/REVIEW.md` with this structure:

```markdown
# Review: <feature-name>

**Date:** <current date>
**Verdict:** <PASS | PASS WITH WARNINGS | FAIL>

## Completion Score

**X/Y tasks completed**

| Task | Status | Notes |
|------|--------|-------|
| <task description> | ✓ / ⚠️ / ✗ | <details> |

## Goal-Backward Verification

### Truth: "<observable truth>"

| Level | Status | Evidence |
|-------|--------|----------|
| EXISTS | ✓ / ✗ | <path or detail> |
| SUBSTANTIVE | ✓ / ✗ | <detail> |
| WIRED | ✓ / ✗ | <import/route/reference> |
| FUNCTIONAL | ✓ / ✗ | <test result or gap> |

**Result:** PASS / PARTIAL / FAIL

<!-- Repeat for each truth -->

## Issues

### Blocking
- <issue description>

### Warnings
- <issue description>

### Info
- <issue description>

## Code Quality (via /simplify)
- <findings from Step 1, if any>
```

2. Determine the verdict:
   - **PASS** — all tasks completed, all truths pass, no blocking issues.
   - **PASS WITH WARNINGS** — all tasks completed, all truths pass, but warnings exist.
   - **FAIL** — any task missing, any truth fails, or any blocking issue exists.

## Step 5: Report to User

1. Print a summary of the review to the conversation.
2. Based on the verdict:
   - **PASS or PASS WITH WARNINGS:** Say: "All good! Run /bridge:archive to archive this feature."
   - **FAIL:** Say: "Issues found. Fix them and run /bridge:review again, or use /bridge:debug to investigate."
