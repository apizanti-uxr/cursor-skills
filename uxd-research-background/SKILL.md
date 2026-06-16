---
name: uxd-research-background
description: >-
  Builds structured learning backgrounds for UXD research topics, products, and KPIs
  using Archie research reports, personal Google Drive documents and meeting notes,
  and Atlassian Rovo. Delivers past / near-past / present / future timeline, stakeholder
  map, cited resources, and wide-to-narrow learning chunks. Use when the user asks for
  background, context, research planning, product overview, KPIs, meeting notes,
  "what do I need to know about...", or onboarding to a UXD topic.
---

# UXD Research Background

Build structured learning backgrounds for UXD topics, products, and KPIs by orchestrating Google Drive (via **Google Workspace MCP**), Atlassian Rovo, and optionally Dataverse.

Unlike the **Archie skill** (retrieval-only, no synthesis), this skill **synthesizes across sources** and **teaches in developmental chunks** (wide → narrow).

## Terminology

| Term | What it actually is |
|------|---------------------|
| **Google Workspace MCP** | The MCP server (`user-google_workspace`) that reads your Google Drive, Docs, and Slides |
| **Archie** | A separate Cursor **skill** (not an MCP) for verbatim UX research retrieval — it also uses Google Workspace MCP |
| **Team UXR reports folder** | A shared Google Drive folder where formal UX research readouts live — the folder Archie searches by default |
| **Your Drive** | Your personal/work Google Drive files — meeting notes, docs, anything you name or link |

All Drive access in this skill goes through **Google Workspace MCP** tools (`search_drive_files`, `get_drive_file_content`, etc.).

## When to Use

Apply when the user:

- Asks for **background**, **context**, or **research planning** on a UXD topic
- Wants to **learn about a product** and its **KPIs**
- Says "what do I need to know about...", "onboard me to...", or "give me the landscape on..."
- Needs a **stakeholder map** and **timeline** before starting UX work
- Names **specific Drive files** to include (file name, link, or ID)
- Points to **meeting notes** or working documents in their Drive

Do **not** use for verbatim research quote retrieval — suggest the **Archie skill** instead.

## Modes

Detect mode from user intent:

| Mode | Trigger signals | Emphasis |
|------|-----------------|----------|
| **Research topic background** | "background on [topic] research", "what research exists" | UX reports, study timeline, research authors |
| **Product + KPI learning** | "learn about [product]", "KPIs", "success metrics" | Product context, HPUX work, metrics, stakeholders |

If ambiguous, ask once in a single message (max 3 questions): topic/product name, time scope, whether KPIs matter.

## Configuration

> **Setup:** Copy [sources.example.md](sources.example.md) to `sources.md` and fill in your values before using this skill.

Read [sources.md](sources.md) for stable IDs, MCP server names, folder IDs, and JQL patterns.

| Setting | Value |
|---------|-------|
| Google Workspace MCP | `user-google_workspace` |
| Google email | see `sources.md` |
| Team UXR reports folder ID | see `sources.md` |
| Atlassian cloudId | see `sources.md` |
| Jira project key | see `sources.md` |

## Workflow

Copy this checklist and track progress:

```
Research Background Progress:
- [ ] Step 1: Clarify scope (if needed)
- [ ] Step 2: Parallel source gathering
- [ ] Step 3: Synthesize temporal timeline
- [ ] Step 4: Build stakeholder map
- [ ] Step 5: Deliver learning layers (wide → narrow)
- [ ] Step 6: Format output with links and limitations
```

### Step 1: Clarify scope

Skip if the user gave a clear topic. Otherwise ask (single message):

1. Topic or product name?
2. Any time scope? (e.g. last 12 months, all history)
3. Do KPIs / success metrics matter for this request?
4. Any **specific Drive files** to include? (paste a link, file name, or file ID)

### Step 2: Parallel source gathering

Run sources in parallel where possible.

#### Priority: User-specified Drive files

When the user names specific files — by **Google Drive link**, **file name**, or **file ID** — fetch those **first**, before broad searches.

**If the user pasted a link**, extract the file ID:

| URL pattern | Example ID location |
|-------------|---------------------|
| `docs.google.com/document/d/<ID>/` | Document |
| `docs.google.com/presentation/d/<ID>/` | Slides |
| `docs.google.com/spreadsheets/d/<ID>/` | Sheet |
| `drive.google.com/file/d/<ID>/` | Any file |
| `drive.google.com/open?id=<ID>` | Any file |

