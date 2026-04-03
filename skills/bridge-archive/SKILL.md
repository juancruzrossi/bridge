---
name: bridge-archive
description: "[Bridge SDD] Move completed features from wip to archive. Part of the bridge plugin — invoke via /bridge:archive"
---

# Bridge Archive

Move completed features from `.bridge/wip/` to `.bridge/archive/`.

## Procedure

1. **Check for active features.** List all directories in `.bridge/wip/`. If none exist, report: "No active features in `.bridge/wip/`. Nothing to archive." and stop.

2. **Select the feature to archive.**
   - If an argument was provided, use it as the feature name. Verify it exists in `.bridge/wip/<name>/`. If not found, list available features and ask the user to pick one.
   - If multiple features exist and no argument was given, list them and ask: "Which feature do you want to archive?"
   - If only one feature exists, confirm: "Archive `<name>`? (y/n)"

3. **Move the feature directory.** Run:
   ```bash
   mkdir -p .bridge/archive/
   mv .bridge/wip/<name>/ .bridge/archive/<name>/
   ```

4. **Confirm completion.** Report:
   ```
   Feature '<name>' archived. .bridge/wip/<name>/ → .bridge/archive/<name>/
   ```
