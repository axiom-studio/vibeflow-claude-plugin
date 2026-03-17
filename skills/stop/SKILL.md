---
name: stop
description: Gracefully stop the VibeFlow polling loop, finalize contexts, and end the session.
argument-hint: "[persona]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash
---

# Stop VibeFlow Session

Gracefully stop the VibeFlow session for persona **${1:-developer}**.

## Steps

1. **Find session**: Read `.vibeflow-session-${1:-developer}` for the session ID. If not found, report no active session.

2. **Check pending work**: Call `list_issues(project_id, status: "implementing")` and `list_todos` for implementing items claimed by this session.
   - If any are in progress, commit uncommitted changes as WIP and publish a final execution log noting the pause.

3. **Finalize contexts**: If any work was completed this session:
   - Update project context with a summary of what was done
   - Update feature contexts for features worked on

4. **Output session summary**:
   ```
   Session stopped: ${1:-developer}
   - Completed: N todos, M issues
   - In progress: X items (left as implementing)
   - Commits: [hashes]
   ```

5. **End polling**: Stop the `wait_for_work` loop. The session file is kept for reuse with `/vibeflow:resume`.
