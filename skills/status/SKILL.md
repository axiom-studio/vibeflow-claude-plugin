---
name: status
description: Show VibeFlow session status, active work items, and project summary.
argument-hint: "[project-name]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash
---

# VibeFlow Status

Show current session and project status.

## Steps

1. **Detect sessions**: List all `.vibeflow-session-*` files in the current directory. For each, show the persona and session ID.

2. **Find project**: If `$1` is provided, use it. Otherwise infer from session context or ask.
   Call `list_projects` and find the project.

3. **Query status** (use minimal/filtered calls):
   - `list_features(project_id, minimal: true)` — all features with status
   - `list_issues(project_id, status: "planning,ready_to_implement,implementing,in_review", minimal: true)` — active issues only
   - For each non-done feature: `list_todos(feature_id, status: "planning,ready_to_implement,implementing,in_review", minimal: true)`

4. **Display summary**:
   ```
   Project: <name> (ID: <id>) — Status: <status>

   Active Sessions:
     developer — session-xxx (connected)
     architect — session-yyy (connected)

   Features:
     [done] Feature Name (3/3 todos)
     [implementing] Feature Name (1/3 todos)

   Active Issues:
     [implementing] Issue title (high priority)

   Active Todos:
     [ready_to_implement] Todo title — Feature Name
   ```

5. **Check session health**: For each detected session, call `session_heartbeat` to verify connectivity.
