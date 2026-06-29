# PM-Kit Workflow Examples

## Guided Daily Workflow with `/today`

Start your day with a structured workflow that guides you through standup, sync, review, and focus:

```
/today
```

**Phases**:
1. **Daily Standup** — Update each project (shipped/wip/blocked) with auto-detection of blockers and decisions
2. **Team Sync** — Process external context (Slack threads, meeting notes, Linear updates)
3. **Review** — See cross-project health dashboard (blockers, active docs, recent decisions)
4. **Focus** — View priorities, then do deep work
5. **Wrap-Up** — Summarize the day and commit

**Resume**: Run `/today` again after focus time to wrap up.
**Skip phases**: Use `/today skip` to jump to next phase.
**End of day**: Use `/today wrap` to jump straight to wrap-up.

For quick atomic updates without the full workflow:

```
/daily project-a: shipped auth flow, wip notifications
```

## Daily PM Workflow (Manual)

### Morning (5 min)

```
/onboard                # Load vault context
/daily project-a: shipped auth flow, wip notifications, blocked on API rate limits
/daily project-b: shipped dashboard v2, wip analytics, decided to use websockets
```

Claude will:
- Create/update `daily/2026-01-15.md` with sections for each project
- Detect "blocked on API rate limits" → prompt to create blocker
- Detect "decided to use websockets" → prompt to create decision

### Throughout the Day

```
/block project-a: waiting on legal review for compliance --severity high --due friday
/decide project-a: chose PostgreSQL over MongoDB for transaction data
/doc project-a checkout-flow
/inbox competitor launched similar feature — analyze later
/ask what did we decide about the auth approach?
```

### After Meetings

```
/meet project-a sync sprint-review
[paste meeting notes]
```

Claude extracts decisions, blockers, and action items into separate notes.

### End of Day

```
/push "End of day — shipped auth, 2 blocker updates"
```

## Weekly Sprint Retro

Run on Friday or Monday:

```
/weekly
```

Claude runs 3 phases:
1. **Collect** — Reads all dailies from the past week, aggregates shipped/WIP/blocked
2. **Reflect** — Finds patterns, checks what worked and what slowed progress
3. **Plan** — Helps set next sprint goals, identifies ONE Big Thing

Output: `reports/2026-01-15-sprint-retro.md`

## Vault Health

```
/health
```

Scans vault for:
- Broken wiki-links
- Orphan notes (no inbound links)
- Missing `## Links` sections
- Statistics by type and project

Output: `01-index/_graph-health.md`

## Cross-Project Status

```
/progress all
/progress project-a --since 2026-01-01
```

Synthesizes shipped items, blockers, and decisions across projects.

## Inbox Processing

Capture fast, process later:

```
# Throughout the day
/inbox need to review competitor pricing
/inbox idea: use webhooks for real-time updates
/inbox meeting with legal team about compliance

# End of day — batch process
/inbox
```

Claude classifies each item and routes to the correct folder.

## Multi-Project Daily Example

A typical `daily/2026-01-15.md`:

```markdown
---
type: daily
status: active
date: 2026-01-15
tags: []
---

# 2026-01-15

## Project A

### Shipped
- Auth flow complete and deployed
- Login page redesign merged

### In Progress
- Notification system (60%)
- Payment integration planning

### Blocked
- [[blockers/project-a/2026-01-15-api-rate-limit|API rate limit from provider]]

### Notes
Meeting with legal at 2pm about compliance requirements.

---

## Project B

### Shipped
- Dashboard v2 deployed

### In Progress
- Analytics pipeline

### Blocked
*None*

---

## Tomorrow
- Follow up on legal meeting outcomes
- Start payment integration spec
```

## Get Help Anytime

```
/help
/help workflow
/help preview
/help report
```

Full reference: [HELP_GUIDE.md](HELP_GUIDE.md)
