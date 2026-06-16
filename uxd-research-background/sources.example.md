# UXD Research Background — Source Configuration

Copy this file to `sources.md` and fill in your values before using the skill.
`sources.md` is listed in `.gitignore` and will never be committed.

---

## Google Workspace MCP

| Setting | Your value |
|---------|------------|
| MCP server | `user-google_workspace` |
| user_google_email | `YOUR_EMAIL@company.com` |

### Team UXR reports folder

| Setting | Your value |
|---------|------------|
| Folder ID | `YOUR_UXR_REPORTS_FOLDER_ID` |
| URL | `https://drive.google.com/drive/folders/YOUR_UXR_REPORTS_FOLDER_ID` |

This is the shared Google Drive folder where your team's formal UX research readouts live.
To find the folder ID: open the folder in Drive and copy the ID from the URL after `/folders/`.

**Search query pattern:**
```
'YOUR_UXR_REPORTS_FOLDER_ID' in parents and (fullText contains '<term>')
```

### UXD Research Engagements Spreadsheet

| Setting | Your value |
|---------|------------|
| Spreadsheet ID | `YOUR_ENGAGEMENTS_SPREADSHEET_ID` |
| URL | `https://docs.google.com/spreadsheets/d/YOUR_ENGAGEMENTS_SPREADSHEET_ID/edit` |
| Report links column | Column F, starting row 7 (adjust if your sheet differs) |

---

## Atlassian MCP

| Setting | Your value |
|---------|------------|
| MCP server | `plugin-atlassian-atlassian` |
| cloudId | `YOUR_ATLASSIAN_CLOUD_ID` |
| Site | `YOUR_COMPANY.atlassian.net` |
| Jira project key | `YOUR_PROJECT_KEY` |
| Jira browse URL pattern | `https://YOUR_COMPANY.atlassian.net/browse/<KEY-XXX>` |

To find your cloudId: run `getAccessibleAtlassianResources` via the Atlassian MCP
and copy the `id` field for your site.

### HPUX JQL patterns (adapt project key as needed)

```
project = YOUR_PROJECT_KEY AND text ~ '<topic>' ORDER BY updated DESC
project = YOUR_PROJECT_KEY AND component = '<component>' ORDER BY updated DESC
project = YOUR_PROJECT_KEY AND type = Epic AND text ~ '<topic>'
```

---

## Dataverse MCP

| Setting | Your value |
|---------|------------|
| MCP server | `user-dataverse` |

No additional config needed — tool credentials are handled by the MCP server.

---

## Archie skill (optional)

If your team uses a dedicated retrieval skill for verbatim UX research quotes,
note its name here and offer it to users when they need raw quotes.

| Setting | Your value |
|---------|------------|
| Skill name | `YOUR_VERBATIM_RETRIEVAL_SKILL` |
