---
name: bridge-debug
description: "[Bridge SDD] Systematic root-cause investigation for bugs and failures. Part of the bridge plugin — invoke via /bridge:debug"
---

# Bridge Debug

Systematic root-cause investigation for bugs and failures. Four phases: investigate, analyze, hypothesize, implement.

**Iron Law: NO fixes without root cause. Do not patch symptoms.**

## Procedure

### Phase 0: Context Gathering

1. **Read the bug description from the arguments.** If no description was provided, ask: "What bug or error are you seeing? Paste the error message, describe the behavior, or point me to the failing code."

2. **Check for Bridge feature context.** Look for `.bridge/wip/` directories. If the bug relates to an active feature, read its `BRIEF.md` and `PLAN.md` for architectural context and intended behavior.

### Phase 1: INVESTIGATE

3. **Gather evidence.** Read error messages, logs, and stack traces. Identify the exact trigger and failure point. Reproduce the issue if possible.

4. **Map the affected files.** List every file mentioned in the error or stack trace. Read each one.

5. **Do NOT guess at causes yet.** Only collect facts. Note what you observe without interpretation.

### Phase 2: ANALYZE

6. **Trace the code path.** Follow execution from the trigger point to the failure. Identify every function call, data transformation, and conditional branch along the way.

7. **Identify the broken component.** Determine which specific component, function, or data flow is responsible for the failure.

8. **Document the chain of causation.** Write down: trigger → intermediate steps → failure. Be precise.

### Phase 3: HYPOTHESIZE

9. **Form 2-3 hypotheses ranked by likelihood.** For each hypothesis:
   - State what the root cause would be
   - Describe what evidence would confirm it
   - Describe what evidence would refute it

10. **Test each hypothesis.** Run targeted checks — read specific code, run specific commands, add diagnostic output. Confirm or eliminate each hypothesis.

11. **Identify the root cause.** One hypothesis should survive testing. If none do, return to Phase 1 with new information.

### Phase 4: IMPLEMENT

12. **Fix the root cause.** Make the minimal change that addresses the actual problem, not its symptoms.

13. **Verify the fix.** Run the reproduction steps from Phase 1. Confirm the error no longer occurs.

14. **Run tests.** Execute the project's test suite to confirm no regressions.

15. **Commit the fix.** Make an atomic commit describing the root cause and the fix.

16. **Suggest next step.** Report: "Fixed! Run `/bridge:review` to verify the full feature still works."
