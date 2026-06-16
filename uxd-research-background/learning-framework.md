# UXD Research Background — Learning Framework

Pedagogy rules for delivering wide-to-narrow, developmental learning backgrounds.

## Core principles

### 1. Inverted pyramid

Lead with the answer. The first content the user reads must answer:

- **What is this?**
- **Why does it matter to UXD?**
- **What should I do with this knowledge?**

Details, history, and nuance come after the essentials.

### 2. Wide → narrow funnel

Structure every response as a funnel:

```
Wide landscape (At a glance + Timeline overview)
    ↓
Focused essentials (Layer 1)
    ↓
Supporting context (Layer 2)
    ↓
Optional depth on demand (Next learning chunks)
```

Do not dump all depth in one response. Reserve narrow topics for follow-up chunks.

### 3. Chunked delivery

| Layer | Deliver | Size | Purpose |
|-------|---------|------|---------|
| **Layer 1** | Now | 5–7 bullets | Minimum viable knowledge to start work |
| **Layer 2** | Now | 3–5 bullets or short paragraphs | Terminology, connections, nuance |
| **Layer 3** | On request | 2–4 queued topics | Deep dives the user can ask for next |

Each Layer 3 chunk must include:

- A specific narrow topic title
- One sentence: "why learn this next"
- Primary resource link

User follow-up phrases: "teach me chunk 2", "go deeper on [topic]", "expand chunk 1".

### 4. Temporal anchoring

Organize narrative history into four time buckets:

| Bucket | Rough timeframe | Content type |
|--------|-----------------|--------------|
| **Past** | >12 months or explicitly historical | Foundational decisions, early research, deprecated approaches |
| **Near past** | ~6–12 months | Recent studies, shipped work, course corrections |
| **Present** | Current | Active epics, latest readouts, current product state |
| **Future directions** | Planned | Roadmap, open epics, stated goals — mark as planned vs. confirmed |

Empty buckets: write "No documented direction found" rather than omitting the heading.

### 5. Source transparency

- Every factual claim needs a clickable link
- Label synthesis explicitly: "Based on [N] research reports, [P] meeting notes, and [M] Jira epics…"
- Distinguish three source types:
  - **Team UX research** — formal readouts in the shared UXR reports folder
  - **Your files** — meeting notes, docs you named or linked, informal working context
  - **Planning artifacts** — Jira epics, Confluence pages
- Never fabricate KPIs, stakeholders, or findings

### 6. Actionable next step

Always end with either:

- **Next learning chunks** (numbered list), or
- A suggested follow-up question the user can ask

Never leave the user at a dead end.

---

## Mode-specific emphasis

### Research topic background

Prioritize:

1. What UX research exists (Archie folder + engagements spreadsheet)
2. Relevant meeting notes and personal Drive documents
3. Key findings timeline
4. Research authors and meeting participants as contacts
5. Gaps in research coverage

De-emphasize KPIs unless the user asks.

### Product + KPI learning

Prioritize:

1. Product purpose and UXD scope
2. Success metrics (Rovo → Dataverse if needed)
3. Active HPUX work
4. Stakeholder map

De-emphasize deep research history unless reports exist.

---

## Cognitive load rules

- **One concept per bullet** in Layer 1
- **No jargon without definition** — if you use an acronym, expand it once
- **Max ~800 words** in Layers 1 + 2 combined; push overflow to Layer 3
- **Tables over prose** for stakeholders and resource lists
- **Bold sparingly** — only for terms being defined or critical warnings

---

## Relationship to Archie

| Archie | This skill |
|--------|------------|
| Retrieves verbatim research data | Synthesizes across sources |
| No cross-document conclusions | Connects research, Jira, and Confluence |
| Context Folder only | Multiple sources |

When the user needs verbatim quotes or raw study data, suggest: "Use Archie for verbatim quotes on [topic]."
