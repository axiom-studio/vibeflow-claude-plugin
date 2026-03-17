---
name: log
description: Show recent VibeFlow execution logs — what agents did, commits made, and status changes.
argument-hint: "[project-name]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash
---

# VibeFlow Execution Logs

Show recent agent activity for project **${1}**.

## Steps

1. **Find project**: Resolve from `$1` or active session. Call `list_projects` to get project ID.

2. **Get recent work items**:
   - `list_features(project_id, minimal: true)` to find features
   - For each feature with recent activity: `list_todos(feature_id, minimal: true)`
   - `list_issues(project_id, minimal: true)`

3. **Fetch logs** for recent items:
   - For each recently completed or in-progress todo: `get_todo_logs(todo_id)`
   - For each recently completed or in-progress issue: `get_issue_logs(issue_id)`

4. **Display timeline** (most recent first):
   ```
   Recent Activity — <project-name>

   [2026-03-18 14:32] developer completed todo "Add auth middleware"
     → Commits: abc1234, def5678
     → Files: 4 added, 2 modified

   [2026-03-18 13:15] architect completed todo "Design API schema"
     → Document created: "API Schema v2"

   [2026-03-18 12:00] qa-lead rejected issue #42 "Login fails on Safari"
     → Reason: "Fix doesn't cover incognito mode"
   ```

5. **Limit to last 20 log entries** to keep output manageable.
