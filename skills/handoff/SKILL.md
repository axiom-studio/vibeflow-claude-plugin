---
name: handoff
description: Create a context handoff summary — what was done, what's in progress, and what's next for another persona or human.
argument-hint: "[project-name]"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash, Grep, Glob
---

# Session Handoff

Create a handoff summary for project **${1}** capturing current state for the next persona or human.

## Steps

1. **Find project**: Resolve project from `$1` or active session. Call `list_projects` to get project ID.

2. **Gather state**:
   - `list_features(project_id, minimal: true)` — feature status overview
   - `list_issues(project_id, status: "implementing,in_review", minimal: true)` — in-flight issues
   - `list_contexts(project_id)` — existing context summaries
   - `git log --oneline -20` — recent commits
   - `git diff --stat` — uncommitted changes

3. **Build handoff document**: Create a structured summary:
   ```
   # Handoff: <project-name>
   Date: <today>
   Persona: <current persona>

   ## What was done
   - <completed features/todos/issues>

   ## In progress
   - <implementing items with current state>
   - <uncommitted changes if any>

   ## Blockers / Open questions
   - <anything unresolved>

   ## What's next
   - <ready_to_implement items>
   - <suggested priorities>

   ## Recent commits
   - <last 10 commits>
   ```

4. **Save to VibeFlow**: Call `create_document(project_id, title: "Handoff — <date>", content: <summary>)`.

5. **Output** the handoff summary to the terminal as well.
