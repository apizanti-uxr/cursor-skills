---
name: publish-skill-to-github
description: >-
  Publishes one or more Cursor skills from ~/.cursor/skills/ to a shared GitHub
  repository after scrubbing sensitive data. Creates sanitized copies, splits
  inline config into placeholder example files, sets up .gitignore, and pushes
  via gh CLI. Use when the user wants to share a skill, publish skills to GitHub,
  upload a skill, or create a shareable skills repository for colleagues.
disable-model-invocation: true
---

# Publish Skill to GitHub

Safely publish skills from `~/.cursor/skills/` to a shared GitHub repository
by scrubbing sensitive data and extracting config into placeholder example files.

## Two-folder model

```
~/.cursor/skills/             ← private originals, real config values, never touched
~/Projects/cursor-skills/     ← sanitized copies, git repo, what colleagues see
```

The originals in `~/.cursor/skills/` are never modified. All sanitization happens
on copies written to `~/Projects/cursor-skills/` before pushing to GitHub.

## Prerequisites

Before starting, verify:

```bash
gh auth status   # must be authenticated
git --version    # must be installed
```

If `gh auth status` fails, stop and ask the user to run `gh auth login` first.

## Workflow

```
Progress:
- [ ] Step 1: Gather parameters
- [ ] Step 2: Read skill files
- [ ] Step 3: Detect sensitive data
- [ ] Step 4: Confirm sanitization plan
- [ ] Step 5: Prepare sanitized files
- [ ] Step 6: Set up / verify GitHub repo
- [ ] Step 7: Copy files and push
```

---

### Step 1: Gather parameters

List available skills:

```bash
ls ~/.cursor/skills/
```

Ask in a single message (skip any already provided):

1. **Which skill(s)?** One, several, or all.
2. **Target GitHub repo?** `owner/repo-name` — or say "create new" (default name: `cursor-skills`, public).
3. **Any extra values to scrub?** (optional — user can name specific strings)

The local publish folder is always `~/Projects/cursor-skills/` — this is the
sanitized copy that maps to the GitHub repo. Do not touch `~/.cursor/skills/`.

---

### Step 2: Read skill files

For each skill to publish, read every `.md` file in its directory.

**Hard skip — never read or publish:**
- `config.md`
- `sources.md`
- `*.secrets.md`
- Any file the user flags as private

If a skill references `config.md` or `sources.md` in its SKILL.md, note that — you will need to create the `*.example.md` counterpart in Step 5.

---

### Step 3: Detect sensitive data

Scan every read file against the patterns in [sensitive-patterns.md](sensitive-patterns.md).

For each hit, record: **file path**, **line number**, **original value**, **suggested placeholder**.

Also flag any inline config tables (rows with a Setting column and a hardcoded Value) as candidates for extraction into a `config.example.md`.

---

### Step 4: Confirm sanitization plan

Present a summary table before touching anything:

```
File                  | Line | Original value                       | Replacement
SKILL.md              |  27  | you@company.com                      | YOUR_EMAIL@company.com
SKILL.md              |  62  | 2b9e35e3-...-ee02432                 | YOUR_ATLASSIAN_CLOUD_ID
SKILL.md              |  63  | 1yW2GbqKThAsk...VBo1                 | YOUR_DRIVE_FOLDER_ID
SKILL.md              |  28  | yourcompany.atlassian.net            | YOUR_COMPANY.atlassian.net

Config extraction proposed:
- Inline config table in SKILL.md → config.example.md
```

Also ask: should internal project keys (e.g. HPUX, CNV) be replaced or kept?

**Do not write any files until the user confirms.**

---

### Step 5: Prepare sanitized files

Work in a temp staging directory (e.g. `/tmp/skill-publish/<skill-name>/`).

1. **Apply replacements** — substitute every confirmed value with its placeholder.

