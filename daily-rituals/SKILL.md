---
name: daily-rituals
description: >-
  Start, mid, and end of day tracking. Pulls calendar, meeting transcripts,
  Jira, Drive, and Slack, then writes to a local wiki the skill creates on
  first run. Commands: /morning, /midday, /eod, /brief, /ingest.
  Use when the user says /morning, /midday, /eod, /brief, or /ingest.
disable-model-invocation: true
---

# Daily Rituals

The user's job is to **approve or skip**. The agent does the tracking.
Never ask the user to write or fill in anything — gather first, present a
checklist, write only after approval.

> **Setup:** Copy [sources.example.md](sources.example.md) to `sources.md`
> and fill in your values before the first run.

Read [sources.md](sources.md) for MCP names, Jira config, and the wiki root.

This skill is the router for `/morning`, `/midday`, `/eod`, and `/brief`.
Install the sibling folders in this repo next to it:
`morning-brief`, `eod-log`, `meeting-prep`, `mcp-healthcheck`.

---

## First run — create a new wiki

Before any command, read `sources.md` and resolve `Wiki root`.

If `sources.md` is missing: stop and tell the user to copy `sources.example.md`.

If the wiki root **does not exist**, create a **new empty wiki**. Never reuse
or copy another wiki. Never write to Google Drive.

Create:

```
<wiki-root>/
  index.md
  log/
    daily.md
  ref/
```

`index.md` starter:

```markdown
# Daily wiki

Local map of notes created by the daily-ritual skills.
```

`log/daily.md` starter:

```markdown
# Daily log

Newest entries at the top.
```

Then tell the user the wiki was created at `<wiki-root>` and continue.

If the wiki root already exists, use it as-is. Do not delete or reformat it.

---

## Data sources

| Source | Method | Notes |
|--------|--------|-------|
| Google Calendar | Google Workspace MCP | |
| Google Drive | Google Workspace MCP | Read only — never write to Drive |
| Slack | Slack MCP from `sources.md` | |
| Dataverse | Dataverse MCP from `sources.md` | Health-check only unless needed |
| Jira / Confluence | Atlassian MCP from `sources.md` | |
| Wiki (local files) | Read/Write file tools | Only write destination |

**In Cursor chat:** `/startup`, `/morning`, `/midday`, `/eod`, `/brief` must
pass `mcp-healthcheck` first (all five MCPs). Stop if any are down.

---

## Wiki layout (created on first run)

```
<wiki-root>/
├── index.md           ← map of pages
├── log/
│   └── daily.md       ← running log, newest at top
└── ref/               ← one file per project, person, or decision
    ├── project-*.md
    ├── person-*.md
    └── YYYY-MM-DD-*.md
```

**Naming:** `ref/project-<slug>.md`, `ref/person-<firstname-lastname>.md`,
`ref/YYYY-MM-DD-<slug>.md` for decisions.

## Cross-linking

1. Log → ref: each log line links to relevant ref pages.
2. Ref → log: each ref page lists dated log entries that mentioned it.
3. Ref → ref: link related pages.
4. Update `index.md` whenever a new ref page is created.

## Log format

Append new days at the top of `log/daily.md`:

```markdown
## YYYY-MM-DD

### EOD

**Summary:** [1 sentence]

**Meetings:**
- [title] — [outcome] → [project link]

**Jira:**
- [KEY-XXX] — [what changed]

**Drive / Docs:**
- [title] — [what happened] [link]

**Slack:**
- [who] → [commitment or topic]

**Decisions:**
- [decision] | [why] | [alternatives]

**Tomorrow's focus:** [1 thing]
```

Never overwrite past entries.

---

## Commands

| Command | Delegates to |
|---------|----------------|
| `/startup` `/morning` `/midday` | `morning-brief` |
| `/brief` | `meeting-prep` |
| `/eod` | `eod-log` |
| `/ingest` | Inline below |

**MCP gate:** read and execute `~/.cursor/skills/mcp-healthcheck/SKILL.md` first.

**For /startup, /morning, /midday:** `~/.cursor/skills/morning-brief/SKILL.md`

**For /brief:** `~/.cursor/skills/meeting-prep/SKILL.md`

**For /eod:** `~/.cursor/skills/eod-log/SKILL.md`

---

## /ingest

**Usage:** `/ingest <url>` · Slack channel/thread · pasted text.

Fully autonomous — confirm after writing.

| Input | Method |
|---|---|
| Google Doc URL | `get_doc_as_markdown` |
| Drive file URL | metadata then read |
| Slack channel | `search_messages` |
| Web URL | fetch text |
| Pasted text | use as-is |

Classify as project, person, or decision. Create or update `ref/` pages.
Cross-link. Update `index.md`. Never write to Drive.

---

## Guardrails

- **Gather → checklist → write** (except `/ingest`, which confirms after)
- **Drive is read-only**
- **New wiki only** — first run creates `<wiki-root>`; do not import another wiki
- **Append, never overwrite** log entries
- **MCP gate** for `/morning`, `/midday`, `/eod`, `/brief`
- **10-minute ceiling** — if a ritual exceeds 10 min, stop and flag it
