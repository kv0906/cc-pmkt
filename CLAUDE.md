# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

PM-Kit is a markdown-only Claude Code plugin — an AI-augmented knowledge vault for Technical PMs and Engineering Leads. No build system, no runtime, no dependencies. The entire framework is `.md` files, shell scripts, and YAML config. Claude Code reads `CLAUDE.md` + `.claude/` to become a PM workspace operator.

Run `/onboard` to get started as a user.

**Always scan `_core/` first:** `config.yaml` (projects, paths, note types), `PROCESSING.md` (I-P-O flows), `MANIFESTO.md` (philosophy).

## Architecture

```
User types /command
  → Claude reads .claude/skills/{command}/SKILL.md
  → SKILL.md contains I-P-O instructions + tool permissions
  → Claude executes: reads config.yaml, uses templates, writes notes
  → PostToolUse hook auto-commits vault changes
```

**Two-layer design**: Skills (user-invocable `/commands` with SKILL.md) delegate heavy work to Agents (`.claude/agents/*.md` — specialized subprocesses for scribe, analyst, maintainer, processor roles).

**Naming-as-API**: Strict filename patterns (`daily/YYYY-MM-DD.md`, `blockers/{project}/YYYY-MM-DD-{slug}.md`) enable glob queries without a database. All patterns defined in `_core/config.yaml` under `note_types`.

**Hooks** (`.claude/hooks/`):
- `session-init.sh` — sets `$TODAY`, `$ACTIVE_PROJECTS`, `$DAILY_NOTE` on SessionStart
- `auto-commit.sh` — auto-commits after Write/Edit on vault files (controlled by `GIT_AUTO_COMMIT` env var in `.claude/settings.json`)

**Permissions** (`.claude/settings.json`): Write/Edit allowed only in user content dirs (daily, docs, decisions, blockers, meetings, inbox, index, reports, _archive). Framework files require explicit approval.

## Structure

| Folder | Purpose |
|--------|---------|
| `_core/` | config.yaml, identity.md, MANIFESTO.md, PROCESSING.md |
| `_templates/` | Note templates (ALWAYS USE when creating notes) |
| `.claude/skills/` | Skill definitions (SKILL.md per command) |
| `.claude/agents/` | Agent definitions (scribe, analyst, maintainer, processor) |
| `.claude/rules/` | Naming conventions, markdown standards, workflow rules |
| `.claude/hooks/` | Session init + auto-commit hooks |
| `scripts/` | setup.sh, update.sh, release.sh (maintainer-only) |
| `handbook/` | Framework documentation (maintainer-owned) |
| `.github/workflows/` | PR labeler, changelog-on-merge, release-on-tag |
| User content dirs | `00-inbox/`, `01-index/`, `daily/`, `docs/`, `decisions/`, `blockers/`, `meetings/`, `reports/`, `_archive/` |

## Development Commands

```bash
# Setup (first install or in-place refresh)
./scripts/setup.sh

# Update (user pulls latest framework release)
./scripts/update.sh           # Full update
./scripts/update.sh --check   # Check version only
./scripts/update.sh --dry-run # Preview changes without applying

# Validation (no test suite — markdown-only framework)
# Use /health to check vault integrity (broken links, orphans, missing sections)
# Use grep to verify cross-references after changes:
grep -r "xmarket\|MindLoom\|project-a" . --include="*.md" -l
```

Maintainer-only release and governance flow is documented in `handbook/maintainer-runbook.md`.

## Update System (Framework vs User Content)

The update script (`scripts/update.sh`) overwrites framework files but never touches user content:

| Category | Action |
|----------|--------|
| **Framework** (CLAUDE.md, _templates/*, .claude/**, scripts/*, handbook/*) | Overwrite (backed up to `_archive/_updates/`) |
| **Hybrid** (_core/config.yaml, .gitignore) | Diff shown, user merges manually |
| **User content** (daily/, docs/, decisions/, blockers/, meetings/, 00-inbox/, 01-index/) | Never touched |

## Skills

Skills are invoked with `/skill-name` or automatically by Claude when relevant.

| Skill | Invocation | Purpose |
|-------|------------|---------|
| `today` | `/today` | Guided daily workflow (standup/sync/review/focus/wrap-up) |
| `daily` | `/daily` | Multi-project standup with keyword detection |
| `progress` | `/progress` | Cross-project status synthesis |
| `preview` | `/preview` | Render vault notes as styled HTML |
| `report` | `/report` | Executive brief — business/user impact, plain language |
| `block` | `/block` | Create blocker with severity/owner/due |
| `decide` | `/decide` | Log decision with alternatives |
| `doc` | `/doc` | Draft PRD/spec/doc |
| `meet` | `/meet` | Process meeting notes |
| `inbox` | `/inbox` | Quick capture + batch processing |
| `ask` | `/ask` | Fast vault Q&A |
| `health` | `/health` | Vault health check |
| `weekly` | `/weekly` | Sprint retro (Collect/Reflect/Plan) |
| `push` | `/push` | Git commit and sync |
| `jira` | `/jira` | Jira integration via acli CLI |
| `linear` | `/linear` | Linear issue tracking integration |
| `notion` | `/notion` | Notion workspace sync, publish, pull |
| `update` | `/update` | Check for and apply framework updates |
| `onboard` | `/onboard` | Interactive setup + context loading |
| `help` | `/help` | Command reference and workflow guide |
| `vault-ops` | (auto) | Core file read/write/link operations |

### Export Flags

Skills that generate output support format export flags: `--xlsx`, `--docx`, `--pdf`, `--pptx`. Append to any supported skill invocation (e.g., `/progress all --xlsx`, `/doc project-a prd --docx`). Requires optional dependencies — run `./scripts/setup-export.sh` to install. See `.claude/rules/export-formats.md` for full mapping and layout specs.

Skills use session task tools for progress visibility during multi-step operations.

## Agents

| Agent | Purpose |
|-------|---------|
| `scribe` | Note creation — knows all templates and naming conventions |
| `analyst` | Deep synthesis, trend analysis, sprint metrics, decision audits |
| `maintainer` | Index regen, link validation, auto-archiving |
| `processor` | GTD-style routing: inbox items to proper folders |

## Conventions

- **Files**: `YYYY-MM-DD-{slug}.md` (daily: `YYYY-MM-DD.md`, docs: `{slug}.md`)
- **Frontmatter**: `type`, `project`, `status`, `date`, `tags`; Blocker adds `severity`/`owner`/`due`
- **Links**: `[[wikilinks]]` with `[[path|Title]]`; `## Links` required on typed notes
- **Linking**: Every typed note backlinks to `01-index/{project}.md`; see `.claude/rules/index-and-linking.md`
- **Naming-as-API**: Strict filename patterns enable glob queries without database

## Processing

- Detect: blockers ("blocked", "stuck") | decisions ("decided", "chose") | shipped ("deployed", "merged")
- Auto-archive on status change: resolved/shipped/superseded → `_archive/YYYY-MM/`
- Critical blockers: due < 2 days → escalate

## Output Style

**PM Coach** — Challenges scope creep, pushes for user value, holds accountable to delivery.

## Customization

Edit `_core/identity.md` to set your role, working style, and coaching preferences. `/onboard` populates it automatically.

---

*See `.claude/rules/` for detailed conventions*
