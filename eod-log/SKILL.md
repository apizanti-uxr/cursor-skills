---
name: eod-log
description: >-
  End-of-day log. Requires all five MCPs healthy first (mcp-healthcheck).
  Gathers today's calendar, Drive transcripts, Jira, and Slack, scores items,
  writes to the local wiki created by daily-rituals, and asks once about Jira
  actions. Use when called by daily-rituals /eod.
disable-model-invocation: true
---

# EOD Log

Runs after the MCP gate. Pause only at Step 5 (Jira actions).

Read `~/.cursor/skills/daily-rituals/sources.md` first.
If the wiki root does not exist, read and execute
`~/.cursor/skills/daily-rituals/SKILL.md` (first-run wiki create) before
listing Drive files.

---

## Step 0 — Verify all MCPs (hard gate)

Read and execute `~/.cursor/skills/mcp-healthcheck/SKILL.md` before listing
Drive files or scraping.

**Any still down** → report and **stop**. Do not write the wiki.
**All five healthy** → continue.

---

## Step 0.5 — List today's Drive files

```
search_drive_files(
  query: (createdTime >= startOfToday OR modifiedTime >= startOfToday) AND trashed = false,
  order_by: modifiedTime desc,
  page_size: 20
)
```

Present the list and ask which to ingest (`none` / `all` / `skip` / numbers).
For each selected file: `~/.cursor/skills/daily-rituals/SKILL.md` → `/ingest [url]`.

---

## Step 1 — Gather in parallel

**Calendar** — `get_events(today, detailed: true)`:
include `accepted` + organizer; exclude `declined`.

**Drive** — same search as 0.5; `get_doc_as_markdown` for each. Read only.

**Jira** — `assignee = currentUser() AND updated >= startOfDay()`

**Slack** — `from:YOUR_SLACK_USERNAME after:<today>` (username from `sources.md`).
Group by thread; flag commitments.

---

## Step 2 — Score (1–5)

Start at 1. Cap at 5.

| Signal | Boost |
|--------|-------|
| External stakeholder / customer | +2 |
| Senior teammate in the meeting or thread | +1 |
| Deliverable shipped (Jira Done/Review, doc shared) | +2 |
| Decision with rationale | +1 |
| Sprint ticket touched (`YOUR_JIRA_PROJECT` from sources) | +1 |
| Session ended blocked or open | +1 |
| Carry-forward again | +1 per day |
| Pure admin / scheduling | −1 |

4–5 → **[IMPORTANT]**.

---

## Step 2.5 — Carry-forward

1. Read `<wiki-root>/log/daily.md` — yesterday's EOD.
2. Items `in progress` / `blocked` / `unclear`.
3. Resolved today → `resolved`. Else increment DAY N; DAY 3+ → score 5 and `#stale`.
4. Unmentioned 2+ days and not `#blocked` → `auto-closed`.

---

## Step 3 — Write the log

Append at the **top** of `<wiki-root>/log/daily.md`:

```markdown
## YYYY-MM-DD

### EOD

**Signal:** N important items

**Summary:** [1 sentence]

**Meetings:**
- [title] — [outcome] [score: N/5]

**Jira:**
- [KEY-XXX] — [what changed]

**Drive:**
- [title] — [what happened] [link]

**Slack:**
- [who] → [commitment]

**Decisions:**
- [decision] | [why] | [alternatives]

**Carry Forward:**
- [DAY N] [item]

**Tomorrow's focus:** [1 thing]
```

---

## Step 4 — Ref pages

For each log item, create or update under `<wiki-root>/ref/` per daily-rituals
cross-linking. Update `index.md`.

---

## Step 5 — Jira actions (only pause)

```
EOD logged. Wiki updated.

JIRA — anything to do before tomorrow?
[suggested ticket actions]

Say yes, no, or describe what to update. (or skip)
```

Use Atlassian MCP to edit, comment, or transition. For a new ticket, create
it via the Atlassian MCP (do not assume a company-specific ticket skill).

---

## Step 6 — Report

Wiki files written, important items, carry-forwards, sources skipped.

---

## Fallback

Do not write an EOD if any of the five MCPs are down.

If a query fails after a passed gate, note it in SOURCES SKIPPED and continue.
