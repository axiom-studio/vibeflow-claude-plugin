---
name: resume
description: Resume a previous VibeFlow agent session from an existing session file.
argument-hint: "[persona]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Write, Bash
---

# Resume VibeFlow Session

Resume an existing VibeFlow session for persona **${1:-developer}**.

## Steps

1. **Find session file**: Read `.vibeflow-session-${1:-developer}` in the current working directory.
   - If not found, list all `.vibeflow-session-*` files and show available sessions.
   - If none exist, tell the user: "No sessions found. Use `/vibeflow:init <project-name>` to start one."

2. **Extract session ID**: Read the session ID from the file (first line).

3. **Get git context**: Run `git branch --show-current` and `git remote get-url origin`.

4. **Re-initialize**: Call `session_init` with the existing session_id to get the full agent prompt:
   ```
   session_init(
     project_name: <infer from session or ask>,
     working_directory: <cwd>,
     git_branch: <current branch>,
     git_remote_url: <remote url>,
     persona: "${1:-developer}",
     session_id: <from file>,
     agent_type: "claude",
     agent_model: <your model ID>
   )
   ```

5. **Follow the returned prompt**: The server returns full Phase 1-4 instructions. Follow them exactly — skip Phase 1 steps that are redundant (project already exists), but honor all behavioral rules.
