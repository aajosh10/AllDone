# AllDone — Living Product Plan

*Last updated: March 17, 2026 | Current phase: Phase 0 — Personal Build*

---

## Vision

A warm, emotionally intelligent daily task tracker for people who get overwhelmed by volume and need momentum, not pressure. Built on the insight that *starting* and *finishing* feel different — and both deserve recognition. No strikethroughs, no red, no failure language. Just two ticks and a little joy.

**Target user (Phase 0):** Me — a Senior PM managing a complex portfolio who needs to pace herself, not sprint and crash.
**Target user (Phase 1):** Anyone who manages complex solo workloads and finds traditional task tools either too simple, too cold, or too punishing.
**Target user (Phase 2+):** Teams and individuals across knowledge-work roles who struggle with task completion, not task capture.

---

## Emotional Design Principles

These are non-negotiable across all phases. Features can change. These cannot.

1. **No negative visual language.** No strikethroughs, no red states, no "overdue" alarms, no failure counters.
2. **The double-tick is the heart.** ○ → ✓ (started, amber) → ✓✓ (done, green + confetti). Borrowed from a childhood habit of ticking book indices before exams. It worked then. It works now.
3. **Overflow, not failure.** Unfinished tasks are "carried forward." The user chooses which ones. Nothing is lost.
4. **Score what got done.** The scoring system adds bonuses for priority completions. It can go above 100%. Streaks reward effort on hard days, not perfection.
5. **Calm aesthetics.** Warm cream background, sage green for done, amber for started. Breathing room. No dashboard that looks like a cockpit.

---

## Architecture Decisions Log

| Decision | Choice | Rationale | Revisit When |
|---|---|---|---|
| Frontend | Single HTML file (vanilla JS) | Portable, no build step, no framework dependency, fast to iterate | Phase 2 — when complexity justifies a framework |
| Storage (Phase 0) | Google Sheets API (real-time) | Zero infra cost, user owns their data, accessible across devices, familiar format | Phase 2 / 100+ users |
| Storage (Phase 2) | Supabase or Firebase (TBD) | Proper multi-tenancy, team features, no Google dependency | Decide at Phase 1 exit |
| Auth | Google OAuth 2.0 | Required for Sheets API, familiar to users, handles identity for free | Phase 2 — add email auth |
| Multi-tenancy model | BYO Google Sheet (Phase 1) | No backend needed, user data stays in their Drive, zero liability | Phase 2 |
| Hosting | GitHub Pages | Free, version-controlled, deploys on push to main | Phase 2 — consider Vercel/Netlify if SSR needed |
| Repo visibility | Public | Enables free GitHub Pages, signals credibility, forces clean code | Keep public unless IP concern emerges |
| Mobile strategy | Mobile-first responsive HTML → PWA | No app store friction, works on any device today | Phase 2/3 — evaluate React Native |
| Monetization | Free forever (personal) + paid (teams/integrations) | Build trust first, monetize on expanded value | Phase 2 |

---

## Google Sheets Data Schema

**Tab 1: `Tasks`**
| Column | Type | Notes |
|---|---|---|
| Date | YYYY-MM-DD | Task's assigned date |
| TaskID | UUID | Unique identifier |
| Title | String | Free text |
| Tag | Enum | Urgent / Great for Career / Long-term Goodness / Personal |
| DueTime | HH:MM or blank | Optional, 24hr format |
| Status | 0 / 1 / 2 | 0=untouched, 1=started, 2=done |
| IsRoutine | TRUE/FALSE | Routine tasks tracked separately |
| IsOverflow | TRUE/FALSE | Was this rolled from a previous day? |
| OverflowFrom | YYYY-MM-DD or blank | Original date if overflow |
| ScoreContribution | Number | Calculated on completion |
| CreatedAt | Timestamp | For audit |

**Tab 2: `Routines`**
| Column | Type | Notes |
|---|---|---|
| Title | String | e.g., "Walk 20 mins" |
| Active | TRUE/FALSE | Hidden if false |
| SortOrder | Number | Display order |

**Tab 3: `Settings`**
| Key | Value |
|---|---|
| tags | JSON array of tag objects (name, color) |
| confettiEnabled | TRUE/FALSE |
| streakCurrentDay | Number |
| streakLongest | Number |
| lastOpenedDate | YYYY-MM-DD |

