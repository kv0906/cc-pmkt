---
name: preview
description: Render vault notes as styled HTML and open in browser. Turn markdown docs, decisions, progress reports into readable previews. Use for "/preview docs/project-a/checkout-flow.md", "/preview progress all", "/preview decide auth-approach", or when user wants to see a doc before sharing.
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, TaskCreate, TaskUpdate, TaskList, TaskGet
user-invocable: true
---

# /preview — HTML Preview

Render vault content as styled HTML for browser viewing. Markdown stays the source of truth; HTML is the presentation layer.

## Context

Today's date: `!date +%Y-%m-%d`

Config: @_core/config.yaml
Preview rules: @.claude/rules/preview-formats.md
HTML shells: @_templates/html/
Processing logic: @_core/PROCESSING.md

## Usage

```
/preview docs/project-a/checkout-flow.md
/preview decisions/project-a/2026-06-29-auth-approach.md
/preview progress all
/preview progress project-a --week
/preview decide auth-approach
/preview block api-rate-limit
/preview meet 2026-06-29-sync-sprint-review
/preview reports/2026-06-29-progress-all.md
```

## Flags

| Flag | Effect |
|------|--------|
| `--with-links` | Inline first paragraph from linked notes |
| `--no-open` | Write HTML only, do not open browser |
| `--shell memo\|prd\|status\|executive` | Force template shell |
| `--link` | Write `preview:` path to source note frontmatter |

## Session Task Progress

```
TaskCreate: "Resolve preview source"
  activeForm: "Resolving preview source..."

TaskCreate: "Render HTML"
  activeForm: "Rendering HTML preview..."

TaskCreate: "Open and verify"
  activeForm: "Opening preview in browser..."
```

## Processing Steps

### 1. Parse Input

Determine source mode:

| Input pattern | Action |
|---------------|--------|
| Path to `.md` file | Read that file directly |
| `progress {project\|all}` | Synthesize like `/progress`, use status shell |
| `decide {slug}` | Glob `decisions/*/*{slug}*.md`, pick latest |
| `block {slug}` | Glob `blockers/*/*{slug}*.md` |
| `meet {slug}` | Glob `meetings/*{slug}*.md` |
| `doc {project} {slug}` | Read `docs/{project}/{slug}.md` |

If ambiguous, ask user to clarify path.

### 2. Gather Content

**File mode:** Read markdown + frontmatter.

**Synthesis mode (`progress`):** Follow `/progress` gather steps:
- Scan dailies, blockers, docs, decisions for project/timeframe
- Build structured sections: Shipped, In Progress, Blockers, Decisions
- Count metrics for status shell

### 3. Select HTML Shell

Auto-detect from source:

| Source | Shell |
|--------|-------|
| `docs/` | `shell-prd.html` |
| `decisions/`, `meetings/`, `blockers/` | `shell-memo.html` |
| `progress` synthesis | `shell-status.html` |
| `reports/*executive*` | `shell-executive.html` |
| Other | `shell-memo.html` (default) |

Override with `--shell` flag.

### 4. Convert Markdown → HTML

- Remove YAML frontmatter block
- Map frontmatter to shell vars: `project`, `status`, `date`, `title` (from H1 or filename)
- Convert body markdown to semantic HTML
- Resolve `[[wikilinks]]` per preview-formats.md
- Escape user content in HTML (no XSS — literal text only)

### 5. Write Output

- Ensure `reports/previews/` exists
- Filename: `{date}-{slug}.html` (slug from source file or synthesis type)
- Load shell template, replace all `{{PLACEHOLDER}}` tokens
- Write complete self-contained HTML file

### 6. Open Browser

Unless `--no-open`:

```bash
# macOS
open "reports/previews/{filename}.html"

# Linux
xdg-open "reports/previews/{filename}.html" 2>/dev/null

# Windows
start "" "reports/previews/{filename}.html"
```

### 7. Optional: Link Back to Source

If `--link` and source is a single vault note:
- Edit source frontmatter: `preview: reports/previews/{filename}.html`

### 8. Append Vault Log

Append to `01-index/_vault-log.md`:
- Action: `preview`
- Files: output HTML path + source path
- Details: shell used, synthesis or file mode

## Output

```
Preview ready: reports/previews/2026-06-29-checkout-flow.html
Shell: prd
Source: docs/project-a/checkout-flow.md
Opened in browser.
```

## Error Handling

| Situation | Action |
|-----------|--------|
| Source file not found | Suggest similar paths via Glob |
| Empty vault (progress) | Report "no data in timeframe" — do not write empty HTML |
| Browser open fails | Report file path; user can open manually |

## Design Principles

- **Typography first** — readable fonts, generous line-height, print-ready
- **No dependencies** — pure HTML/CSS, no npm/pip required
- **Self-contained** — single file, embedded styles, works offline
- **Source preserved** — never modify markdown body unless `--link` requested