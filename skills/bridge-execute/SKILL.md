---
name: bridge-execute
description: "[Bridge SDD] Wave-based parallel execution with fresh subagents and atomic commits. Part of the bridge plugin — invoke via /bridge:execute"
---

# Bridge Execute — Wave-Based Parallel Execution

You are executing an implementation plan from a PLAN.md produced by bridge-plan.

## 1. Locate the feature

1. List directories under `.bridge/wip/`.
2. If exactly one feature directory exists, use it automatically.
3. If multiple exist and the user passed `$ARGUMENTS` matching a feature name, use that one.
4. If multiple exist and no argument matches, list them and ask: "Which feature should I execute?"
5. Read `.bridge/wip/<feature>/PLAN.md`. If it does not exist, stop and tell the user to run `/bridge:plan` first.
6. Read `.bridge/wip/<feature>/BRIEF.md` for context. If it does not exist, warn but continue with PLAN.md only.

## 2. Parse tasks and build the dependency graph

1. Extract the task table from PLAN.md — each row has: task number, title, dependencies, wave, estimate.
2. Extract the detailed task descriptions from the "Task details" section.
3. Extract the "Waves" section to determine execution order.
4. Build a dependency graph: for each task, record which tasks must complete before it can start.
5. Validate the graph: check for circular dependencies. If found, stop and report the cycle to the user.

## 3. Execute wave by wave

For each wave (starting from Wave 1):

### 3a. Prepare the wave

1. Identify all tasks in the current wave.
2. Verify all dependencies for each task are satisfied (completed successfully in previous waves).
3. If any dependency failed and was not resolved, ask the user how to proceed before starting the wave.
4. Announce: **"Starting Wave N — Tasks [X, Y, Z]"**

### 3b. Dispatch parallel subagents

For each task in the wave, dispatch a subagent using the Agent tool with the following prompt structure:

```
You are executing Task <N>: <title>

## Context
<Full BRIEF.md content>

## Implementation Plan
<Full PLAN.md content>

## Your Task
<Detailed task description from PLAN.md, including What, Why, Files, Acceptance>

## Instructions
1. Implement the task as described.
2. Only modify the files listed in the task (or closely related files if needed).
3. Follow existing code style and conventions in the codebase.
4. After implementation, verify your work:
   - If tests exist: run the relevant test suite and ensure tests pass.
   - If a build system exists: run the build and ensure it succeeds.
   - If a linter is configured: run it and fix any issues.
   - If none of the above exist: manually verify the acceptance criteria.
5. Make an atomic commit for this task using conventional commits format.
   - Commit message: "feat(<feature>): <task-title>" or appropriate type (fix, refactor, etc.)
   - Only include files related to this task in the commit.
6. Report back: what you did, what files you changed, verification results.
```

Key principles for subagent dispatch:
- Each subagent gets a **fresh context window** — no shared state between subagents. This prevents context rot.
- Each subagent receives the full BRIEF.md and PLAN.md so it understands the broader picture.
- Each subagent is responsible for its own verification and commit.
- Dispatch all tasks in the wave **in parallel** using separate Agent tool calls.

### 3c. Collect results

1. Wait for all subagents in the wave to complete.
2. For each task, record the outcome: **completed** or **failed**.
3. Print a wave summary:

```
Wave N complete:
  ✓ Task X: <title> — <brief summary of changes>
  ✓ Task Y: <title> — <brief summary of changes>
  ✗ Task Z: <title> — FAILED: <error summary>
```

### 3d. Handle failures

If any task in the wave failed:

1. Report the failure details clearly: what went wrong, which files were affected, error output.
2. Ask the user how to proceed. Present options:
   - **Retry**: Re-dispatch the failed task with additional context about the failure.
   - **Skip**: Mark the task as skipped and continue. Warn if downstream tasks depend on it.
   - **Abort**: Stop execution entirely. Report progress so far.
3. If the user chooses to skip a task that has dependents, list the affected downstream tasks and confirm.
4. Wait for user decision before proceeding.

### 3e. Advance to next wave

1. Update the dependency graph: mark completed tasks as satisfied.
2. Move to the next wave.
3. Repeat from step 3a.

## 4. Progress tracking

Maintain a running progress summary throughout execution. After each wave, display:

```
Progress: N/M tasks completed | Wave K/T done
Completed: [1, 2, 3]
Remaining: [4, 5]
Failed: [6] (skipped)
```

## 5. Finalize

After all waves complete (or execution is aborted):

1. Print a final execution report:
   - Total tasks: completed, failed, skipped.
   - List of all commits made (hash + message).
   - Any warnings or notes from subagents.
2. If all tasks completed successfully, suggest: **"Ready to review? Run `/bridge:review`"**
3. If some tasks failed or were skipped, suggest the user address remaining issues before review.
