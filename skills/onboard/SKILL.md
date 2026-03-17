---
name: onboard
description: First-time project onboarding — scans codebase and creates a VibeFlow project with contexts, architecture docs, and tech stack analysis.
argument-hint: "<project-name>"
disable-model-invocation: true
user-invocable: true
allowed-tools: Read, Bash, Grep, Glob, Agent
---

# Onboard Project to VibeFlow

Set up **$1** as a VibeFlow-managed project by scanning the codebase and building contexts.

## Steps

1. **Check if project exists**: Call `list_projects` and search for "$1".
   - If found, confirm with user: "Project already exists. Update contexts instead?" If yes, switch to a context refresh flow (update existing contexts). If no, stop.
   - If not found, proceed to create it.

2. **Create project**: Call `create_project(name: "$1", description: <inferred from repo>)`.

3. **Scan codebase in parallel** using Explore agents. Launch these concurrently:

   **Agent 1 — Architecture & Structure**:
   - Identify the project type (monorepo, backend, frontend, full-stack, library, CLI, etc.)
   - Map top-level directory structure and what each directory does
   - Identify key entry points (main files, routers, app bootstrap)
   - Note the build system, package manager, and project layout

   **Agent 2 — Tech Stack & Dependencies**:
   - Read package.json, go.mod, requirements.txt, Cargo.toml, or equivalent
   - List primary frameworks and libraries
   - Identify database, messaging, caching, and infrastructure dependencies
   - Note CI/CD configuration if present

   **Agent 3 — Patterns & Conventions**:
   - Sample 5-10 source files across different directories
   - Identify coding patterns (DI, repository pattern, hooks, etc.)
   - Note naming conventions (files, functions, variables)
   - Identify test patterns and test file locations

4. **Create contexts** from scan results. For each, call `create_context(project_id, title, body)`:

   - **Project Overview**: What this project is, who it's for, high-level architecture
   - **Tech Stack**: Languages, frameworks, key dependencies, infrastructure
   - **Architecture**: Directory structure, layers, key modules, data flow
   - **Conventions**: Coding patterns, naming, file organization, test patterns

5. **Create a project document** with `create_document(project_id, title, content)`:
   - **Getting Started**: How to build, run, and test the project (inferred from Makefile, package.json scripts, README, etc.)

6. **Report summary**:
   ```
   Project "$1" onboarded to VibeFlow.
   - Project ID: <id>
   - Contexts created: 4
   - Documents created: 1

   Next: Run /vibeflow:init $1 to start an agent session.
   ```
