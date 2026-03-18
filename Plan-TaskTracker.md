# AllDone — Living Product Plan

*Last updated: March 18, 2026 — Resumed. URD v1.1 + dual-timeline scoring complete. Ready for production build. | Current phase: Phase 0 — Personal Build*

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

## Google Sheets Data Schema — v1.1

*Updated to support dual-timeline scoring (execution score + investment coins)*

**Tab 1: `Tasks`**
| Column | Type | Notes |
|---|---|---|
| Date | YYYY-MM-DD | Task's assigned date |
| TaskID | UUID | Unique identifier |
| Title | String | Free text |
| Tag | Enum | urgent / deep / mind / body / home / passions |
| DueTime | HH:MM or blank | Optional, 24hr format |
| Status | 0 / 1 / 2 | 0=untouched, 1=started, 2=done |
| Priority | 1–5 or blank | Optional urgency rank within a day |
| IsOverflow | TRUE/FALSE | Rolled from a previous day? |
| OverflowFrom | YYYY-MM-DD or blank | Original date if overflow |
| OverflowCount | Number | How many times this task has been rolled (0 = original) |
| CoinsEarned | Number | Total coins earned on this task (0 for Urgent) |
| CreatedAt | Timestamp | For audit |

**Tab 2: `Routines`**
| Column | Type | Notes |
|---|---|---|
| RoutineID | UUID | Unique identifier |
| Title | String | e.g., "Walk 20 mins" |
| Active | TRUE/FALSE | Hidden if false |
| SortOrder | Number | Display order |

**Tab 3: `Settings`**
| Key | Value |
|---|---|
| tags | JSON array of tag objects (id, emoji, label, bg, text, coinStart, coinDone) |
| confettiEnabled | TRUE/FALSE |
| coinsEnabled | TRUE/FALSE |
| executionStreakCurrent | Number |
| executionStreakLongest | Number |
| investmentStreakCurrent | Number |
| investmentStreakLongest | Number |
| lastOpenedDate | YYYY-MM-DD |

**Tab 4: `DailyScores`**
| Column | Type | Notes |
|---|---|---|
| Date | YYYY-MM-DD | |
| ExecutionScore | Number | Done tasks ÷ total × 100 |
| FinalScore | Number | Same as ExecutionScore (no tag weighting) |
| RoutinesDone | Number | |
| RoutinesTotal | Number | |
| TasksDone | Number | |
| TasksTotal | Number | |
| CoinsEarnedToday | Number | Total coins earned on this date |
| StrategicRatio | Number | Coin-earning tasks done ÷ total tasks done × 100 |
| ExecutionStreakDay | Number | Running execution streak count |
| InvestmentStreakDay | Number | Running investment streak count |
| CountsForExecutionStreak | TRUE/FALSE | Score ≥ 50% OR all Urgent done |
| CountsForInvestmentStreak | TRUE/FALSE | ≥ 1 coin-earning task completed |

**Tab 5: `Quotes`**
| Column | Type | Notes |
|---|---|---|
| QuoteID | Number | Row index for rotation logic |
| QuoteText | String | The quote body |
| Attribution | String | e.g., "— Marcus Aurelius" |
| Active | TRUE/FALSE | Skip if false |

**Tab 6: `CoinLog`**
| Column | Type | Notes |
|---|---|---|
| Timestamp | Datetime | When coins were awarded |
| Date | YYYY-MM-DD | Day of award |
| TaskID | UUID | Which task triggered the award |
| Tag | Enum | Tag of the task |
| CoinsAwarded | Number | Amount awarded in this event |
| AwardType | start / done | Whether triggered by Start or Done tick |

*CoinLog is the full audit trail. Human-readable. User can open and understand it directly.*

---

## Phases & Milestones

### Phase 0 — Personal Build (Now)

**Goal:** A working tool I use every day for 4–6 weeks. No polish needed. Just functional.

