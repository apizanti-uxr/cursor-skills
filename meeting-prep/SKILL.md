---
name: meeting-prep
description: >-
  Status check and meeting prep (/brief). Requires all five MCPs healthy
  first. Pulls Jira, Calendar, the local daily wiki, and Confluence.
  Renders a Cursor Canvas. Use when called by daily-rituals /brief.
disable-model-invocation: true
---

# Meeting Prep (/brief)

Read `~/.cursor/skills/daily-rituals/sources.md` first.
If the wiki root does not exist, read and execute
`~/.cursor/skills/daily-rituals/SKILL.md` (first-run wiki create).

---

## Required MCP verification (hard gate)

Read and execute `~/.cursor/skills/mcp-healthcheck/SKILL.md` before any mode.

**Any still down** → stop. Do not render a canvas.
**All five healthy** → continue.

---

## Mode detection

- `/brief` → Mode 1: general status check
- `/brief [meeting name or time]` → Mode 2: meeting-specific prep
- `/brief [person name]` → Mode 3: 1:1

Jira project key and cloudId come from `sources.md`
(`YOUR_JIRA_PROJECT`, `YOUR_ATLASSIAN_CLOUD_ID`).

---

## Mode 1 — General status check

```
get_events(today)
searchJiraIssuesUsingJql(assignee = currentUser() AND updated >= -3d ORDER BY updated DESC)
Read <wiki-root>/log/daily.md
```

Carry-forward from yesterday's EOD (`in progress` / `blocked`).

Canvas: write `brief.canvas.tsx` in the current workspace `canvases/` folder.
Do not hardcode a username or machine path.

1. STATUS CHECK + time
2. Done today
3. Up next — next 2 meetings
4. Open / blocked Jira
5. Carry forward
6. "Anything not in Jira today?"

If the user describes undocumented work, offer to create a Jira issue via
the Atlassian MCP.

---

## Mode 2 — Meeting-specific prep

Find the event on today's calendar. Branch on title:

**Sprint planning** — Jira `project = YOUR_JIRA_PROJECT AND sprint in openSprints()`

**Standup** — Jira updated since yesterday + last EOD in `log/daily.md`

**Design / stakeholder review** — relevant Jira issue, Confluence search,
`ref/project-*.md` if it exists

**1:1** — `ref/person-*.md` if it exists; calendar history with that person

**Retro** — `project = YOUR_JIRA_PROJECT AND sprint in closedSprints()`

**Other** — Confluence/Jira search on the meeting title

After the canvas, always ask if any Jira tickets should be updated.

---

## Mode 3 — 1:1

Resolve email from calendar; `lookupJiraAccountId` with cloudId from sources.
Lookback: since last 1:1, max 30 days.

Pull Slack DMs, calendar history, shared Jira, `ref/person-<name>.md` if
present, Drive files they can edit.

Canvas `brief.canvas.tsx`: talking points, Slack threads, shared Jira,
wiki notes, shared docs.

Chat: top 3 talking points + next meeting time.

---

## Fallback

Unclear meeting type → Mode 1.

Do not render if any of the five MCPs are down.
Query failures after a passed gate → note in the canvas footer and continue.
