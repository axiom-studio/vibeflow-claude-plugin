# VibeFlow Claude Plugin

Thin bootstrap plugin that connects Claude Code to the [VibeFlow](https://cloud.axiomstudio.ai) MCP server for autonomous agent sessions.

## What it does

- Registers the VibeFlow MCP server with Claude Code
- Provides `/vibeflow:init <project-name> [persona]` to start a session
- All agent behavior, rules, and personas are delivered by the server — the plugin stays minimal

## Setup

1. Get an API token from [cloud.axiomstudio.ai](https://cloud.axiomstudio.ai) → Settings → API Keys
2. Add to your shell profile:
   ```bash
   export VIBEFLOW_API_TOKEN="your-token-here"
   ```
3. Install the plugin (see below)

## Install

**Step 1** — Add the marketplace:
```bash
claude plugin marketplace add axiom-studio/vibeflow-claude-plugin
```

**Step 2** — Install the plugin:
```bash
claude plugin install vibeflow@axiom-vibeflow
```

**From local path** (for development/testing):
```bash
claude --plugin-dir /path/to/vibeflow-claude-plugin
```

## Permissions

Add to `.claude/settings.json` or `~/.claude/settings.json` to auto-allow MCP tools:

```json
{
  "permissions": {
    "allow": ["mcp__vibeflow"]
  }
}
```

## Skills

### Project Lifecycle

| Command | Description |
|---------|-------------|
| `/vibeflow:setup` | Verify token and MCP server connection |
| `/vibeflow:onboard my-project` | First-time setup — scan codebase, create project, build contexts |
| `/vibeflow:scan my-project` | Re-scan codebase and refresh existing contexts |

### Session Management

| Command | Description |
|---------|-------------|
| `/vibeflow:init my-project [persona]` | Start an agent session (default: developer) |
| `/vibeflow:resume [persona]` | Resume a previous session from session file |
| `/vibeflow:status [project-name]` | Show session status and active work items |
| `/vibeflow:stop [persona]` | Graceful shutdown with context save |

### Visibility

| Command | Description |
|---------|-------------|
| `/vibeflow:log [project-name]` | Show recent agent activity and execution logs |
| `/vibeflow:handoff [project-name]` | Create a handoff summary for the next persona or human |

### Typical Workflow

```bash
/vibeflow:setup                              # First time: verify auth
/vibeflow:onboard my-project                 # First time: scan codebase → create project
/vibeflow:init my-project                    # Start working as developer
/vibeflow:init my-project architect          # Or as architect
/vibeflow:status                             # Check progress
/vibeflow:handoff                            # Summarize state for handoff
/vibeflow:stop                               # Done for the day
```

All agent behavior, rules, and personas are delivered by the server — the plugin provides entry points.

### Available Personas

| Persona | Role |
|---------|------|
| `developer` | Implements features, fixes bugs, writes tests (default) |
| `architect` | Designs systems, reviews architecture, makes technical decisions |
| `product-manager` | Defines requirements, creates feature specs, prioritizes backlog |
| `project-manager` | Tracks progress, identifies blockers, coordinates work |
| `qa-lead` | Verifies completed work, runs test suites, checks edge cases |
| `security-lead` | Reviews for vulnerabilities, audits dependencies, checks compliance |
| `customer` | Tests from end-user perspective, reports usability issues |

### Running Multiple Personas

Each persona needs its own Claude Code session. Open separate terminals:

```bash
# Terminal 1
claude
> /vibeflow:init my-project developer

# Terminal 2
claude
> /vibeflow:init my-project architect

# Terminal 3
claude
> /vibeflow:init my-project qa-lead
```

Each session writes its own `.vibeflow-session-<persona>` file, so they don't conflict. The VibeFlow server coordinates work distribution across all active personas.

## Plugin vs CLI

There are two ways to use VibeFlow with Claude Code:

| | **This Plugin** | **[vibeflow-cli](https://github.com/axiom-studio/vibeflow-cli)** |
|---|---|---|
| **Install** | Claude Code plugin | Standalone CLI tool |
| **Launch** | `/vibeflow:init` inside Claude Code | `vibeflow start` from terminal |
| **Single persona** | Great — just start a session | Works, but heavier setup |
| **Multi-persona** | Manual — you open separate terminals | Automated — one command launches all personas |
| **Session management** | You manage terminals yourself | CLI handles lifecycle, restarts, and monitoring |
| **Best for** | Quick single-agent work, trying VibeFlow out, CI/automation | Full team simulation, daily development workflow |

**When to use the plugin**: You want a single persona working on your project, or you're comfortable managing multiple terminal sessions yourself. Lightweight, no extra dependencies.

**When to use the CLI**: You want to spin up a multi-persona team (e.g., developer + architect + qa-lead) with one command. The CLI runs `vibeflow start my-project --personas developer,architect,qa-lead` and handles session creation, monitoring, and graceful shutdown.

## Custom Server

Set `VIBEFLOW_SERVER_URL` to point to a self-hosted instance:
```bash
export VIBEFLOW_SERVER_URL="https://your-server.com"
```
