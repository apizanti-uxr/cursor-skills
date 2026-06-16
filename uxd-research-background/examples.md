# UXD Research Background — Examples

Two abbreviated examples showing expected output shape. Real responses should be longer and fully cited.

---

## Example 1: Research topic background

**User:** "Give me background on ACS onboarding research"

```markdown
# ACS Onboarding — Learning Background

## At a glance
ACS (Advanced Cluster Security) onboarding is a recurring UXD focus area. Past research
identified friction in cluster setup and policy configuration. Active HPUX work continues
to address first-run experience gaps.

## Timeline

### Past
- 2023 foundational usability study found cluster connection steps confused new admins.
  [ACS Onboarding Usability Study 2023](https://drive.google.com/file/d/EXAMPLE/view)

### Near past
- Q2 2024 follow-up interviews validated improvements to the welcome flow but flagged
  policy wizard complexity. [ACS Policy Wizard Interviews](https://drive.google.com/file/d/EXAMPLE/view)

### Present
- HPUX-1393 epic tracks onboarding redesign; discovery story in progress.
  [HPUX-1393](https://YOUR_COMPANY.atlassian.net/browse/HPUX-1393)

### Future directions
- Planned: simplified policy templates (stated in epic goals, not yet shipped).

## Stakeholders

| Role | Person | Relevance | Source |
|------|--------|-----------|--------|
| UX/Research | Jane Doe | HPUX epic assignee | HPUX-1393 |
| UX/Research | John Smith | 2024 study author | Report slide 1 |

## Layer 1 — Start here (essentials)

- ACS onboarding covers cluster registration, policy setup, and first dashboard view.
- Primary user pain: policy wizard steps are opaque to first-time admins.
- Two research cycles exist (2023 usability, 2024 interviews) — evidence is moderate, not exhaustive.
- Active design work lives under HPUX-1393 (CNV component).
- Before designing, read the 2024 interview readout for current pain points.

## Layer 2 — Context

ACS sits in the Hybrid Cloud Console portfolio. Onboarding research typically pairs
with CNV/ACM stakeholder reviews. The engagements spreadsheet lists three ACS studies
since 2023.

## Next learning chunks

1. **Policy wizard interaction patterns** — core remaining friction — [2024 interview deck](link)
2. **CNV onboarding patterns (cross-product)** — shared console patterns — [Confluence onboarding guide](link)
3. **HPUX-1393 epic scope and DoD** — what design must deliver — [HPUX-1393](link)

## Resources

**Research:** [2023 study](link) · [2024 interviews](link)
**Meeting notes:** [ACS onboarding sync 2025-03](link)
**Jira:** [HPUX-1393](link)
**Spreadsheet:** [UXD Research Engagements](https://docs.google.com/spreadsheets/d/YOUR_ENGAGEMENTS_SPREADSHEET_ID/edit)

## Sources consulted

Searched Archie folder for "ACS onboarding" (4 hits, fetched 3). Personal Drive for meeting notes
matching "ACS onboarding" (2 hits, fetched 1). Rovo: "ACS HPUX", "ACS onboarding research".
Jira JQL: `project = HPUX AND text ~ 'ACS onboarding'`.

## Limitations

This is an AI synthesis across [3] research artifacts and [2] Jira issues. Verify findings
against source links. For verbatim user quotes, use Archie. Gaps: no quantitative usage data consulted.
```

---

## Example 2: Product + KPI learning

**User:** "Learn about Hybrid Cloud Console and its KPIs"

```markdown
# Hybrid Cloud Console — Learning Background

## At a glance
Hybrid Cloud Console (HCC) is Red Hat's unified web UI for managing cloud and
on-prem workloads. UXD tracks adoption, task completion, and satisfaction metrics
across multiple product areas (OCP, ROSA, ACM, etc.).

## Timeline

### Past
- Console launched as consolidation of siloed product UIs (historical Confluence architecture page).

### Near past
- 2025 navigation redesign research informed information architecture changes.

### Present
- Multiple active HPUX epics across OCP and ACM components.

### Future directions
- Continued unification of navigation patterns (roadmap Confluence page, planned).

## Stakeholders

| Role | Person | Relevance | Source |
|------|--------|-----------|--------|
| Product | (from Confluence page author) | HCC product overview | Confluence |
| UX/Research | (HPUX epic assignees) | Active design work | HPUX issues |

## Layer 1 — Start here (essentials)

- HCC is the shell UI; individual products (OCP, ROSA, ACM…) are "apps" within it.
- UXD success is tracked via task completion rates and CSAT on key workflows (per epic "Tracking success" fields).
- HPUX uses component labels (OCP, ACM, etc.) to scope design work within HCC.
- Check active HPUX epics before starting any HCC-related design.
- KPI definitions vary by product area — there is no single HCC-wide KPI doc.

## Layer 2 — Context

HCC spans multiple business units. Research and design work are often product-specific
even when the shell UI is shared. The engagements spreadsheet lists studies tagged
"Hybrid Cloud Console" or product-specific variants.

## Next learning chunks

1. **HCC information architecture** — understand navigation model — [Confluence IA page](link)
2. **OCP-specific KPIs within HCC** — most active UXD area — [HPUX epics](link)
3. **Recent HCC navigation research** — latest user evidence — [Research readout](link)

## Resources

**Confluence:** [HCC overview](link) · [Roadmap](link)
**Jira:** [HPUX OCP epics](link)
**KPIs:** Sourced from epic "Tracking success" fields; Dataverse not queried in this example.

## Sources consulted

Rovo: "Hybrid Cloud Console KPI", "Hybrid Cloud Console HPUX", "Hybrid Cloud Console roadmap".
Jira JQL: `project = HPUX AND text ~ 'Hybrid Cloud Console'`.

## Limitations

KPIs listed are from planning docs, not live analytics. For measured metrics, query Dataverse
or ask the product analytics team. This synthesis may miss recent unpublished research.
```