- [x] Repo set up on GitHub — `github.com/aajosh10/AllDone` ✅
- [x] GitHub Pages enabled — `https://aajosh10.github.io/AllDone` ✅
- [x] Plan-TaskTracker.md committed to repo root ✅
- [x] Google Cloud project created (`AllDoneTracker`) ✅
- [x] Sheets API + Drive API enabled ✅
- [x] OAuth consent screen configured ✅
- [x] OAuth 2.0 Client ID created (`AllDone Web`) — ⚠️ old ID compromised (committed to repo) — regenerate before building
- [x] UI aesthetic direction finalized — **The Hepburn** (Audrey Hepburn × Flow App) ✅
- [x] UI prototype v6 complete — all screens built and approved ✅
- [x] Tag system redesigned — 6 life-category tags confirmed ✅
- [x] Quick-add bar spec finalized (pinned above nav, return to save, braindump mode) ✅
- [x] Drag reorder implemented (pointer events — works on touch + mouse) ✅
- [x] Edit Routines modal specced and built ✅
- [x] Journey screen insights: heatmap, tag bars, overflow patterns, streak card, sparkline ✅
- [x] URD v1.1 written and committed — `URD-AllDone.md` ✅
- [x] Dual-timeline scoring model locked (execution score + investment coins + strategic ratio) ✅
- [x] Project journey summary written — `ProjectSummary-AllDone.md` ✅
- [ ] Regenerate Google OAuth Client ID (⚠️ blocker — do on own machine first)
- [ ] Wire Google Sheets API + dual-timeline scoring into `alldone-v6.html` → becomes `index.html`
- [ ] 6-tab Sheet auto-created on first login (Tasks · Routines · Settings · DailyScores · Quotes · CoinLog)
- [ ] Real-time read/write on every status and coin change
- [ ] Carry-forward panel with user selection
- [ ] Proactive intelligence signals (morning nudge, drought signal)
- [ ] Pre-loaded sample data on first run
- [ ] Test on Windows Chrome + iPhone Safari/Chrome
- [ ] Push `index.html` to GitHub → live at `https://aajosh10.github.io/AllDone`
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

## Scoring System — Dual-Timeline Spec v1.1

*Two currencies measuring two different things. Both are required for a complete picture.*

### Timeline 1 — Execution Score (%)
*Did I handle what the world threw at me?*

```
Execution Score = points earned ÷ total possible × 100

Points:
  ✓ Started  = 0.5 per task (any tag)
  ✓✓ Done    = 1.0 per task (any tag)
  ○ Untouched = 0

Tag has NO effect on execution score.
Every task is worth the same — urgent grunt work and deep strategy alike.

Routines scored separately: shown as "3/3 ✓" — not folded into execution score.
```

**Execution streak rule:**
Day counts if `ExecutionScore ≥ 50%` OR all Urgent tasks are done (whichever is easier — rewards hard days).

---

### Timeline 2 — Investment Coins 🪙
*Did I build anything for the person I'm becoming?*

```
Coin values by tag:

  🔥 Urgent       →  Start: 0   Done: 0   (reactive work — score-only reward)
  💼 Deep Work    →  Start: 1   Done: 3
  🧠 Mind & Craft →  Start: 2   Done: 5
  🌿 Body & Soul  →  Start: 2   Done: 5
  🏡 Home & Heart →  Start: 2   Done: 5
  ✨ Passions     →  Start: 3   Done: 8   (highest signal, most neglected)

Coins on Start are intentional — protecting time deserves reward, not just delivering output.
Routines earn no coins — they are maintenance, not investment.
```

**Investment streak rule:**
Day counts if `≥ 1 coin-earning task completed` (any non-Urgent tag, Done state).

---

### Timeline 3 — Strategic Ratio
*What is my time actually made of?*

