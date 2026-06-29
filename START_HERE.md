# Start Here

You need 3 things installed: **Git** (or GitHub Desktop), **[Claude Code](https://claude.ai/code)**, and **[Obsidian](https://obsidian.md)**.

## Step 1: Get your copy

### Option A: CLI Install (Recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/kv0906/pm-kit/main/cli/install.sh | bash
pm-kit init ~/Documents/my-vault
cd ~/Documents/my-vault
```

### Option B: GitHub Template

On GitHub, click **"Use this template"** and create a new repo (private recommended).

Clone it locally:

```bash
git clone <your-repo-url>
cd pm-kit
```

## Step 2: Open in Obsidian

Open Obsidian, choose **"Open folder as vault"**, and select the cloned folder.

## Step 3: First run

Open a terminal in the folder and run:

```
claude
/onboard
```

`/onboard` walks you through everything — asks about you, sets up your first project, and loads context.

## Step 4: Daily routine (2 min)

```
/daily my-project: shipped login page, wip checkout, blocked on API keys
/push
```

## Step 5: When things happen

```
/block my-project: API rate limit, owner: backend-team, severity: high
/decide my-project: chose PostgreSQL over MongoDB for user data
```

## Step 6: Weekly review (Fridays)

```
/weekly
```

## Step 7: Browse in Obsidian

- `01-index/my-project.md` — project home page
- `daily/` — all standups
- `blockers/my-project/` — active blockers
- `decisions/my-project/` — decision log

## Step 8: Sync with GitHub

```
/push
```

Or use GitHub Desktop to commit and push.

## Step 9: Enable smarter /ask (optional but recommended)

This requires **bun** and **QMD** — both are CLI tools that need technical comfort to install.

```bash
curl -fsSL https://bun.sh/install | bash
bun install -g github:tobi/qmd
qmd collection add . --name pm-kit && qmd embed
```

After this, `/ask` uses semantic search instead of basic grep. If you skip this, `/ask` still works — just less accurate.

Having trouble? See [handbook/QMD_INTEGRATION.md](handbook/QMD_INTEGRATION.md) or [open an issue](https://github.com/kv0906/pm-kit/issues).

## Step 10: Customize (when ready)

- `_core/config.yaml` — add more projects, change folder paths
- `/onboard --reset` — redo personal preferences
- `_templates/` — modify note templates

## Step 11: Check for updates

```
/update --check
```

PM-Kit is actively developed. Check for framework updates periodically — your notes and config are never touched.

## Step 12: Get help

```
/help
/help workflow
/ask what did we decide about auth?
/health
```

Full command reference: [handbook/HELP_GUIDE.md](handbook/HELP_GUIDE.md) · Overview: [README.md](README.md)
