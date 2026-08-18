# Cursor Skills

A collection of shareable [Cursor](https://cursor.com) Agent Skills for the UXD team.

## Using a skill

1. Copy the skill folder to `~/.cursor/skills/`
2. If a `sources.example.md` or `config.example.md` is present, copy it to `sources.md` / `config.md` and fill in your values
3. The skill is available in your Cursor agent sessions on next launch

> **Note:** `sources.md` and `config.md` files are listed in `.gitignore` and are never committed — they hold your personal IDs, emails, and org-specific values.

## Skills

| Skill | Description |
|-------|-------------|
| [uxd-research-background](./uxd-research-background/) | Builds structured learning backgrounds for UXD research topics, products, and KPIs using Google Drive, Atlassian Rovo, and Dataverse. |
| [publish-skill-to-github](./publish-skill-to-github/) | Safely publishes Cursor skills to a shared GitHub repo after scrubbing sensitive data. |
| [daily-rituals](./daily-rituals/) | Router for start / mid / end of day. Creates a **new empty local wiki** on first run. |
| [morning-brief](./morning-brief/) | `/morning` and `/midday` canvas brief. |
| [eod-log](./eod-log/) | `/eod` scrape + wiki log. |
| [meeting-prep](./meeting-prep/) | `/brief` status check and meeting prep. |
| [mcp-healthcheck](./mcp-healthcheck/) | Hard MCP gate before those commands. |

### Daily rituals (install together)

Copy **all five** folders into `~/.cursor/skills/`. Copy `daily-rituals/sources.example.md` to `daily-rituals/sources.md` and fill in your values. The first `/morning` or `/eod` creates a new wiki at the path you set (default `~/daily-wiki`). It does not use or import anyone else's wiki.

## Local wiki

The daily-ritual skills keep notes in a **local wiki**: a folder on your computer that the skills create for you. It is private to your machine. It is not a GitHub wiki, and it is not published with this repo.

On first `/morning` or `/eod`, the skills create a **new empty wiki**. They never copy or import someone else's wiki.

Set the path with **Wiki root** in `daily-rituals/sources.md` (copy `sources.example.md` to `sources.md` and fill in your values). The default is `~/daily-wiki`.

What gets created:

```
<wiki-root>/
  index.md
  log/
    daily.md
  ref/
```

Google Drive is read-only. The wiki is the only place these skills write.
