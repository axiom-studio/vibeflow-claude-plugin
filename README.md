# VibeFlow Claude Plugin

Connect Claude Code to [VibeFlow](https://cloud.axiomstudio.ai) — autonomous AI agent sessions with multi-persona workflows, task tracking, and project context management.

## What is VibeFlow?

VibeFlow turns Claude Code into an autonomous agent that can plan, implement, test, and review code — all coordinated through a central server. You assign personas (developer, architect, QA lead, etc.) and VibeFlow distributes work, tracks progress, and maintains project context across sessions.

This plugin is the bridge between Claude Code and the VibeFlow server. It provides slash commands to manage your project and sessions. All agent intelligence — prompts, rules, task coordination — lives on the server, not in the plugin.

## Quick Start

### 1. Get an API token

Sign up at [cloud.axiomstudio.ai](https://cloud.axiomstudio.ai), then go to **Settings → API Keys → Generate New Key**.

Add to your shell profile (`~/.zshrc` or `~/.bashrc`):
```bash
export VIBEFLOW_API_TOKEN="your-token-here"
```

### 2. Install the plugin

Add the marketplace and install:
```bash
claude plugin marketplace add axiom-studio/vibeflow-claude-plugin
claude plugin install vibeflow@axiom-vibeflow
```

### 3. Verify the connection

```
/vibeflow:setup
```

This checks your token and confirms the MCP server is reachable.

### 4. Onboard your project

Navigate to your project directory and run:

```
/vibeflow:onboard my-project
```

This scans your codebase and creates a VibeFlow project with contexts that agents use to understand your code — architecture, tech stack, patterns, and conventions. You only need to do this once per project.

### 5. Start working

```
/vibeflow:init my-project
```

This starts an autonomous agent session. The server returns full instructions — the agent begins polling for work, picking up tasks, writing code, and committing changes.

## Commands

### Setting Up

#### `/vibeflow:setup`

Verify your authentication and MCP server connection. Run this first to make sure everything is configured correctly.

**When to use**: First time using the plugin, after rotating your API token, or if sessions fail to connect.

#### `/vibeflow:onboard <project-name>`

Scan your codebase and create a VibeFlow project with rich context. Launches parallel agents to analyze:
- **Architecture** — directory structure, entry points, module boundaries
- **Tech stack** — languages, frameworks, dependencies, infrastructure
- **Conventions** — coding patterns, naming, test structure

The results are stored as project contexts that all personas can reference during their work.

**When to use**: First time connecting a codebase to VibeFlow. If the project already exists, it will ask whether to update the existing contexts instead.

#### `/vibeflow:scan <project-name>`

Re-scan the codebase and refresh existing project contexts. Compares current code against stored contexts and updates what changed — without overwriting any manual notes you've added.

**When to use**: After major refactors, dependency upgrades, or architectural changes. Keeps the agents' understanding of your codebase current.

---

### Working

#### `/vibeflow:init <project-name> [persona]`

Start an autonomous agent session. The server returns the full agent prompt with instructions for:
- Loading project contexts and documents
- Polling for assigned work items
- Executing tasks (planning → implementing → done)
- Updating contexts when work is complete

The agent runs autonomously — picking up todos and issues, writing code, committing, and moving on to the next task.

**When to use**: Beginning of a work session. You can specify a persona to control what kind of work the agent focuses on.

```
/vibeflow:init my-project                    # Developer (default)
/vibeflow:init my-project architect          # Architecture and design
/vibeflow:init my-project product-manager    # Requirements and specs
```

#### `/vibeflow:resume [persona]`

Resume a previous session without specifying the project name. Reads the session file from your working directory and re-connects to the existing session.

**When to use**: Picking up where you left off — after closing a terminal, restarting Claude Code, or coming back the next day. The session file persists between restarts.

#### `/vibeflow:stop [persona]`

Gracefully stop the agent session. Commits any in-progress work as WIP, finalizes project contexts with what was accomplished, and ends the polling loop.

**When to use**: End of a work session, before closing the terminal, or when switching to a different task. The session file is kept so you can `/vibeflow:resume` later.

---

### Monitoring

#### `/vibeflow:status [project-name]`

Show a dashboard of your project: active sessions, feature progress, in-flight issues, and pending todos.

**When to use**: Checking progress, seeing what agents are working on, or getting an overview before starting work.

#### `/vibeflow:log [project-name]`

Show recent agent activity — completed tasks, commits made, status changes, and QA results. Displays a chronological timeline of what happened.

**When to use**: Understanding what agents did while you were away, reviewing work before merging, or debugging why something was done a certain way.

#### `/vibeflow:handoff [project-name]`

Create a structured handoff document capturing: what was done, what's in progress, blockers, and what's next. Saves it as a project document in VibeFlow and displays it in the terminal.

**When to use**: Switching between personas, handing off to a teammate, end-of-day summary, or before a review meeting.

---

## Personas

Personas determine what kind of work the agent focuses on and how it approaches tasks.

| Persona | Focus | Best for |
|---------|-------|----------|
| `developer` | Implementation, bug fixes, tests | Day-to-day coding (default) |
| `architect` | System design, technical decisions, code review | Planning new features, reviewing architecture |
| `product-manager` | Requirements, specs, backlog prioritization | Defining what to build and why |
| `project-manager` | Progress tracking, coordination, blockers | Keeping the pipeline moving |
| `qa-lead` | Test verification, edge cases, regression checks | Validating completed work |
| `security-lead` | Vulnerability review, compliance, dependency audit | Security hardening |
| `customer` | End-user testing, usability feedback | Validating UX and workflows |

### Running Multiple Personas

Each persona runs in its own Claude Code session. Open separate terminals:

```bash
# Terminal 1 — Developer writes code
claude
> /vibeflow:init my-project developer

# Terminal 2 — Architect reviews design
claude
> /vibeflow:init my-project architect

# Terminal 3 — QA validates completed work
claude
> /vibeflow:init my-project qa-lead
```

Each session writes its own `.vibeflow-session-<persona>` file. The VibeFlow server coordinates work distribution — the developer implements, the QA lead verifies, the architect reviews.

## Plugin vs CLI

There are two ways to use VibeFlow with Claude Code:

| | **This Plugin** | **[vibeflow-cli](https://github.com/axiom-studio/vibeflow-cli)** |
|---|---|---|
| **Install** | Claude Code plugin | Standalone CLI tool |
| **Launch** | `/vibeflow:init` inside Claude Code | `vibeflow start` from terminal |
| **Single persona** | Start a session and go | Works, but heavier setup |
| **Multi-persona** | Open separate terminals yourself | One command launches all personas |
| **Session management** | You manage terminals | CLI handles lifecycle, restarts, monitoring |
| **Best for** | Quick single-agent work, trying VibeFlow out, CI/automation | Full team simulation, daily multi-persona workflow |

**Use the plugin** when you want a single persona working on your project, or you prefer managing sessions yourself. Lightweight, no extra dependencies.

**Use the CLI** when you want a multi-persona team with one command. `vibeflow start my-project --personas developer,architect,qa-lead` launches everything and handles session lifecycle automatically.

## Permissions

To avoid permission prompts for VibeFlow MCP tools, add to your `.claude/settings.json` or `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["mcp__vibeflow"]
  }
}
```

## Custom Server

Point to a self-hosted VibeFlow instance:
```bash
export VIBEFLOW_MCP_URL="https://your-server.com/rest/v1/vibeflow/mcp"
```

## License

Apache-2.0