```
Strategic Ratio = coin-earning tasks completed ÷ total tasks completed × 100

Interpretation:
  0%       → Pure execution day. Fine occasionally. Pattern = problem.
  10–20%   → Mostly reactive. One investment task made it through.
  30%+     → Healthy. Executing and building simultaneously.
  50%+     → Deep work day. Rare and worth celebrating.

Shown in Journey only (30-day trend). Not on Today screen.
```

---

### Display — Today Screen Header
```
Score: 72%     🪙 14 this week     🔥 6 days  ·  🪙 2 days
```
- Score: primary, large (Execution %)
- Weekly coins: this week only (not all-time — all-time loses meaning)
- Both streaks: side by side, same size, different icons — never combined

---

### Proactive Intelligence Signals
```
Morning nudge (no coin-earning tasks on list):
  Replace daily quote with:
  "No strategic tasks today yet — what's one thing Future You would thank you for?"
  [ + Add one ] — pre-tagged Deep Work, editable

Investment drought (0 coins for 3+ consecutive days):
  Below score card:
  "3 days since your last investment 🪙"
  No red. No alarm. A mirror, not a punishment.

Weekly context (Monday morning):
  Last week's strategic ratio shown next to empty task list — as orientation, not grade.
```

---

### Journey — Investment Metrics
- Investment calendar heatmap (separate from execution heatmap)
- Strategic ratio 30-day trend line
- Tag allocation donut (which tags coins came from)
- Weekly coins sparkline — last 8 weeks
- Best investment week (positive anchor)
- Coin drought weeks visible but not penalised

---

## UI Aesthetic Spec — LOCKED (v6, March 17 2026)

**Aesthetic north star:** Audrey Hepburn meets the Flow App. Timeless elegance with youthful energy. Pastels with restraint — muted, sophisticated, not candy-bright. The energy comes from typography rhythm and graceful motion, not colour loudness. Think cream silk, not confetti paper.

**Typography:**
- Display / logo font: Playfair Display (Google Fonts) — logo italic with period ("AllDone."), section headers italic
- Body / task font: DM Sans (Google Fonts)

**Palette (locked):**
- Page background: `#F5F2ED` warm cream
- Score card: `#D8E8F5` sky blue
- Routines card: `#EBE6F7` lavender
- Carry banner: `rgba(249,213,190,0.6)` peach
- Task cards: `#FFFFFF` white with `box-shadow: 0 1px 10px rgba(0,0,0,0.05)`
- Nav active pill: `#E3DBFA` lavender
- Done tick fill: `#3EA843` bright green · Done text: `#2E7D32`
- Started tick border: `#E07A00` amber · Started text: `#C06000`

**Tag system (locked — 6 life-category tags):**
| Tag | Emoji | Label | Background | Text |
|---|---|---|---|---|
| urgent | 🔥 | Urgent | `#FADDCC` | `#842608` |
| deep | 💼 | Deep Work | `#D8E8F5` | `#163B70` |
| mind | 🧠 | Mind & Craft | `#EBE6F7` | `#3D1466` |
| body | 🌿 | Body & Soul | `#D8ECC8` | `#2A4A13` |
| home | 🏡 | Home & Heart | `#FDE8D8` | `#7A3010` |
| passions | ✨ | Passions | `#FCE4EC` | `#880E4F` |

**Task states (locked):**
| State | Tick | Text | Row |
|---|---|---|---|
| Untouched | White circle, 1.5px border `rgba(0,0,0,0.14)` | Normal weight, `#1A1A1A` | White |
| Started | Amber border `#E07A00`, unfilled, partial SVG stroke | Bold, `#C06000` | White |
| Done | Filled `#3EA843`, white SVG checkmark stroke draws in | Italic, `#2E7D32`, opacity 0.65 | White, reduced shadow |

