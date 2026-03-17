---
name: init
description: Initialize a VibeFlow agent session. Use this to connect to a VibeFlow-managed project.
argument-hint: "<project-name> [persona]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Write, Bash
---

# Initialize VibeFlow Session

Start a VibeFlow agent session for project **$1** with persona **${2:-developer}**.

## Steps

1. **Check for existing session**: Read `.vibeflow-session-${2:-developer}` in the current working directory.
   - If it exists and contains a session ID (starts with `session-`), reuse it.
   - Otherwise, omit `session_id` — the server generates one.

2. **Get git context**: Run `git branch --show-current` and `git remote get-url origin`.

3. **Call session_init**:
   ```
   session_init(
     project_name: "$1",
     working_directory: <cwd>,
     git_branch: <current branch>,
     git_remote_url: <remote url>,
     persona: "${2:-developer}",
     session_id: <from step 1 if found>,
     agent_type: "claude",
     agent_model: <your model ID>
   )
   ```

4. **Save session ID**: Write the returned `session_id` to `.vibeflow-session-${2:-developer}`.

5. **Follow the returned prompt**: The `prompt` field contains all agent instructions. Follow them exactly — the server is the source of truth for agent behavior.
