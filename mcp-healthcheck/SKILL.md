---
name: mcp-healthcheck
description: >-
  Checks that all critical MCP servers are live before daily rituals.
  Required before /startup, /morning, /midday, /eod, and /brief. Tests Slack,
  Atlassian, Google Workspace, Dataverse, and Smartsheet. Attempts recovery,
  then stops if any source is still down.
disable-model-invocation: true
---

# MCP Healthcheck

Hard gate for `/startup`, `/morning`, `/midday`, `/eod`, and `/brief`.
Do **not** scrape, render a brief, or write an EOD log until every MCP below
returns a valid response.

Server IDs come from `~/.cursor/skills/daily-rituals/sources.md` when that
file exists. Fall back to the names in the table if `sources.md` is not
installed yet.

## Critical MCPs to check

Run all five test calls **in parallel**:

| MCP | Typical server ID | Test call |
|-----|-------------------|-----------|
| Slack | from `sources.md` | `whoami` |
| Atlassian (Jira/Confluence) | from `sources.md` | `getAccessibleAtlassianResources` |
| Google Workspace | from `sources.md` | `get_events` (today, max 1) |
| Dataverse | from `sources.md` | `identify_dataproducts` (any query) |
| Smartsheet | from `sources.md` | `get_resource_guide` with `intents: ["smartsheet-intelligence"]` |

## Step 1 — Test each MCP

- **Success** = valid response → ✓
- **Failure** = error or no response → ✗

## Step 2 — Recovery

**Slack down:**
1. If a local container runtime is required for that Slack MCP, run
   `podman-healthcheck` when that skill is installed.
2. Otherwise tell the user to re-authorize or refresh Slack MCP credentials
   in Cursor Settings → MCP. Do not ask them to paste tokens into chat.

**Atlassian down:**
- 401 → re-authorize in Cursor Settings → MCP
- Other → toggle off/on

**Google Workspace down:**
- Re-authorize / toggle the Google Workspace MCP

**Dataverse or Smartsheet down:**
- Toggle off/on in Cursor Settings → MCP

Re-test only the failures. Do not proceed while any remain down.

## Step 3 — Report

```
MCP STATUS
✓ Slack
✓ Atlassian
✓ Google Workspace
✓ Dataverse
✓ Smartsheet
```

**All five healthy** → continue the calling routine.

**Any still down** → report how to fix them and **stop**.
Do not gather partial data. Do not render a brief. Do not write the EOD log.

Run `/morning`, `/midday`, `/eod`, and `/brief` from **chat**, not unattended
automation — OAuth MCPs need an active Cursor session.
