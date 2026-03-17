---
name: setup
description: Set up VibeFlow authentication and verify connection to the MCP server.
argument-hint: ""
disable-model-invocation: true
user-invocable: true
allowed-tools: Bash, Read
---

# VibeFlow Setup

Verify and configure VibeFlow authentication.

## Steps

1. **Check token**: Run `echo $VIBEFLOW_API_TOKEN` to see if the token is set.

2. **If missing**:
   - Tell the user: "`VIBEFLOW_API_TOKEN` is not set."
   - Guide them:
     1. Log in to https://cloud.axiomstudio.ai
     2. Go to Settings → API Keys → Generate New Key
     3. Add to shell profile: `export VIBEFLOW_API_TOKEN="your-token-here"`
     4. Restart Claude Code for the MCP server to pick it up

3. **If present**: Verify connection by calling `list_projects`.
   - Success: "Connected. Found N projects."
   - Auth error: "Token is invalid or expired. Regenerate at cloud.axiomstudio.ai."

4. **Check custom server**: If `VIBEFLOW_MCP_URL` is set, report the custom URL. Otherwise confirm using default (cloud.axiomstudio.ai).