**Tab 4: `DailyScores`**
| Column | Type | Notes |
|---|---|---|
| Date | YYYY-MM-DD | |
| BaseScore | Number | Done/Total × 100 |
| PriorityBonus | Number | +5 per Urgent task done |
| FinalScore | Number | Base + Bonus |
| RoutinesDone | Number | |
| RoutinesTotal | Number | |
| TasksDone | Number | |
| TasksTotal | Number | |
| StreakDay | Number | Running streak count on this date |
| CountsForStreak | TRUE/FALSE | Score ≥ 50% OR all Urgent done |

---

## Phases & Milestones

### Phase 0 — Personal Build (Now)

**Goal:** A working tool I use every day for 4–6 weeks. No polish needed. Just functional.

- [x] Repo set up on GitHub — `github.com/aajosh10/AllDone` ✅
- [x] GitHub Pages enabled — `https://aajosh10.github.io/AllDone` ✅
- [ ] Clone repo to local machine (pending — git account check needed)
- [ ] Plan-TaskTracker.md committed to repo root
- [ ] Google Cloud project created, Sheets API enabled, OAuth configured
- [ ] Core UI: today view with task list and routines panel
- [ ] Double-tick mechanic: ○ → ✓ (amber) → ✓✓ (green + confetti)
- [ ] Google Sheets real-time read/write on every status change
- [ ] Carry-forward panel for unfinished tasks
- [ ] Tag chips: Urgent / Great for Career / Long-term Goodness / Personal
- [ ] Due time field with past-due amber indicator
- [ ] Daily scoring: base % + Urgent bonus + routines separate
- [ ] Streak counter with soft reset (no negative language)
- [ ] Dashboard: calendar heatmap (weeks as rows, months as columns)
- [ ] Dashboard: click a day → side panel with that day's summary
- [ ] Dashboard: streak stats, top tag, overflow rate, routines sparkline
- [ ] Settings: manage routines, edit tags, toggle confetti, sign out
- [ ] Pre-loaded sample data on first run
- [ ] Mobile-responsive: bottom nav, 44px tap targets, no hover-only states
- [ ] Use personally for 4 weeks minimum before Phase 1

### Phase 1 — Shareable (3–6 months post Phase 0)

**Goal:** Anyone can use this with their own Google account. Zero backend. 10 real beta users.

- [ ] Multi-user OAuth: shared Google Cloud app with proper consent screen
- [ ] Auto-create Sheet from template on first login (copy from master template Sheet)
- [ ] Onboarding flow: name, routine tasks, tag customization on first run
- [ ] README with setup instructions + demo GIF
- [ ] GitHub Pages as the canonical public URL
- [ ] Issue templates: bug report, feature request
- [ ] Recruit 10 beta users (target: PMs, consultants, solo operators)
- [ ] Feedback mechanism (lightweight — Google Form linked from app)
- [ ] CHANGELOG.md maintained from this point forward
- [ ] Decide product name — **AllDone** ✅ confirmed

### Phase 2 — Product (6–18 months post Phase 1)

**Goal:** Real product. Proper backend. Potential revenue.

- [ ] Evaluate Supabase vs Firebase vs PlanetScale — decide based on Phase 1 learnings
- [ ] Proper auth: email/password + Google SSO
- [ ] PWA: installable on mobile home screen, offline support
- [ ] Team/shared lists: invite by email, shared task board
- [ ] Integrations (prioritize based on user demand): Outlook calendar, Notion, Jira/ADO
- [ ] Pricing model: free (personal) / paid (teams + integrations)
- [ ] Custom domain
- [ ] Analytics: PostHog or equivalent (privacy-first)
- [ ] Native mobile app: evaluate React Native at this stage

---

## Scoring System — Spec

```
Daily Score = Base Score + Priority Bonus

Base Score = (Tasks with Status=2) / (Total tasks excl. routines) × 100
Priority Bonus = (Urgent tasks with Status=2) × 5

Routines Score = Routines done / Total active routines  [shown separately]

Streak Rule = Day counts if:
  FinalScore >= 50
  OR all Urgent tasks have Status=2
  (whichever is easier — rewards effort on hard days)

Streak resets to 0 silently. No negative language on reset.
Score CAN exceed 100% — this is intentional and rewarding.
```

