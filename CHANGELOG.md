# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

<!-- New merged PR entries are inserted here automatically. -->

## [2026.06.29] - 2026-06-29

<!-- New merged PR entries are inserted here automatically. -->

### Added
- **HTML artifact pipeline** — preview vault notes as styled HTML before sharing; markdown stays source of truth
- **`/preview` skill** — render docs, decisions, progress reports as HTML and open in browser; supports file paths plus `progress`, `decide`, `block`, `meet`, `doc` synthesis modes
- **`/report` skill** — executive briefing in plain language (business and user impact, no jargon); optional `--save` and `--preview` flags
- **`/help` skill** — command reference and workflow cheat sheet; topic-specific help via `/help preview`, `/help report`, etc.
- **`handbook/HELP_GUIDE.md`** — full help guide covering all commands, workflows, flags, folder map, and troubleshooting
- **HTML shell templates** — `_templates/html/shell-memo.html`, `shell-prd.html`, `shell-status.html`, `shell-executive.html`
- **Preview rules** — `.claude/rules/preview-formats.md` defines rendering workflow, wikilink resolution, and output paths
- **`previews` path** — `reports/previews/` in `_core/config.yaml`

## [2026.04.05] - 2026-04-05

<!-- New merged PR entries are inserted here automatically. -->

### Added
- **Vault log** — append-only chronological record at `01-index/_vault-log.md` tracks all vault operations (daily, blocker, decision, meeting, doc, inbox, archive, index-regen, health). New rule at `.claude/rules/vault-log.md` defines format and conventions. All note-creating skills (`/daily`, `/meet`, `/block`, `/decide`, `/doc`, `/inbox`) now append log entries automatically
- **Synthesis sections in project indexes** — `_templates/index.md` now includes a `## Synthesis` section with Current State, Key Tensions, and Open Questions. Maintainer agent generates these by reading across all project notes during index regeneration
- **Enriched index links** — maintainer agent now generates one-line summaries next to every link in project indexes instead of bare `[[wikilinks]]`
- **Contradiction checking** — processor agent cross-checks new items against existing decisions, archived blockers, and active docs before routing. Flags conflicts and recurring issues for user review
- **Synthesis health in `/health`** — health check now reports whether each project has a synthesis section and when the last index regeneration occurred. Stale synthesis (7+ days) flagged in report

## [2026.03.11] - 2026-03-11

<!-- New merged PR entries are inserted here automatically. -->

### Added
- **External integration skills** — three new skills for bridging PM-Kit with popular task management tools:
  - **Jira skill** (`.claude/skills/jira/`): Full Jira integration via `acli` CLI with 11 sub-commands (board, create, status, move, edit, assign, backlog, search, sprint, sync, setup). Includes vault sync (blocker bridging, daily note updates), JQL cheatsheet reference, project/status mapping in config, and health check script
  - **Linear skill** (`.claude/skills/linear/`): Linear issue tracking via MCP server with sub-commands for setup, sync, create, search, and link. OAuth-based auth, vault note linking with `linear:` frontmatter, daily note integration
  - **Notion skill** (`.claude/skills/notion/`): Notion workspace integration for search, sync, publish, and pull operations. Bridges vault notes with Notion pages
- **Jira config block** in `_core/config.yaml` with `site`, `email`, `project_mapping`, `default_board`, and `status_mapping` fields
- `/jira`, `/linear`, `/notion` added to Skills table in CLAUDE.md

## [2026.03.06.3] - 2026-03-06

<!-- New merged PR entries are inserted here automatically. -->

### Fixed
- **Cleaner user vaults**: `pm-kit init` and `pm-kit update` no longer copy maintainer-only files (`handbook/`, `examples/`, `.github/`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `pm-kit-banner.png`) into user vaults

## [2026.03.06.2] - 2026-03-06

### Added
- **Google Workspace CLI skill** (`.claude/skills/gws/`): comprehensive skill for connecting PMs to Google Workspace via the `gws` CLI
  - Gmail, Calendar, Drive, Sheets, Docs, Chat, Meet, Forms integration
  - Helper shortcuts (`+send`, `+triage`, `+agenda`, `+upload`, `+read`, `+append`, `+write`)
  - MCP server integration for deeper Claude Code connectivity
  - Model Armor safety filtering for agent responses
  - Reference docs: service catalog, setup troubleshooting guide
  - Health check script (`scripts/check-gws.sh`)
