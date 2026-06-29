# Preview Format Rules

When a user invokes `/preview` or appends `--preview` to `/report`, render vault content as styled HTML for browser viewing.

## Output Location

```
reports/previews/
  2026-06-29-checkout-flow.html
  2026-06-29-progress-all.html
  2026-06-29-decision-auth-approach.html
  2026-06-29-executive-brief-project-a.html
```

Pattern: `reports/previews/{YYYY-MM-DD}-{slug}.html`

## HTML Shells

Load from `_templates/html/`:

| Shell | Use When |
|-------|----------|
| `shell-prd.html` | `docs/{project}/*.md` (type: doc) |
| `shell-memo.html` | `decisions/`, `meetings/`, `blockers/`, general notes |
| `shell-status.html` | `/preview progress`, synthesized status reports |
| `shell-executive.html` | `/report` with `--preview` flag |

## Rendering Workflow

1. Read source markdown (or synthesize for `progress` / `report`)
2. Strip YAML frontmatter — use fields for template metadata
3. Convert markdown body to HTML:
   - Headings → `<h2>` / `<h3>` (H1 comes from shell title)
   - Lists, tables, blockquotes, bold, italic, code
   - No raw markdown left in output
4. Resolve wikilinks:
   - `[[path\|Title]]` → `<a href="#fn-N">Title</a>` with footnote, OR inline summary if `--with-links`
   - `[[Note#Section]]` → anchor link if same document
   - Broken links → plain text + footnote "(source not found)"
5. Inject into shell placeholders: `{{TITLE}}`, `{{CONTENT}}`, `{{DATE}}`, `{{PROJECT}}`, `{{SOURCE_PATH}}`, `{{FOOTNOTES}}`, `{{STATUS}}`, `{{PERIOD}}`, `{{METRICS}}`, `{{LEAD}}`
6. Write HTML file to `reports/previews/`
7. Open in browser: `open` (macOS), `xdg-open` (Linux), `start` (Windows)
8. Append vault log entry (action: `preview`)

## Wikilink Footnotes Block

When links are not inlined, append:

```html
<div class="footnotes">
  <h2>References</h2>
  <ol>
    <li id="fn-1"><strong>API Rate Limit</strong> — blockers/project-a/2026-06-29-api-rate-limit.md</li>
  </ol>
</div>
```

## Flags

| Flag | Behavior |
|------|----------|
| `--with-links` | Inline one-line summary from linked note body (first paragraph) |
| `--no-open` | Write file only, skip browser open |
| `--shell {name}` | Force shell: memo, prd, status, executive |

## Status Shell Metrics Block

For progress previews, inject `{{METRICS}}`:

```html
<div class="metrics">
  <div class="metric shipped"><div class="value">3</div><div class="label">Shipped</div></div>
  <div class="metric wip"><div class="value">2</div><div class="label">In Progress</div></div>
  <div class="metric blocked"><div class="value">1</div><div class="label">Blockers</div></div>
</div>
```

## Print CSS

All shells include `@media print` rules. Users can print-to-PDF from browser without extra dependencies.

## Combining with --save

`/report project-a --save --preview`:
1. Save executive brief markdown to `reports/{date}-executive-brief-{project}.md`
2. Also render `reports/previews/{date}-executive-brief-{project}.html`

## Source Note Frontmatter (Optional)

After preview, optionally update source note frontmatter:

```yaml
preview: reports/previews/2026-06-29-checkout-flow.html
```

Only add when user requests `--link` or when previewing a single doc/decision file.