---

## UI States Reference

| Element | Untouched | Started | Done |
|---|---|---|---|
| Status indicator | ○ | ✓ | ✓✓ |
| Row background | `#FAF8F4` (cream) | `#FFF3E0` (amber tint) | `#C8E6C9` (sage green) |
| Tick color | — | `#E65100` (deep amber) | `#2E7D32` (forest green) |
| Animation | — | subtle pulse once | 1-sec confetti burst |

**Tag colors:**
- 🔴 Urgent: `#FFCCBC` (soft coral)
- 🔵 Great for Career: `#BBDEFB` (sky blue)
- 🟣 Long-term Goodness: `#E1BEE7` (lavender)
- 🍑 Personal: `#FFE0B2` (warm peach)

---

## Open Questions

- [ ] What happens to streak if user doesn't open the app for a day? (Proposal: check `lastOpenedDate` on load — if gap > 1 day and previous day score < threshold, streak resets)
- [ ] Should routines have their own streak counter separate from main streak?
- [ ] Phase 1 pricing: fully free or freemium from day 1? (Lean: free in Phase 1, freemium in Phase 2)
- [x] Product name — **AllDone** ✅ confirmed March 17, 2026
- [ ] Should overflow tasks lose their original due time when rolled forward?
- [ ] Cap on number of tasks per day? (No cap for now — user responsibility)
- [ ] Should past days be editable? (Proposal: yes, but with a visual indicator that it was edited retroactively)

---

## Current Sprint

*Update this section at the start of every build session*

**Working on:** Phase 0 setup — pre-build
**Blocked by:** Google Cloud OAuth setup (doing in ~4-5 hours)
**Next 3 actions:**
1. Check Git account config (`git config --global user.name`) and clone repo
2. Add this Plan file to repo root and push first commit
3. Complete Google Cloud setup (Sheets API + OAuth Client ID), then run build prompt

---

## Google Cloud Setup Checklist

*(One-time setup, required before the app can read/write Google Sheets)*

- [ ] Go to [console.cloud.google.com](https://console.cloud.google.com)
- [ ] Create new project: `task-tracker`
- [ ] Enable **Google Sheets API**
- [ ] Enable **Google Drive API** (needed to create Sheets on user's Drive)
- [ ] Go to **APIs & Services → Credentials**
- [ ] Create **OAuth 2.0 Client ID** → Web application
- [ ] Add Authorized JavaScript origins: `https://aajosh10.github.io`
- [ ] Add Authorized redirect URIs: `https://aajosh10.github.io/AllDone`
- [ ] Copy **Client ID** → paste into `index.html` config section
- [ ] Configure OAuth consent screen: App name, logo, scopes (`spreadsheets`, `drive.file`)

---

## Context Handoff

*Paste this section into a new Claude conversation to resume without losing context*

**Project:** AllDone — Aayushi's personal task tracker → eventual SaaS product
**Repo:** `github.com/aajosh10/AllDone`
**Live URL:** `https://aajosh10.github.io/AllDone`
**Current phase:** Phase 0 — Personal Build
**Stack:** Single HTML file · Google Sheets API v4 · Google OAuth 2.0 · GitHub Pages

**Current state of codebase:** Not started — no index.html yet
**Last thing completed:** GitHub repo created (`aajosh10/AllDone`), Pages enabled, Plan file drafted
**Exact next step:** Complete Google Cloud OAuth setup → get Client ID → run build prompt to generate index.html
**Key decisions pending:** None — all architecture decisions locked. See Architecture Decisions Log above.

**Core mechanic to never break:** Double-tick (○ → ✓ amber → ✓✓ green + confetti). No strikethroughs. No red. No failure language. Overflow = carry forward, user decides which tasks roll.

**Scoring:** Base % + 5pt bonus per Urgent task done. Streak counts if score ≥ 50% OR all Urgent done. Can exceed 100%. No streak penalty language.

**Sheet structure:** 4 tabs — Tasks · Routines · Settings · DailyScores. See Architecture section above for full column schema.
