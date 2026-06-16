# Sensitive Pattern Reference

Scan skill files for these patterns before publishing. For each match, propose
the corresponding placeholder.

## Pattern categories

### Identifiers and IDs

| Pattern | Example | Regex hint | Placeholder |
|---------|---------|------------|-------------|
| Email address | `you@company.com` | `[\w.+\-]+@[\w\-]+\.\w+` | `YOUR_EMAIL@company.com` |
| UUID / cloud tenant ID | `2b9e35e3-6bd3-4cec-b838-f4249ee02432` | `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}` | `YOUR_CLOUD_ID` |
| Google Drive folder ID | appears after `/folders/` or in Drive search queries | alphanumeric 28–44 chars | `YOUR_DRIVE_FOLDER_ID` |
| Google Doc / Sheet / Slides ID | appears after `/d/` in docs/sheets/presentations URLs | alphanumeric 44 chars | `YOUR_DRIVE_FILE_ID` |
| Atlassian org ID | integer in Atlassian API calls | `"id":\s*\d{7,}` | `YOUR_ATLASSIAN_ORG_ID` |
| Slack workspace ID | `T` + 8–11 uppercase alphanumeric | `T[A-Z0-9]{8,11}` | `YOUR_SLACK_WORKSPACE_ID` |
| Slack channel ID | `C` + 8–11 uppercase alphanumeric | `C[A-Z0-9]{8,11}` | `YOUR_SLACK_CHANNEL_ID` |

### Hostnames and URLs

| Pattern | Example | Placeholder |
|---------|---------|-------------|
| Atlassian host | `yourcompany.atlassian.net` | `YOUR_COMPANY.atlassian.net` |
| Slack host | `yourcompany.slack.com` | `YOUR_COMPANY.slack.com` |
| Internal domain | any non-public hostname | `YOUR_COMPANY_DOMAIN` |
| Hardcoded internal base URL | `https://internal.yourcompany.com` | `YOUR_INTERNAL_BASE_URL` |

### People and org names

| Pattern | When to flag | Placeholder |
|---------|--------------|-------------|
| Colleague name (contact reference) | e.g. "contact Peter Kreuser if missing" | `[Your team contact]` |
| Personal name in config | assigned user, default assignee | `[Your name]` |
| Internal team or group name | team-specific labels or codes | Ask user whether to keep or replace |

### Credentials and tokens

| Pattern | Example | Placeholder |
|---------|---------|-------------|
| API key / token | long alphanumeric in auth context | `YOUR_API_TOKEN` |
| Bearer token | `Bearer eyJ...` | `YOUR_BEARER_TOKEN` |
| GitHub PAT | `ghp_...` | `YOUR_GITHUB_TOKEN` |
| AWS key | `AKIA...` | `YOUR_AWS_ACCESS_KEY` |

### Internal project identifiers

These may or may not be sensitive depending on whether the skill is being
shared publicly or only with colleagues in the same org.

| Item | Ask the user |
|------|-------------|
| Jira project key | "Is `HPUX` an internal-only key, or fine to publish?" |
| Internal product codes | "Should `CNV`, `MTV`, etc. be kept or replaced?" |
| Internal initiative names | User decides |

## Ambiguous long strings

If you encounter an alphanumeric string of 20+ characters that does not match
a known pattern but appears in a config table or URL, flag it and ask the user:
> "Line X contains `<value>` — is this a private ID or safe to publish?"

## Files to always skip (never scan or publish)

- `config.md`
- `sources.md`
- `*.secrets.md`
- Any file the user explicitly marks as private