**Screens and components (all built in v6, coin layer to be added in production build):**
- Today: logo header · score card (with weekly coins + both streaks) · daily quote / morning nudge · carry-forward banner · routines card (collapsible) · task list (with 🪙 badge on coin-earning tag chips) · score footer
- Journey: execution heatmap · investment heatmap · stat grid · strategic ratio trend · tag allocation donut · tag completion bars · overflow patterns · longest streak cards · weekly coins sparkline · routines sparkline
- Settings: confetti toggle · routines edit ↗ · tags display · Google Sheet link · sign out
- Quick-add bar: pinned above nav, tag chips with 🪙 indicators, return to save
- Edit Routines modal: inline rename, drag reorder, delete, add, 2 toggles
- Edit Task modal: text input, tag picker (with coin values shown), priority 1–5

**Interaction patterns (locked):**
- Drag reorder: pointer events (works touch + mouse), floating clone, placeholder slot
- Tick: tap anywhere except handle/title → cycles untouched → started → done
- Task detail: tap task title/body → edit modal slides up
- Routines: tap card header → expand/collapse · "edit" button → edit modal
- Quick add: type → Return OR tap + button → task added, cursor stays for braindump

**Quick-add bar visual:**
- Frosted translucent background block behind bar for visual separation from task list
- Extra padding (20px+) between bar and bottom nav tabs

**Google Sheets extra tab:**
- Tab 5: `Quotes` — columns: QuoteText · Attribution · Active (TRUE/FALSE)
- One quote shown per day, selected by day-of-year mod count
- In Phase 0: hardcoded Farnam Street-style quotes. In Phase 1+: pulled from Sheet.

**Animation spec (graceful, not bouncy):**
- Task entrance: `slideInUp 0.45s ease-out`, staggered 65ms per item
- Tick pop: `scale(1.2)` then settle, `cubic-bezier(.34,1.56,.64,1)`
- Confetti: 8 particles, radiate from tick, fade 0.75s, restrained palette
- Modal: `translateY(28px)` → 0, `cubic-bezier(.34,1.1,.64,1) 0.28s`
- Screen switch: `opacity` fade `0.22s`
- Drag lift: `scale(1.02)` + elevated shadow while dragging

---

## URD Reference

Full User Requirements Document: `URD-AllDone.md` (in repo root alongside this file)
Version: 1.1 | Last updated: March 18, 2026

Key URD sections relevant to build:
- Section 4: Full scoring system spec (dual-timeline, coins table, strategic ratio, two streaks, proactive intelligence)
- Section 5: Complete functional requirements per screen
- Section 9: Success metrics for Phase 0 (includes strategic ratio target ≥ 15% by week 4)
- Section 10: Research agenda — run after 4-week MVP trial, before Phase 1
- Section 11: Open decisions table (resolve before building each feature)

---

## Open Questions — Updated March 18, 2026

| Question | Proposal | Status |
|---|---|---|
| Streak logic if app not opened for a day | Check `lastOpenedDate` on load — gap > 1 day and previous day below threshold = silent reset on both streaks | TBD |
| Overflow tasks: keep original due time on rollover? | No — clear it, user can re-set | TBD |
| Tasks rolled 3+ times — signal? | Label changes to "→ carried 3×" — no alarm, just visibility | TBD |
| Past days editable? | Yes, with retroactive edit indicator on the task | TBD |
| Coin indicator on untouched tasks in Today list? | Small 🪙 on tag chip of coin-earning tasks — reminds of value as you scan | Pending decision |
| Coins toggle in Settings? | Phase 1 — not Phase 0 | Deferred |
| Routines own coin logic? | No — routines are maintenance, not investment | Confirmed |
| CoinLog tab in Sheet | Yes — full audit trail, human-readable | Confirmed |
| Cap on tasks per day? | No cap | Confirmed |
| Phase 1 pricing: free or freemium from day 1? | Free in Phase 1, freemium in Phase 2 | Confirmed |
| Product name | **AllDone** | Confirmed ✅ |

---

## Current Sprint

*Update this section at the start of every build session*

