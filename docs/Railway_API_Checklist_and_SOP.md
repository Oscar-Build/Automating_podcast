# YouTube Factory — Railway API Checklist & Operating SOP

## Part A: Railway Environment Variables Checklist

Track each variable's status as it's added. Mapped to the milestone that needs it, so nothing gets requested before it's actually required.

| # | Variable / Credential | Needed for (Milestone) | Status |
|---|---|---|---|
| 1 | `GOOGLE_CLIENT_SECRET` (from `client_secret.json`, YouTube Data API OAuth) | M0 — Task 0.4 | ☐ |
| 2 | `GAMMA_API_KEY` | M0 — Task 0.5 | ☐ |
| 3 | `YOUTUBE_REFRESH_TOKEN_EDP_EN` | M2 — Task 2.2 | ☐ |
| 4 | `YOUTUBE_REFRESH_TOKEN_EDP_ES` | M2 — Task 2.2 | ☐ |
| 5 | `SUPABASE_URL` | M3 — Task 3.1 | ☐ |
| 6 | `SUPABASE_SERVICE_KEY` | M3 — Task 3.1 | ☐ |
| 7 | `N8N_INSTANCE_URL` | M3 — Task 3.2 | ☐ |
| 8 | `N8N_API_KEY` | M3 — Task 3.2 | ☐ |
| 9 | `TELEGRAM_BOT_TOKEN` | M3 — Task 3.4 (alerts) | ☐ |
| 10 | `TELEGRAM_GROUP_ID` | M3 — Task 3.4 (alerts) | ☐ |
| 11 | `GITHUB_TOKEN` (repo-scoped, for auto-push per MASTER_CLAUDE standard) | Ongoing | ☐ |

**Rule:** don't request a credential before its milestone is actually in progress — keeps the Railway variable list from filling up with unused keys.

## Part B: GitHub / Board Operating SOP

### Repo setup (one-time, done)
- ✅ `Automating_podcast` marked as a Template Repository
- ✅ Kiss and Ver added as collaborators (confirmed via existing repo access)

### Issue conventions
- One owner per issue — assigned via GitHub Assignee now that the team has repo access (previously tracked via `owner:*` labels before assignees were possible)
- Every issue body ends with a **Done means** line — that line is the acceptance criteria, not a suggestion
- Milestones = phases (M0–M4); an issue's milestone tells you which phase it belongs to, not when to start it — work top to bottom per the Build Plan's stated rule

### Board columns (GitHub Project)
`Backlog → In Progress → Review → Done`

- New issues land in Backlog
- Move to In Progress only when someone is actively working it (avoid multiple things "in progress" per person)
- Review = done by the assignee, waiting on Oscar's approval (per the "Done means" line)
- Done = Oscar has approved

### Status visibility (three views, one glance each)
- **GitHub Project board** → live status of every task
- **Weekly `dashboard/REPORT.md`** → results (once Analytics Agent, M3 Task 3.6, is live)
- **Telegram** → daily pulse / alerts

### Reassignment note
All 28 current issues are assigned to Kiss as a placeholder so nothing sits unassigned. Reassign individual issues to Ver, Dave, or Oscar as work starts on each — the `owner:*` labels are still attached to each issue as a reference for who the Build Plan originally intended.

### The hard rule (repeated from the Build Plan — don't skip this)
No channel work beyond EDP English + Spanish starts until Milestone 3's gate review (Task 3.8) passes.
