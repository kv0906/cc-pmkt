# PM-Kit Help Guide

Your command reference, workflow map, and cheat sheet. Run `/help` in Claude Code to surface this guide anytime.

---

## How PM-Kit Works (30 seconds)

```
You type /command  →  Agent reads skill  →  Writes markdown notes  →  Links everything
```

- **Vault** = folder of markdown files (open in Obsidian)
- **Skills** = slash commands in `.claude/skills/`
- **Naming-as-API** = strict filenames enable search without a database
- **Filesystem = memory** — context persists across sessions

**First time?** Run `/onboard`. **Returning?** Run `/onboard` or `/today` to load context.

---

## Quick Cheat Sheet

| I want to… | Command |
|------------|---------|
| Start my day | `/today` |
| Log standup quickly | `/daily project-a: shipped X, wip Y, blocked on Z` |
| Brief me like a CEO | `/report` or `/report project-a` |
| See project status | `/progress all` |
| Preview a doc in browser | `/preview docs/project-a/checkout-flow.md` |
| Create a blocker | `/block project-a: description --severity high --due friday` |
| Log a decision | `/decide project-a: chose X over Y` |
| Draft a PRD/spec | `/doc project-a checkout-flow` |
| Process meeting notes | `/meet project-a sync sprint-review` |
| Capture a thought | `/inbox idea to review later` |
| Ask about past work | `/ask what did we decide about auth?` |
| Weekly review | `/weekly` |
| Check vault health | `/health` |
| Commit and sync | `/push` |
| Get help | `/help` or `/help preview` |

---

## Commands by Category

### Daily Operations

| Command | What it does | Creates files? |
|---------|--------------|----------------|
| `/today` | Guided day: standup → sync → review → focus → wrap-up | Yes (daily, blockers) |
| `/daily` | Atomic standup log with keyword detection | Yes (`daily/`) |
| `/progress` | Cross-project status synthesis | No (use `--save`) |
| `/report` | Executive brief — business/user impact, plain language | No (use `--save`) |
| `/weekly` | Sprint retro: Collect → Reflect → Plan | Yes (`reports/`) |
| `/push` | Git commit and sync | No |

**`/today` phases**

```
/today          # Full workflow
/today skip     # Jump to next phase
/today wrap     # End-of-day summary only
```

**`/daily` keyword detection**

| You say | Agent detects |
|---------|---------------|
| shipped, done, merged, deployed | Shipped |
| wip, working on, in progress | In Progress |
| blocked, stuck, waiting on | Blocker (prompts to create note) |
| decided, chose, going with | Decision (prompts to create note) |

---

### Artifacts (Docs, Decisions, Blockers)

| Command | Output path | Template |
|---------|-------------|----------|
| `/doc` | `docs/{project}/{slug}.md` | `_templates/doc.md` |
| `/decide` | `decisions/{project}/{date}-{slug}.md` | `_templates/decision.md` |
| `/block` | `blockers/{project}/{date}-{slug}.md` | `_templates/blocker.md` |
| `/meet` | `meetings/{date}-{type}-{slug}.md` | `_templates/meeting.md` |

**Examples**

```
/doc project-a checkout-flow
/decide project-a: chose PostgreSQL over MongoDB for billing data
/block project-a: waiting on legal review --severity high --owner legal --due friday
/meet project-a sync sprint-review
```

**Meeting types:** `sync` (standup/weekly), `milestone` (planning/retro), `external` (client/stakeholder)

Every typed note needs a `## Links` section back to `01-index/{project}.md`.

---

### Preview & Share (HTML Artifacts)

Markdown is the source of truth. HTML is how you **see and share** before sending.

| Command | What it does |
|---------|--------------|
| `/preview {path}` | Render any vault note as styled HTML, open in browser |
| `/preview progress all` | Synthesize status + render as dashboard HTML |
| `/preview decide auth-approach` | Find decision by slug + render |
| `/report --preview` | Executive brief as HTML |

**Output:** `reports/previews/{date}-{slug}.html`

**Flags**

| Flag | Effect |
|------|--------|
| `--with-links` | Inline summaries from linked notes |
| `--no-open` | Write HTML only, skip browser |
| `--shell memo\|prd\|status\|executive` | Force template |
| `--link` | Add `preview:` path to source note frontmatter |

**HTML shells** (`_templates/html/`)

| Shell | Best for |
|-------|----------|
| `shell-prd.html` | PRDs, specs |
| `shell-memo.html` | Decisions, meetings, blockers |
| `shell-status.html` | Progress reports |
| `shell-executive.html` | CEO/PO briefs |

Print-to-PDF from browser works — no extra dependencies.

---

### Export Formats (Formal Delivery)

