---
name: morning-brief
description: >-
  Morning startup and midday check. Handles /startup, /morning, and /midday.
  Checks MCP health, pulls calendar, last EOD focus, open Jira, and
  carry-forwards, then renders a Cursor Canvas. Use when called by
  daily-rituals /startup, /morning, or /midday.
disable-model-invocation: true
---

# Morning Brief

Canvas brief after health checks. Read
`~/.cursor/skills/daily-rituals/sources.md` first.
If the wiki root in `sources.md` does not exist, read and execute
`~/.cursor/skills/daily-rituals/SKILL.md` (first-run wiki create) before
pulling data.

---

## Required MCP verification (all briefings)

Before /startup, /morning, or /midday, read and execute
`~/.cursor/skills/mcp-healthcheck/SKILL.md`.

All five MCPs must pass. Do not proceed with partial data.

**All five healthy** → continue.
**Any still down** → report and stop. Do not render a brief.

---

## /startup

1. Run MCP verification.
2. If Slack recovery needs a local container runtime, read
   `~/.cursor/skills/podman-healthcheck/SKILL.md` when that skill is installed.
3. All healthy → run `/morning`. Otherwise stop.

---

## /morning

### Step 1 — Verify MCPs
Only proceed if all five pass.

### Step 2 — Pull data in parallel

```
get_events(today, detailed: true)
searchJiraIssuesUsingJql(assignee = currentUser()
  AND status != Done AND updated >= -1d ORDER BY updated DESC)
Read <wiki-root>/log/daily.md
```

Calendar: include `accepted` + organizer. Exclude `declined`.

Carry-forward: scan yesterday's EOD for `in progress`, `blocked`, or
`unclear`. Surface as `[CARRY: DAY N]`.

### Step 3 — Render Canvas

Read `~/.cursor/skills-cursor/canvas/SKILL.md`.

Write `morning-brief.canvas.tsx` in the current workspace `canvases/` folder
(Cursor project canvases directory). Do not hardcode a username or machine path.

Layout:

1. Header — "Good morning" + date
2. Stats — meetings today | open Jira | carry-forward count
3. Meetings — time + title + attendees
4. From yesterday — "Tomorrow's focus" from last EOD
5. Open in Jira — KEY + status + summary
6. Carry Forward — only if items exist; `[STALE]` at DAY 3+

No emojis. No gradients. Use `useHostTheme()` tokens.

### Step 4 — Chat summary

```
Good morning. Canvas ready → [morning-brief]

Meetings today: N
Open Jira: N
Carry-forward: N items (M stale)

Anything to update in Jira before you start? (or skip)
```

---

## /midday

### Step 1 — Verify MCPs
Only proceed if all five pass.

### Step 2 — Pull data in parallel

Drive (today, read only):

```
search_drive_files(query: createdTime >= startOfToday AND trashed = false,
  order_by: createdTime desc, page_size: 10)
```

Slack since morning (`YOUR_SLACK_USERNAME` from `sources.md`):

```
search_messages(query: "from:YOUR_SLACK_USERNAME after:<today>", count: 20)
```

Jira since morning:

```
searchJiraIssuesUsingJql(assignee = currentUser() AND updated >= startOfDay())
```

### Step 3 — Render Canvas

Overwrite the same `morning-brief.canvas.tsx`.

1. Header — "Midday" + time
2. Transcripts — title + link + 2–3 topics
3. Drive edits — docs modified today
4. Slack — by channel, flag commitments
5. Jira — changes since morning
6. Up next — remaining calendar events

### Step 4 — Chat summary

```
Midday check. Canvas updated → [morning-brief]

Transcripts: N found
Slack: N messages
Jira: N changes

Anything to note before the afternoon? (or skip)
```

---

## Fallback

Do not render a brief if any of the five MCPs are down.

If a query fails **after** a passed gate, note it in the canvas footer and
continue. That applies only to individual queries, not a dead MCP server.