Then call **`get_drive_file_content`** directly:

```
user_google_email: YOUR_EMAIL@company.com
file_id: <extracted or provided ID>
```

**If the user gave a file name only**, resolve it with **`search_drive_files`**:

```
query: name contains '<exact or partial file name>' and trashed = false
user_google_email: YOUR_EMAIL@company.com
```

Fetch content for every user-specified file. For Google Docs, call **`inspect_doc_structure`** and read all tabs.

**In output:** list user-specified files under **Resources → Files you referenced** and weight them heavily in the synthesis.

#### A. Team UX research reports (Google Drive)

MCP: `user-google_workspace`

Search the **team UXR reports folder** — the same shared Drive folder the Archie skill uses for formal research readouts. This is a folder on Drive, not an MCP.

1. **`search_drive_files`** scoped to the team folder:

```
query: 'YOUR_UXR_REPORTS_FOLDER_ID' in parents and (fullText contains '<term1>' OR fullText contains '<term2>')
user_google_email: YOUR_EMAIL@company.com
page_size: 20-25
```

2. Retrieve content for the **2–4 most relevant** hits (skip files already fetched in the user-specified step):
   - **Google Docs** → use **`get_doc_as_markdown`** (preserves headings, tables, links as Markdown)
   - **Slides, PDFs, Sheets** → use **`get_drive_file_content`**
3. For multi-tab Google Docs, call **`inspect_doc_structure`** first and fetch each tab.
4. Search the **UXD Research Engagements spreadsheet** (`YOUR_ENGAGEMENTS_SPREADSHEET_ID`) using `get_drive_file_content` — it exports as CSV. Scan for study metadata: dates, owners, engagement names, report links in column F.

Build citation links: `https://drive.google.com/file/d/<file_id>/view`

When citing research, include when available: participant count, methodology, date, report authors (often on first slide).

#### B. Your Drive — meeting notes and other documents

Search your full Google Drive for relevant meeting notes and working docs — anywhere in Drive, not limited to the team UXR folder.

1. **`search_drive_files`** across personal Drive (no folder scope):

```
query: (fullText contains '<term1>' OR fullText contains '<term2>') and trashed = false
user_google_email: YOUR_EMAIL@company.com
page_size: 15
order_by: modifiedTime desc
```

2. Prioritize files whose **name or content** suggests meeting notes:

```
(name contains 'notes' OR name contains 'meeting' OR name contains 'sync' OR name contains 'standup'
 OR fullText contains 'meeting notes' OR fullText contains 'action items')
 AND (fullText contains '<topic>' OR name contains '<topic>')
```

3. Optionally use **`search_docs`** for Google Docs by title:

```
query: <topic> notes
user_google_email: YOUR_EMAIL@company.com
```

4. Retrieve content for the **2–3 most relevant** additional hits (skip files already fetched):
   - **Google Docs** → `get_doc_as_markdown`
   - **Slides, PDFs, Sheets** → `get_drive_file_content`
5. For multi-tab Google Docs, call **`inspect_doc_structure`** first and fetch each tab.

**Label source type in output:** meeting notes and personal docs are **working context**, not formal UX research — distinguish them from team UXR reports in the Timeline and Resources sections.

If the user names a specific Drive folder, add `'folder_id' in parents` to the query. See [sources.md](sources.md).

#### C. Product / process context (Atlassian Rovo + Confluence)

MCP: `plugin-atlassian-atlassian`

1. **`search`** (Rovo — cross-system discovery, no cloudId needed) with multiple queries:

```
"<topic> roadmap"
"<product> KPI"
"<product> HPUX"
"<topic> stakeholder"
```

2. For targeted Confluence searches, use **`searchConfluenceUsingCql`** (more precise than Rovo for space/page scoping):

```
cql: text ~ "<topic>" AND type = page AND space.title ~ "HPUX"
cql: title ~ "<topic>" AND type = page AND lastmodified >= now("-6M")
cloudId: <from sources.md>
```

3. Fetch top 3–5 hits with **`getConfluencePage`** (`contentFormat: "markdown"`) or **`getJiraIssue`** (`fields: "*all"`).

#### D. Stakeholder / people map (Atlassian)

From Rovo and Jira results, extract people and group by role:

| Role | Examples |
|------|----------|
| Product | PM, product owner, BU lead |
| Engineering | Dev lead, architect |
| UX/Research | HPUX assignee, report author |
| Leadership | Director, initiative owner |
| Other | Content design, support |

