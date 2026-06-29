---
name: help
description: PM-Kit command reference and workflow guide. Use for "/help", "what commands are available", "how do I use PM-Kit", "cheat sheet", "show me workflows", or "/help preview" for topic-specific help.
allowed-tools: Read, Glob, Grep
user-invocable: true
---

# /help — PM-Kit Help Guide

Surface the command reference and workflow guide. Read-only — no files created.

## Context

Full guide: @handbook/HELP_GUIDE.md
Config: @_core/config.yaml
Workflow examples: @handbook/WORKFLOW_EXAMPLES.md
Processing flows: @_core/PROCESSING.md

## Usage

```
/help
/help daily
/help preview
/help report
/help export
/help integrations
/help workflow
/help folders
```

## Processing Steps

### 1. Parse Topic

| Input | Action |
|-------|--------|
| No args | Show quick cheat sheet + category overview |
| `daily`, `today` | Daily operations section |
| `preview`, `html` | Preview & HTML artifact section |
| `report`, `executive` | Report to me section + vs /progress |
| `export`, `xlsx`, `docx`, `pdf`, `pptx` | Export flags section |
| `integrations`, `jira`, `linear`, `notion` | Integrations section |
| `workflow`, `routine` | Recommended workflows section |
| `folders`, `structure`, `paths` | Folder map section |
| `ask`, `qmd` | Capture & retrieval section |
| `setup`, `onboard` | Setup & maintenance section |
| `{command}` | Look up specific command in HELP_GUIDE.md |

If topic not found, show cheat sheet and suggest closest match.

### 2. Load Content

Read `handbook/HELP_GUIDE.md` and extract the relevant section(s).

For specific commands, also check if `.claude/skills/{command}/SKILL.md` exists — include one-line description and example invocation.

### 3. Format Output

**Default (`/help`):**

```markdown
# PM-Kit Help

## Quick Cheat Sheet
{table from HELP_GUIDE.md}

## Categories
- **Daily:** /today, /daily, /progress, /report, /weekly, /push
- **Artifacts:** /doc, /decide, /block, /meet
- **Preview:** /preview, /report --preview
- **Export:** --xlsx, --docx, --pdf, --pptx
- **Capture:** /inbox, /ask
- **Maintenance:** /onboard, /health, /update, /help

## New here?
/onboard → /today → /daily project-a: shipped X, wip Y

Full guide: handbook/HELP_GUIDE.md
```

**Topic-specific (`/help preview`):**

Return only that section from HELP_GUIDE.md, plus 2-3 example commands.

### 4. Suggest Next Command

End with one contextual suggestion based on time of day or topic:

| Context | Suggestion |
|---------|------------|
| Morning / daily topic | "Try: `/today` or `/daily project-a: ...`" |
| Preview topic | "Try: `/preview docs/{project}/{slug}.md`" |
| Report topic | "Try: `/report all` or `/report --preview`" |
| No topic, Friday | "It's Friday — try `/weekly` then `/report all --preview`" |
| Default | "Try: `/help workflow` for full daily routine" |

## Rules

- **Read-only** — never create or modify files
- **Concise** — cheat sheet first, details on request
- **Point to handbook** — for deep dives, cite `handbook/HELP_GUIDE.md`
- **No skill dumping** — don't list all 34 skills unless user asks for "all skills"

## List All Skills (only when asked)

If user says `/help all` or "list all skills":

```
Glob: .claude/skills/*/SKILL.md
```

Output table: `Command | One-line description | User-invocable?`

Group by category matching HELP_GUIDE.md structure.