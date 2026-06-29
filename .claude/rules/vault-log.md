# Vault Log Convention

Every skill that creates, routes, or archives a note **must** append an entry to `01-index/_vault-log.md`.

## Format

Each entry is an H2 with a consistent prefix for grep-ability:

```markdown
## [YYYY-MM-DD] {action} | {title}

- **Project**: {project}
- **Files**: {list of files created/modified}
- **Source**: /{skill-name}
- **Details**: {one-line summary of what happened}
```

### Actions

| Action | When |
|--------|------|
| `daily` | `/daily` creates or updates a daily note |
| `blocker` | `/block` or auto-detected blocker created |
| `decision` | `/decide` or auto-detected decision created |
| `meeting` | `/meet` processes meeting notes |
| `doc` | `/doc` creates or updates a document |
| `inbox-capture` | `/inbox` captures a new item |
| `inbox-route` | `/inbox` routes items to destinations |
| `archive` | Maintainer archives a note |
| `index-regen` | Maintainer regenerates an index |
| `health` | `/health` runs a vault check |
| `preview` | `/preview` renders HTML preview |
| `report` | `/report` saves executive brief (--save) |

## How to Append

Use Edit to append at the **end** of the file. Never overwrite existing entries — the log is append-only.

```markdown
## [2026-04-05] daily | project-a

- **Project**: project-a
- **Files**: `daily/2026-04-05.md`
- **Source**: /daily
- **Details**: Shipped auth migration, 1 new blocker (API rate limits)
```

## Querying the Log

The consistent prefix makes the log queryable:

```bash
grep "^## \[" 01-index/_vault-log.md | tail -10    # Last 10 entries
grep "blocker" 01-index/_vault-log.md               # All blocker events
grep "project-a" 01-index/_vault-log.md              # All project-a events
```

## File Bootstrap

If `01-index/_vault-log.md` does not exist, create it with:

```markdown
# Vault Log

> Append-only chronological record of vault operations. Each entry is machine-parseable.
> Query: `grep "^## \[" 01-index/_vault-log.md | tail -10`

---
```