Append flags to supported skills. Requires `./scripts/setup-export.sh` once.

| Flag | Format | Output |
|------|--------|--------|
| `--xlsx` | Excel | `reports/exports/` |
| `--docx` | Word | `reports/exports/` |
| `--pdf` | PDF | `reports/exports/` |
| `--pptx` | PowerPoint | `reports/exports/` |

**Which skills support exports**

| Skill | xlsx | docx | pdf | pptx |
|-------|------|------|-----|------|
| `/progress` | ✓ | ✓ | ✓ | ✓ |
| `/doc` | — | ✓ | ✓ | — |
| `/meet` | — | ✓ | ✓ | — |
| `/weekly` | ✓ | ✓ | ✓ | ✓ |
| `/block` | ✓ | — | — | — |
| `/decide` | — | ✓ | ✓ | — |

**Examples**

```
/progress all --xlsx
/doc project-a checkout-flow --docx --pdf
/weekly --pptx
```

---

### Capture & Retrieval

| Command | Mode | Purpose |
|---------|------|---------|
| `/inbox {thought}` | Capture | Quick save to `00-inbox/` |
| `/inbox` | Process | Batch route items to proper folders |
| `/ask {question}` | Query | Vault Q&A (QMD if installed, else grep) |

**`/ask` examples**

```
/ask what did we decide about auth?
/ask who's blocked on project-a?
/ask show me open blockers
/ask project-a: what's the checkout PRD status?
```