- Decision record: Do not publish CLI to npm (`decisions/pm-kit/2026-03-06-no-npm-package.md`)

## [2026.03.06] - 2026-03-06

<!-- New merged PR entries are inserted here automatically. -->

### Added
- **PM-Kit CLI** (`cli/`): standalone bash CLI installed via `curl | bash`
  - `pm-kit init` — scaffold new vault from latest GitHub release
  - `pm-kit update` — update framework files while preserving user content
  - `pm-kit self-update` — update the CLI itself
  - `pm-kit doctor` — verify vault structure, dependencies, and health
  - `pm-kit status` — show CLI version, vault version, active projects
  - `pm-kit migrate` — convert template-cloned vault to CLI-managed
- Local testing override (`PM_KIT_LOCAL_TARBALL` env var) for CI and pre-release testing
- CLI docs added to README, START_HERE, SETUP_GUIDE, and maintainer-runbook

### Fixed
- `find_vault_root()` unbound variable error when called without arguments under `set -u`

### Changed
- `scripts/update.sh` now includes `AGENTS.md` in framework files and `cli/` in framework dirs

## [2026.03.05] - 2026-03-05

### Added
- Multi-agent compatibility: symlinks for Cursor (`.cursorrules`, `.cursor/rules/`), Windsurf (`.windsurfrules`), Cline (`.clinerules`), GitHub Copilot (`.github/copilot-instructions.md`), and generic agent conventions (`.agent/`, `.agents/`)
- All compatibility files symlink to `CLAUDE.md` or `.claude/` as single source of truth

### Changed
- Updated README "Works With" table to reflect native compatibility with Cursor, Windsurf, Cline, Copilot, and Codex

## [2026.02.25.2] - 2026-02-25

<!-- New merged PR entries are inserted here automatically. -->

### Added
- `.claude/rules/index-and-linking.md` — 7 enforced rules for MOC/index linking, backlinks, and Obsidian graph health
- Linking convention in `CLAUDE.md` pointing to the new rule file

### Changed
- Renamed `inbox/` → `00-inbox/` and `index/` → `01-index/` — numeric prefixes pin these folders to the top of file explorers
- Updated all path references across skills, agents, templates, config, and docs to match new folder names
- Moved user identity from `CLAUDE.local.md` (gitignored) to `_core/identity.md` (tracked) — part of the bootloader directory
- Deleted `CLAUDE.local.md.template` — `_core/identity.md` serves as both default and populated profile
- `setup.sh` includes migration logic for existing vaults with old folder names

## [2026.02.25] - 2026-02-25

### Added
- Export format flags (`--xlsx`, `--docx`, `--pdf`, `--pptx`) for PM-Kit skills — generate shareable files for stakeholders directly from vault workflows
- New skills: `/xlsx` (spreadsheet creation/editing), `/docx` (Word document creation/editing), `/pdf` (PDF processing), `/pptx` (PowerPoint creation/editing)
- `.claude/rules/export-formats.md` — export rules defining skill-to-format mapping, layout specs, and dependency handling
- `scripts/setup-export.sh` — one-command installer for optional export dependencies (openpyxl, docx-js, reportlab, pptxgenjs)
- `exports` path in `_core/config.yaml` — output to `reports/exports/`

### Changed
- Skills `/progress`, `/doc`, `/meet`, `/weekly`, `/block` now accept export format flags and include `Bash` in allowed tools
- `.claude/settings.json` adds `Bash(python3:*)`, `Bash(python:*)`, `Bash(node:*)` permissions for export generation
- `CLAUDE.md` documents export flags in Skills section

## [2026.02.15a] - 2026-02-15

### Added
- `AGENTS.md` — Cross-agent compatibility file for Codex, Cursor, Windsurf, and other AI coding tools (project structure, build commands, coding style, testing, commit conventions)

### Changed
- `CLAUDE.md` rewritten for developer clarity: added architecture overview, two-layer design (skills + agents), hooks documentation, development commands, versioning/release flow, update system (framework vs user content), and permissions model
- Fixed formatting bug where frontmatter and links conventions were merged on one line
- Skills table reordered: `/today` moved to top as primary daily workflow
- `/onboard` description updated to "Interactive setup + context loading"
- Progress visibility section condensed to single line

## [2026.02.15] - 2026-02-15