2. **Config extraction** — if inline config exists in SKILL.md:
   - Create `config.example.md` in the staging dir with this structure:

     ```markdown
     # Configuration

     Copy this file to `config.md` and fill in your values before using the skill.

     | Setting | Your value |
     |---------|------------|
     | Atlassian cloudId | YOUR_ATLASSIAN_CLOUD_ID |
     | Google email | YOUR_EMAIL@company.com |
     ```

   - Update the sanitized SKILL.md to say:
     `> **Setup:** Copy [config.example.md](config.example.md) to \`config.md\` and fill in your values.`
   - Remove the inline hardcoded table.

3. **For skills that already reference `sources.md`** — create `sources.example.md` in staging with the same pattern (placeholder values, copy-rename instructions).

---

### Step 6: Set up / verify GitHub repo

```bash
# Check if repo exists
gh repo view <owner>/<repo-name>

# If not, create it
gh repo create <owner>/<repo-name> --public \
  --description "Shared Cursor Agent Skills"
```

The local publish folder is `~/Projects/cursor-skills/`. It is a git repo
already pointed at the remote — do not re-clone or re-init it.

Before making changes, pull any remote updates:

```bash
git -C ~/Projects/cursor-skills pull --ff-only
```

If `pull --ff-only` fails due to diverged history, stop and ask the user how
to proceed — never force-push or reset without explicit instruction.

If the folder does not exist yet, set it up once:

```bash
mkdir ~/Projects/cursor-skills
git -C ~/Projects/cursor-skills init
git -C ~/Projects/cursor-skills remote add origin git@github.com:<owner>/<repo-name>.git
```

Add required entries to `.gitignore` without overwriting existing content:

```bash
cd <local-clone-path>
for pattern in "**/config.md" "**/sources.md" "**/*.secrets.md"; do
  grep -qxF "$pattern" .gitignore 2>/dev/null || echo "$pattern" >> .gitignore
done
```

Create or update `README.md` at the repo root. Add a row for the new skill to the skills table:

```markdown
# Cursor Skills

A collection of shareable [Cursor](https://cursor.com) Agent Skills.

## Using a skill

1. Copy the skill folder to `~/.cursor/skills/`
2. If a `config.example.md` is present, copy it to `config.md` and fill in your values
3. The skill is available in your Cursor agent sessions on next launch

## Skills

| Skill | Description |
|-------|-------------|
| [skill-name](./skill-name/) | One-line description from SKILL.md frontmatter |
```

---

### Step 7: Copy files and push

Check whether this skill already exists in the repo — this determines the commit verb:

```bash
[ -d ~/Projects/cursor-skills/<skill-name> ] && echo "update" || echo "add"
```

Copy the staging dir into the repo clone. Use `rsync` to safely overwrite an
existing skill folder without leaving stale files:

```bash
rsync -a --delete /tmp/skill-publish/<skill-name>/ ~/Projects/cursor-skills/<skill-name>/
```

Stage changes and verify there is actually something to commit before proceeding:

```bash
git -C ~/Projects/cursor-skills add .
git -C ~/Projects/cursor-skills status --porcelain
```

If `status --porcelain` returns empty output, tell the user the skill is already
up to date and stop — do not create an empty commit.

Otherwise commit and push:

```bash
# Use "Add" for new skills, "Update" for existing ones
git -C ~/Projects/cursor-skills commit -m "Add <skill-name> skill"
# or: commit -m "Update <skill-name> skill"

git -C ~/Projects/cursor-skills push -u origin main
```

If `push` fails, report the error verbatim — do not retry with `--force`.

Report back:
- Published URL: `https://github.com/<owner>/<repo>/tree/main/<skill-name>`
- List of values that were scrubbed
- Any private files that were skipped

---

## Guardrails

- **Never publish** `config.md`, `sources.md`, or `*.secrets.md` — skip silently
- **Never guess** on ambiguous long strings — ask the user if it's sensitive
- **Always confirm** the sanitization table before writing any files
- **Idempotent** — if the skill already exists in the repo, update (overwrite) rather than duplicate
- **One skill at a time** when multiple skills are requested — confirm each sanitization plan individually before moving to the next