**Working on:** Production build — wiring Google Sheets API + dual-timeline scoring into finalized UI
**Blocked by:** ⚠️ New OAuth Client ID — old one was accidentally committed to public repo and is compromised. Must regenerate on own machine before the build starts.
**Next 3 actions:**
1. On own machine: go to Google Cloud Console → delete `AllDone Web` → create new OAuth 2.0 Client ID with same settings → save new Client ID securely (text note, NOT a file in the repo)
2. Start new Claude conversation → paste Context Handoff below + new Client ID → say "ready to build"
3. Wire Sheets API + dual-timeline scoring into `alldone-v6.html` → push as `index.html` → test live

---

## Google Cloud Setup Checklist ✅ COMPLETE — ⚠️ Client ID needs regeneration

- [x] Go to [console.cloud.google.com](https://console.cloud.google.com)
- [x] Create new project: `AllDoneTracker`
- [x] Enable **Google Sheets API**
- [x] Enable **Google Drive API**
- [x] Create **OAuth 2.0 Client ID** → Web application (`AllDone Web`)
- [x] Add Authorized JavaScript origins: `https://aajosh10.github.io`
- [x] Add Authorized redirect URIs: `https://aajosh10.github.io/AllDone`
- [x] Configure OAuth consent screen · Test user added · Scopes: `spreadsheets`, `drive.file`
- [⚠️] **Client ID `343637366388-nuoqpapjkfnsvu3jfmags84bph1jimrj` — COMPROMISED** — was committed to public repo. Delete and regenerate before building. Store new Client ID as a text note, never as a file in the repo.
- [ ] New Client ID regenerated and saved securely

---

## Context Handoff

*Paste this section into a new Claude conversation to resume without losing context*

**Project:** AllDone — Aayushi's personal task tracker → eventual SaaS product
**Repo:** `github.com/aajosh10/AllDone`
**Live URL:** `https://aajosh10.github.io/AllDone`
**Current phase:** Phase 0 — Personal Build
**Stack:** Single HTML file · Google Sheets API v4 · Google OAuth 2.0 · GitHub Pages

**Current state of codebase:** UI complete (`alldone-v6.html` approved). No Sheets integration yet. All planning documents complete.
**Last thing completed:** URD v1.1 · dual-timeline scoring model locked · project summary written · Plan file fully updated
**Exact next step:** Regenerate OAuth Client ID (old one compromised) → wire Sheets API + dual scoring into v6 → push as `index.html`
**Key decisions pending:** Coin indicator on untouched tasks (🪙 badge on tag chip — show or not? See Open Questions)

**Documents in repo:**
- `Plan-TaskTracker.md` — architecture decisions, milestones, this handoff
- `URD-AllDone.md` — full user requirements v1.1
- `alldone-v6.html` — approved UI prototype (all screens, all interactions)
- `ProjectSummary-AllDone.md` — project journey summary

**Core mechanic to never break:** Double-tick (○ → ✓ amber → ✓✓ green + confetti). No strikethroughs. No red. No failure language. Overflow = carry forward, user decides which tasks roll.

**Scoring — DUAL TIMELINE (locked):**
- Execution Score: every task = 1pt regardless of tag. ✓=0.5, ✓✓=1.0. Score = done÷total×100.
- Investment Coins: Urgent=0, Deep Work=1/3, Mind & Craft=2/5, Body & Soul=2/5, Home & Heart=2/5, Passions=3/8 (start/done).
- Two streaks: Execution (score≥50% OR all Urgent done) · Investment (≥1 coin-earning task done). Both reset silently.
- Strategic Ratio = coin-earning done ÷ total done × 100. Journey only. Target: 30%+.
- Morning nudge if no coin-earning tasks on list. Drought signal after 3 consecutive days of 0 coins.

**Sheet structure — 6 tabs:** Tasks · Routines · Settings · DailyScores · Quotes · CoinLog
Full column schemas in Architecture section above.

**Tag system — 6 tags (locked):**
urgent=🔥(0 coins) · deep=💼(1/3) · mind=🧠(2/5) · body=🌿(2/5) · home=🏡(2/5) · passions=✨(3/8)