### Added
- `/today` — Guided daily workflow orchestrator with 5 phases: standup, team sync, review, focus, wrap-up
- `/today wrap` — Jump directly to end-of-day summary
- `/today skip` — Skip current phase and advance to next
- `/today status` — Show current workflow phase
- Same-session resume: run `/today` again after focus time to pick up where you left off
- New skills: `/excalidraw`, `/explain`, `/find-skills`, `/interview`, `/json-canvas`, `/mermaid-visualizer`, `/obsidian-bases`, `/obsidian-cli`, `/obsidian-markdown`
- README banner image

### Changed
- README daily routine section now features `/today` as primary workflow
- `WORKFLOW_EXAMPLES.md` leads with guided `/today` workflow, existing manual flow renamed to "Manual"
- `PROCESSING.md` `/today` entry corrected to match implementation phases

### Fixed
- `update.sh` now checks for uncommitted changes before applying updates

## [2026.02.14] - 2026-02-14

<!-- New merged PR entries are inserted here automatically. -->

### Removed
- `/adr` command and ADR workflow (architecture decisions now use `/decide`)
- OKR tracking system (`/okr-tracking` skill, `roadmap/` folder)
- ADR template, naming conventions, and folder structure
- OKR references from `/weekly`, `/progress`, `/onboard`, and output styles
- `research/` folder concept removed (use `docs/general/` for non-project docs)
- Framework docs moved from `docs/` to `handbook/` (docs/ is now user-only)

### Changed
- `/weekly` simplified to operational weekly review (Collect/Reflect/Plan without OKR alignment)
- PM Coach output style uses delivery/accountability language instead of OKR-specific terms
- Migration auto-removes deprecated `adrs/` and `roadmap/` directories on update
- Framework documentation moved to `handbook/` directory
- `docs/` is now exclusively for user-generated content
- `/doc` without project writes to `docs/general/` instead of requiring project

## [3.1.0] - 2026-02-14

### Added
- Update system: `scripts/update.sh` for safe framework updates from upstream
- `/update` skill for checking and applying updates from within Claude
- `VERSION` file as single source of truth for release tracking
- Interactive `/onboard` rewrite with project setup wizard
- QMD integration documentation (`docs/QMD_INTEGRATION.md`)

### Changed
- Path standardization: dropped `00-inbox`/`01-index` prefixed folders
- Setup hardening: `scripts/setup.sh` validates prerequisites and handles edge cases
- Vault consistency pass: standardized paths, hardened setup, fixed stale refs

### Fixed
- Stale references to old folder naming conventions
- Broken wikilinks after path standardization

## [3.0.0] - 2026-01-15

### Added
- Initial PM-Kit release (renamed from MindLoom)
- 15 skills: daily, progress, block, decide, doc, meet, inbox, ask, health, adr, weekly, push, onboard, vault-ops, okr-tracking
- 4 agents: scribe, analyst, maintainer, processor
- QMD integration for hybrid search (BM25 + vector + rerank)
- Obsidian-compatible knowledge vault structure
- `_core/` framework: config.yaml, MANIFESTO.md, PROCESSING.md
- Template system with Handlebars syntax
- Naming-as-API conventions for glob-based queries
- Auto-archiving on status change
- PM Coach output style
- GitHub Actions workflow for changelog automation

[Unreleased]: https://github.com/kv0906/pm-kit/compare/v2026.06.29...HEAD
[2026.06.29]: https://github.com/kv0906/pm-kit/compare/v2026.04.05...v2026.06.29[2026.04.05]: https://github.com/kv0906/pm-kit/compare/v2026.03.11...v2026.04.05
[2026.03.11]: https://github.com/kv0906/pm-kit/compare/v2026.03.06.3...v2026.03.11
[2026.03.06.3]: https://github.com/kv0906/pm-kit/compare/v2026.03.06.2...v2026.03.06.3
[2026.03.06.2]: https://github.com/kv0906/pm-kit/compare/v2026.03.06...v2026.03.06.2
[2026.03.06]: https://github.com/kv0906/pm-kit/compare/v2026.03.05...v2026.03.06
[2026.02.25.2]: https://github.com/kv0906/pm-kit/compare/v2026.02.25...v2026.02.25.2
[2026.02.25]: https://github.com/kv0906/pm-kit/compare/v2026.02.15a...v2026.02.25
[2026.02.14]: https://github.com/kv0906/pm-kit/compare/v0.1.1...v2026.02.14
[3.1.0]: https://github.com/kv0906/pm-kit/compare/v3.0.0...v3.1.0
[3.0.0]: https://github.com/kv0906/pm-kit/releases/tag/v3.0.0
