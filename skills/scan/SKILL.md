---
name: scan
description: Re-scan the codebase and refresh existing VibeFlow project contexts with current state.
argument-hint: "<project-name>"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash, Grep, Glob, Agent
---

# Refresh Project Contexts

Re-scan the codebase for **$1** and update existing VibeFlow contexts.

## Steps

1. **Find project**: Call `list_projects` and find "$1". If not found, tell the user to run `/vibeflow:onboard $1` first.

2. **Load existing contexts**: Call `list_contexts(project_id)` to get current context titles and IDs.

3. **Scan codebase** using Explore agents (same parallel scan as onboard):
   - Architecture & structure
   - Tech stack & dependencies
   - Patterns & conventions

4. **Diff and update**: For each existing context:
   - Read the current context body with `get_context(id)`
   - Compare with fresh scan results
   - If changes are found, call `update_context(id, body: <updated>)` with the new content
   - Preserve any human-added notes or sections — append new findings, don't overwrite manual edits

5. **Create missing contexts**: If the scan reveals context types that don't exist yet, create them.

6. **Report what changed**:
   ```
   Contexts refreshed for "$1":
   - Updated: Architecture, Tech Stack
   - Unchanged: Conventions, Project Overview
   - Created: <any new ones>
   ```
