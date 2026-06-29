# Processing Logic

> I-P-O reference for AI agents. Quick lookup: Input → Process → Output.

---

## For AI Agents

**Always scan `_core/` first.** This is the working reference:

| File | Purpose | When |
|------|---------|------|
| `config.yaml` | Projects, paths, note types, status values | Always |
| `PROCESSING.md` | Command flows (this file) — I-P-O for each command | Always |
| `MANIFESTO.md` | Philosophy, principles, why we do things this way | Always |

**Before executing any command**: Read relevant `_core/` files to understand current config and conventions.

---

## File Paths

| Type | Pattern |
|------|---------|
| Daily | `daily/YYYY-MM-DD.md` |
| Doc | `docs/{project}/{slug}.md` |
| Decision | `decisions/{project}/YYYY-MM-DD-{slug}.md` |
| Blocker | `blockers/{project}/YYYY-MM-DD-{slug}.md` |
| Meeting | `meetings/YYYY-MM-DD-{type}-{slug}.md` |
| Index | `01-index/{project}.md` |
| Inbox | `00-inbox/YYYY-MM-DD-{slug}.md` |

---

## Commands

### `/daily`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project}: {shipped}, {wip}, {blocked}` |
| **Process** | Parse → segment by keywords → check/create blockers → update daily |
| **Output** | Updated `daily/{date}.md` section, linked blockers |

**Key**: One file per day, project sections within.

---

### `/block`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project}: {description} [--severity] [--owner] [--due]` |
| **Process** | Parse → duplicate check → prompt if exists → create note |
| **Output** | `blockers/{project}/{date}-{slug}.md` |

---

### `/decide`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project}: {decision} [alternatives] [context]` |
| **Process** | Parse → extract alternatives → find related docs → create note |
| **Output** | `decisions/{project}/{date}-{slug}.md`, doc backlinks |

---

### `/meet`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project} {type} {slug}` + raw notes |
| **Process** | Parse notes → extract decisions/blockers/actions → create linked notes |
| **Output** | `meetings/{date}-{type}-{slug}.md` + decision/blocker notes |

---

### `/doc`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project} {title}` |
| **Process** | Parse → load context → prompt sections if interactive |
| **Output** | `docs/{project}/{slug}.md` |

---

### `/inbox`

**Capture mode** (with content):

| I-P-O | Description |
|-------|-------------|
| **Input** | Raw content |
| **Process** | Detect project/type → tag for later |
| **Output** | `00-inbox/{timestamp}-inbox.md` |

**Process mode** (no content):

| I-P-O | Description |
|-------|-------------|
| **Input** | None |
| **Process** | List unprocessed → classify → confirm → route |
| **Output** | Notes in appropriate folders, marked processed |

---

### `/progress`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project}` [--week] [--since] |
| **Process** | Query dailies + blockers + docs + decisions → synthesize |
| **Output** | Terminal report (not saved) |

---

### `/preview`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{path}` or `progress {project\|all}` or `decide\|block\|meet\|doc {slug}` [--with-links] [--no-open] |
| **Process** | Read/synthesize → markdown to HTML → resolve wikilinks → apply shell template |
| **Output** | `reports/previews/{date}-{slug}.html`, open in browser |

---

### `/report`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{project\|all}` [--week] [--since] [--save] [--preview] [--risks-only] |
| **Process** | Gather vault signals → translate to business/user impact → executive brief |
| **Output** | Terminal brief (default); optional `reports/{date}-executive-brief-{project}.md` and HTML preview |

---

### `/today`

| I-P-O | Description |
|-------|-------------|
| **Input** | None |
| **Process** | Standup → Team Sync → Review → Focus → Wrap-up |
| **Output** | Daily notes, blocker updates, priorities set |

---

### `/health`

| I-P-O | Description |
|-------|-------------|
| **Input** | None |
| **Process** | Scan vault → check links/orphans/sections → generate stats |
| **Output** | `01-index/_graph-health.md` |

---

### `/ask`

| I-P-O | Description |
|-------|-------------|
| **Input** | `{question}` or `{project}: {question}` |
| **Process** | Detect query type → search relevant folders → rank results |
| **Output** | Answer with source links |

---

### `/help`

| I-P-O | Description |
|-------|-------------|
| **Input** | Optional topic (`daily`, `preview`, `report`, `export`, `workflow`, etc.) |
| **Process** | Load `handbook/HELP_GUIDE.md` → extract relevant section |
| **Output** | Cheat sheet or topic-specific guide (read-only) |

---

## Detection Keywords

| Trigger | Keywords |
|---------|----------|
| **Shipped** | shipped, done, completed, merged, deployed, released |
| **WIP** | wip, working on, in progress, continuing, started |
| **Blocker** | blocked, stuck, waiting on, need from, dependency |
| **Decision** | decided, going with, chose, selected, agreed |
| **Meeting** | meeting, call, discussed, attendees, action items |

---

## Mappings

### Severity

| Input | → | Output |
|-------|---|--------|
| critical, urgent, p0 | → | high |
| high, important, p1 | → | high |
| medium, normal, p2 | → | medium |
| low, minor, p3 | → | low |

### Meeting Type

| Input | → | Output |
|-------|---|--------|
| sync, standup, daily, weekly, sprint | → | sync |
| milestone, planning, kickoff, retro | → | milestone |
| external, client, stakeholder, vendor | → | external |

---

## Status Lifecycle

| Type | Values | Archive On |
|------|--------|------------|
| Blocker | open → resolved | resolved |
| Doc | draft → in-progress → shipped | shipped |
| Decision | active → superseded | superseded |

---

## Routing Logic

| Situation | Action |
|-----------|--------|
| Blocker mentioned | Search existing → link if similar, else prompt to create |
| Decision mentioned | Always create new (point-in-time) |
| Doc referenced | Link if exists, don't auto-create |
| Duplicate found | Prompt: update / new / skip |
| Project unknown | Prompt to select from config |
| Field missing | Prompt for it |

---

## Auto-Linking

| From | To |
|------|-----|
| Daily (blocked) | `blockers/{project}/` |
| Meeting | `decisions/{project}/`, `blockers/{project}/` |
| Decision | `docs/{project}/` |
| Doc | decisions, blockers |

---

## Index Updates

After note creation, refresh `01-index/{project}.md`:
- Recent dailies (7 days)
- Open blockers
- Active docs
- Recent decisions (30 days)
