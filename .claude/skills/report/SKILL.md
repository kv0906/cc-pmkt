---
name: report
description: Executive briefing in plain language — report to me like I'm the CEO or product owner. Business impact and user impact only, no jargon. Use for "/report", "/report project-a", "report to me", "brief me", "what should I know", "executive summary", or when user wants a non-technical status update focused on outcomes not implementation.
allowed-tools: Read, Bash, Glob, Grep, Write, TaskCreate, TaskUpdate, TaskList, TaskGet
user-invocable: true
---

# /report — Report to Me

Brief the user as if they are the CEO or product owner. Plain language. Outcomes over output. No technical jargon unless translated into business or user impact.

## Context

Today's date: `!date +%Y-%m-%d`

Config: @_core/config.yaml
Processing logic: @_core/PROCESSING.md
Preview rules: @.claude/rules/preview-formats.md (when `--preview`)

## Usage

```
/report
/report project-a
/report all
/report project-a --week
/report all --preview
/report project-a --save --preview
```

## Flags

| Flag | Effect |
|------|--------|
| `--week` | Last 7 days (default) |
| `--since {date}` | Custom start date |
| `--save` | Write to `reports/{date}-executive-brief-{project}.md` |
| `--preview` | Also render HTML via `shell-executive.html` |
| `--risks-only` | Skip shipped/WIP — focus on blockers and decisions needed |

## Session Task Progress

```
TaskCreate: "Gather vault signals"
  activeForm: "Reading vault for executive signals..."

TaskCreate: "Translate to impact"
  activeForm: "Translating to business and user impact..."

TaskCreate: "Deliver brief"
  activeForm: "Preparing your briefing..."
```

## Voice & Rules

**You are briefing a decision-maker, not an engineer.**

### Always

- Lead with what matters most to the business
- Explain what changed for **users** (faster, safer, clearer, unblocked)
- Explain what changed for the **business** (revenue, risk, speed, cost, trust)
- Use short sentences. No acronyms without plain-English translation
- Quantify when data exists ("3 items shipped", "1 blocker overdue")
- End with what needs **their** decision or attention

### Never

- Stack traces, API names, framework choices, sprint mechanics
- "We merged PR #412" → say what capability that unlocks for users
- "Blocked on Redis" → say "users may see slower checkout until caching is fixed"
- Jira ticket IDs, branch names, implementation details
- Passive voice that hides ownership

### Translation examples

| Vault says | Report says |
|------------|-------------|
| Merged auth migration PR | Users can now sign in with their company account — fewer password resets |
| Blocked on API rate limits | Checkout may fail during peak traffic until we secure higher API limits |
| Decided PostgreSQL over MongoDB | We chose a database that handles financial transactions more reliably — reduces data-loss risk |
| WIP on notifications | Users will soon get alerts when their order ships — not live yet |

## Processing Steps

### 1. Parse Input

- Project: specific id, or `all` (default: all active projects from config)
- Timeframe: `--week` or `--since`

### 2. Gather Signals

Scan vault (same sources as `/progress`, different lens):

| Source | Extract for executive view |
|--------|---------------------------|
| `daily/*.md` | Shipped items, WIP themes, blocked themes |
| `blockers/{project}/*.md` | Open blockers — severity, due, user/business impact |
| `decisions/{project}/*.md` | Recent decisions — what it means for product direction |
| `docs/{project}/*.md` | Active specs — what capability is being built |
| `meetings/*.md` | Action items needing leadership input |

### 3. Synthesize Executive Brief

Structure output in this order:

```markdown
# Briefing — {Project or All Projects}
**{Date}** · {Period covered}

## Bottom Line
{2-3 sentences. Most important thing first. What should they walk away knowing?}

## What Users Get (or Will Get)
- {Shipped capabilities in user language}
- {In-progress capabilities — expected value, not ETA unless due date exists}

## Business Impact
- {Revenue, risk reduction, speed, cost, compliance, trust — only what's evidenced in vault}

## What's at Risk
- {Open blockers translated to user/business consequences}
- {Overdue items flagged clearly}

## Decisions Made (Recent)
- {Decision → why it matters to product/business, not technical rationale}

## Needs Your Attention
- {Explicit asks: approve, escalate, decide, unblock}
- {If nothing: "No decisions needed from you this period."}
```

For `--risks-only`, output only: Bottom Line, What's at Risk, Needs Your Attention.

### 4. Deliver

**Default:** Output to terminal. This is a spoken brief — concise, scannable.

**Do not** create files unless `--save` or `--preview`.

### 5. Optional: Save

If `--save`:
- Write markdown to `reports/{date}-executive-brief-{project}.md`
- Append vault log (action: `report`)

### 6. Optional: Preview

If `--preview`:
- Follow `/preview` workflow with `shell-executive.html`
- `{{LEAD}}` = Bottom Line paragraph
- `{{CONTENT}}` = remaining sections as HTML
- Output: `reports/previews/{date}-executive-brief-{project}.html`
- Open in browser unless `--no-open` passed through

### 7. Coaching Close

End with one direct line (PM Coach style):

> "The one thing I'd escalate: {highest-impact risk or decision}."

Or if clear:

> "You're in good shape — keep momentum on {top WIP theme}."

## Output Example

```
# Briefing — Project A
**2026-06-29** · Jun 22 – Jun 29

## Bottom Line
Checkout is live for most users, but peak-hour failures are still possible until we resolve the API limit blocker. No decisions needed from you today.

## What Users Get (or Will Get)
- Company sign-in is live — fewer password reset support tickets
- Order shipping alerts are in progress — users will know when packages leave the warehouse

## Business Impact
- Sign-in migration reduces support load and improves enterprise deal readiness
- Shipping alerts expected to cut "where is my order?" contacts

## What's at Risk
- API rate limits may cause checkout failures during promotions — backend team owns fix, due Friday

## Decisions Made (Recent)
- Chose PostgreSQL for billing data — stronger protection for payment records

## Needs Your Attention
- No decisions needed from you this period.

---
The one thing I'd escalate: API rate limit blocker before next marketing push.
```

## Relationship to Other Skills

| Skill | Difference |
|-------|------------|
| `/progress` | Technical/project status for operators — tables, filenames, severity |
| `/report` | Executive brief — impact language, no implementation detail |
| `/preview` | Renders any content as HTML — use `--preview` on report for shareable brief |
| `/weekly` | Team retrospective — process-focused, not CEO briefing |

## Empty Vault Handling

If no data in timeframe:

```
No meaningful updates in the last 7 days for {project}.
Suggest: run /daily to start capturing, or widen with --since.
```

Do not fabricate impact statements.