**Jira JQL:**

```
project = HPUX AND text ~ '<topic>' ORDER BY updated DESC
project = HPUX AND component = '<CNV|OCP|...>' ORDER BY updated DESC
```

Resolve names with **`lookupJiraAccountId`** when needed. Only include people found in sources — mark uncertain roles as "(likely)".

#### E. Quantitative metrics and KPIs

Trigger when: user asks about KPIs, success metrics, usage data, adoption rates, or any quantitative product question.

1. Rovo first: `"<product> KPI"`, `"success metrics"`, `"tracking success"`, HPUX epic "Tracking success" fields.
2. For measured data, run the Dataverse pipeline (MCP: `user-dataverse`):
   - `identify_dataproducts` — pass the user's product/topic query
   - `shortlist_tables` — pass identified data product name + user query
   - `get_sql` — generates the query
   - `execute_sql` — runs it
3. Label each metric as **measured** (Dataverse) or **aspirational** (Jira/Confluence planning docs).
4. If Dataverse returns no relevant data product, note it and rely on Rovo/Confluence only.

### Step 3: Synthesize temporal timeline

Organize findings into four sections. Use "No documented direction found" for empty buckets.

| Section | Timeframe | Content |
|---------|-----------|---------|
| **Past** | >12 months or historical | Foundational decisions, early research, deprecated approaches |
| **Near past** | ~6–12 months | Recent studies, shipped work, course corrections |
| **Present** | Current | Active epics, latest readouts, current state |
| **Future directions** | Planned | Roadmap, open epics — mark planned vs. confirmed |

Every bullet must include a **clickable link** (Drive, Confluence, or Jira).

### Step 4: Learning delivery (wide → narrow)

Apply [learning-framework.md](learning-framework.md):

- **Layer 1 — Start here:** 5–7 essential bullets (inverted pyramid: what, why, what to do)
- **Layer 2 — Context:** Supporting terminology and connections (shorter)
- **Layer 3 — Next learning chunks:** 2–4 narrow topics queued for follow-up, each with "why next" + link

Do not dump Layer 3 content — tease it for the user to request ("teach me chunk 2").

See [examples.md](examples.md) for abbreviated sample outputs.

### Step 5: Required output template

Every response follows this structure:

```markdown
# [Topic] — Learning Background

## At a glance
[2–3 sentences: what this is and why it matters to UXD]

## Timeline
### Past
### Near past
### Present
### Future directions

## Stakeholders
| Role | Person | Relevance | Source |
|------|--------|-----------|--------|

## Layer 1 — Start here (essentials)
[5–7 bullets]

## Layer 2 — Context
[Supporting detail]

## Next learning chunks
1. [Narrow topic] — why next — [link]
2. ...

## Resources
[Grouped: Files you referenced | UX research reports | Meeting notes & your docs | Confluence | Jira | Spreadsheets | KPIs]

## Sources consulted
[Brief tracing: searches run, artifacts fetched, what was skipped and why]

## Limitations
[AI synthesis disclaimer; gaps; who to contact; suggest Archie for verbatim quotes]
```

## Guardrails

- **Never fabricate** KPIs, stakeholders, or research findings — mark gaps explicitly
- **Distinguish source types** — team UX research reports vs. your meeting notes/docs vs. planning artifacts (Jira/Confluence)
- **User-specified files first** — when the user names files, fetch and prioritize them; do not skip them in favor of search results
- **Every claim linked** — no citation without a clickable URL
- **Label synthesis** — "Based on [N] reports and [M] Jira issues…"
- **Thin research** — say so; name report authors as contacts
- **Archie skill for drill-down** — offer the Archie skill when user needs verbatim quotes: "Use the Archie skill for verbatim quotes on [topic]"
- **Cognitive load** — keep Layers 1 + 2 under ~800 words; push overflow to Next learning chunks

## Follow-up chunks

When the user asks "teach me chunk N" or "go deeper on [topic]":

1. Deliver that chunk as a focused mini-background (Layers 1–2 for the subtopic only)
2. Update the remaining chunk queue
3. Maintain citation and limitation standards

## Additional Resources

- [sources.md](sources.md) — folder IDs, MCP config, JQL patterns
- [learning-framework.md](learning-framework.md) — pedagogy rules
- [examples.md](examples.md) — sample output shapes
