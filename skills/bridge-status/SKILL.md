---
name: bridge-status
description: "[Bridge SDD] Show progress of all features in .bridge/wip/ and suggest next steps. Part of the bridge plugin — invoke via /bridge:status"
---

# Bridge Status

Show progress of all active Bridge features and suggest next steps.

## Procedure

1. **Check for the `.bridge/wip/` directory.** If it does not exist or is empty, report: "No active Bridge features. Start one with `/bridge:interview`" and continue to step 5.

2. **List all feature directories in `.bridge/wip/`.** For each feature directory, check which of these files exist:
   - `BRIEF.md` — created by `/bridge:interview`
   - `PLAN.md` — created by `/bridge:plan`
   - `REVIEW.md` — created by `/bridge:review`

3. **Determine the next step for each feature.** Apply this logic:
   - Missing `BRIEF.md` → Next: `/bridge:interview`
   - Has `BRIEF.md`, missing `PLAN.md` → Next: `/bridge:plan`
   - Has `BRIEF.md` and `PLAN.md`, missing `REVIEW.md` → Next: `/bridge:execute` or `/bridge:review`
   - Has all three → Next: `/bridge:archive`

4. **Display the status table.** Format output exactly like this:
   ```
   Bridge SDD Status

   Active features:
     <feature-name>:
       ✓ BRIEF.md (interview complete)
       ✓ PLAN.md (plan complete)
       ✗ REVIEW.md
       → Next: /bridge:execute or /bridge:review

     <feature-name>:
       ✓ BRIEF.md (interview complete)
       ✗ PLAN.md
       ✗ REVIEW.md
       → Next: /bridge:plan
   ```

5. **Check `.bridge/archive/`.** If it exists, count the subdirectories and report: `Archived: <N> features`. If it does not exist, report: `Archived: 0 features`.