**Smarter search:** Install [QMD](https://github.com/tobi/qmd) — see [QMD_INTEGRATION.md](QMD_INTEGRATION.md).

---

### Maintenance & Setup

| Command | Purpose |
|---------|---------|
| `/onboard` | First-time setup + context load |
| `/onboard --reset` | Re-run personal interview |
| `/health` | Broken links, orphans, missing sections |
| `/update` | Check/apply framework updates |
| `/update --check` | Version check only |
| `/help` | This guide |
| `/help {topic}` | Topic-specific help |

---

### Integrations (Optional)

Enable in `_core/config.yaml` under `integrations:`.

| Command | Tool | Setup |
|---------|------|-------|
| `/jira` | Jira (acli CLI) | `/jira setup` |
| `/linear` | Linear (MCP) | `/linear setup` |
| `/notion` | Notion (MCP) | `/notion setup` |
| `/gws` | Google Workspace CLI | `/gws` setup flow |

---

### Obsidian Tools (Power Users)

| Skill | When to use |
|-------|-------------|
| `obsidian-markdown` | Wikilinks, callouts, embeds |
| `obsidian-cli` | Open notes, search, backlinks from terminal |
| `obsidian-bases` | Database views (`.base` files) |
| `json-canvas` | Visual canvases (`.canvas` files) |
| `excalidraw` | Excalidraw diagrams (`.excalidraw.json`) |
| `mermaid-visualizer` | Mermaid diagrams from text |

---

### Utility Skills (Auto or On-Demand)

| Skill | Purpose |
|-------|---------|
| `vault-ops` | File ops, templates, naming (auto-invoked) |
| `interview` | Structured requirements interview for specs |
| `explain` | First-principles explanations |
| `skill-creator` | Build or improve custom skills |

---

## Recommended Workflows

### 1. Morning (2 min)

```
/onboard                    # or skip if returning
/daily project-a: shipped X, wip Y, blocked on Z
/daily project-b: ...
```

Or use the guided version: `/today`

### 2. During the Day

```
/block project-a: ...       # when stuck
/decide project-a: ...      # when you choose
/doc project-a feature-x    # when speccing
/inbox quick thought        # capture without context-switching
/ask what's blocking payments?
```

### 3. After Meetings

```
/meet project-a sync standup
[paste notes]
```

Agent extracts decisions, blockers, and action items.

### 4. Before Sharing with Stakeholders

```
/preview docs/project-a/checkout-flow.md    # see it first
/report project-a --preview                  # CEO-style brief as HTML
/progress all --docx                         # formal export
```

**Artifact pipeline**

```
draft (markdown)  →  preview (HTML)  →  export (pdf/docx)  →  push (git)
```

### 5. End of Day

```
/today wrap                 # or /report for executive summary
/push
```

### 6. Friday Weekly

```
/weekly
/report all --preview       # leadership brief
/push
```

### 7. Vault Hygiene (Monthly)

```
/health
/inbox                      # process stale captures
/update --check
```

---

## `/report` vs `/progress`

| | `/progress` | `/report` |
|---|-------------|-----------|
| **Audience** | You, eng leads, operators | CEO, product owner, stakeholders |
| **Language** | Project terms, tables, severity | Plain English, business impact |
| **Focus** | What shipped, WIP, blockers | What users get, what's at risk |
| **Output** | Terminal (technical) | Terminal (executive) |
| **Example** | "1 high blocker, due Friday" | "Checkout may fail during promotions until API limits are resolved" |

```
/progress all               # operator view
/report all                 # executive view
/report all --preview       # shareable HTML brief
```

---

## Folder Map

```
pm-kit/
├── 00-inbox/               # Unprocessed captures
├── 01-index/               # Project home pages (MOCs)
│   ├── {project}.md        # Project hub — link everything here
│   ├── _vault-log.md       # Audit trail of vault operations
│   └── _graph-health.md    # Health check output
├── daily/                  # YYYY-MM-DD.md (one file per day)
├── docs/{project}/         # PRDs, specs
├── decisions/{project}/    # Decision records
├── blockers/{project}/     # Active blockers
├── meetings/               # Meeting notes
├── reports/
│   ├── previews/           # HTML previews (/preview, /report --preview)
│   └── exports/            # xlsx, docx, pdf, pptx
├── _archive/               # Auto-archived resolved/shipped items
├── _core/                  # config.yaml, identity.md, PROCESSING.md
└── _templates/             # Note + HTML templates
```

**Browse in Obsidian:** Open `01-index/{project}.md` → toggle Local Graph to see the project's knowledge neighborhood.

---

## Flags Reference

### Timeframe

| Flag | Used by | Effect |
|------|---------|--------|
| `--week` | `/progress`, `/report` | Last 7 days (default) |
| `--since {date}` | `/progress`, `/report` | Custom start date |

### Output

| Flag | Used by | Effect |
|------|---------|--------|
| `--save` | `/progress`, `/report` | Write to `reports/` |
| `--preview` | `/report` | Also render HTML |
| `--risks-only` | `/report` | Blockers + decisions only |
| `--no-open` | `/preview` | Skip browser open |

### Blocker options

```
--severity high|medium|low
--owner {name}
--due {date|friday|next-week}
```

---

## File Naming (Naming-as-API)

| Type | Pattern | Example |
|------|---------|---------|
| Daily | `daily/YYYY-MM-DD.md` | `daily/2026-06-29.md` |
| Doc | `docs/{project}/{slug}.md` | `docs/project-a/checkout-flow.md` |
| Decision | `decisions/{project}/YYYY-MM-DD-{slug}.md` | `decisions/project-a/2026-06-29-auth.md` |
| Blocker | `blockers/{project}/YYYY-MM-DD-{slug}.md` | `blockers/project-a/2026-06-29-api-limit.md` |
| Meeting | `meetings/YYYY-MM-DD-{type}-{slug}.md` | `meetings/2026-06-29-sync-standup.md` |
| Inbox | `00-inbox/YYYY-MM-DD-{slug}.md` | `00-inbox/2026-06-29-idea.md` |

---

## Configuration

Edit `_core/config.yaml` to:

- Add/remove projects
- Change folder paths
- Customize keywords for detection
- Enable integrations (Jira, Linear, Notion)
- Configure QMD for `/ask`

Personal preferences: `_core/identity.md` (populated by `/onboard`)

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Skill not found | `ls .claude/skills/*/SKILL.md` — re-run `./scripts/setup.sh` |
| Project not recognized | Add project to `config.yaml` with `active: true` |
| Auto-commit not working | Check `GIT_AUTO_COMMIT` in `.claude/settings.json` |
| `/ask` inaccurate | Install QMD — see [QMD_INTEGRATION.md](QMD_INTEGRATION.md) |
| Export fails | Run `./scripts/setup-export.sh` |
| Preview won't open | Use `--no-open`, open file manually from `reports/previews/` |

Full guide: [TROUBLESHOOTING.md](TROUBLESHOOTING.md)

---

## More Documentation

| Doc | Covers |
|-----|--------|
| [START_HERE.md](../START_HERE.md) | 12-step beginner onboarding |
| [WORKFLOW_EXAMPLES.md](WORKFLOW_EXAMPLES.md) | Detailed workflow walkthroughs |
| [ARCHITECTURE.md](ARCHITECTURE.md) | How PM-Kit works under the hood |
| [CUSTOMIZATION.md](CUSTOMIZATION.md) | Projects, templates, overrides |
| [SETUP_GUIDE.md](SETUP_GUIDE.md) | Installation |
| [QMD_INTEGRATION.md](QMD_INTEGRATION.md) | Semantic search for `/ask` |

---

## Command Count

**34 skills** — 18 user-invocable commands + integrations + export/preview + Obsidian utilities.

Lost? Start here:

```
/help
/help daily
/help preview
/help report
/today
```