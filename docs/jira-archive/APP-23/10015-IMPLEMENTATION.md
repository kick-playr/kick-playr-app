# APP-23 implementation handoff — 30.10.2026

Chris approval: Jira comment 10023. Implemented as Jarvis; independent Trip QA remains pending. No commits, version bumps, merges, deployments or development-service changes.

## Workspaces

- APP: `/Users/Lolo/git/fcrww-app-APP-23-ux`, branch `codex/app-23-teams-calendar-ux`, base `e2e93d3`.
- API: `/Users/Lolo/.codex/worktrees/app-23-training-calendar/fcrww-api`, branch `codex/app-23-training-occurrences`, base `1528a5d`.
- Changes are uncommitted, including new files. Both primary checkouts were verified clean and left untouched.

## Result

Menu-8 has labeled Heroicons calendar/list buttons after season navigation. Initial view remains list. The original TeamEditor source is unchanged and stays mounted while hidden, retaining unsaved drafts. Calendar sessions open its existing team editor.

Lazy-loaded FullCalendar Standard 6.1.22 React/TimeGrid uses Luxon 3 for named-zone rendering. Desktop and compact mobile retain seven days and separate simultaneous sessions (`slotEventOverlap=false`, no grouping/max-stack cap). Team colors derive only from stable Bexio team IDs. Calendar filters/week/view use existing hash helpers; repeated non-string keys are rejected. Accessible full-size detail buttons supplement tiny mobile cells.

`GET /api/team-details/calendar?season=26.2&weekStart=2026-10-26` returns `{ season, weekStart, weekEnd, occurrences }`, with exclusive weekEnd and minimal `{ id, teamId, teamName, start, end, location? }` events. weekStart must be an ISO Monday; duration is always seven Zurich local days. Existing Clerk guard and admin assertion remain mandatory; response is private/no-store. No new external integrations or write behavior.

API generation reuses season, slot-validity and members-vacation helpers. Trial-only breaks do not cancel regular training. Past weeks have no current-time cutoff. Nonexistent spring-DST wall times are omitted rather than shifted; repeated autumn wall times use the earliest instant deterministically. UTC offsets are retained in timestamps. Unknown-season/malformed week reads fail validation; orphan records outside configured season teams are not returned.

## Verification

- API: `npm test -- --runInBand`: **70 suites, 1327 tests passed**. Includes 11 weekly occurrence tests and three HTTP authorization/cache tests.
- API and APP: `npx tsc --noEmit` passed.
- APP: calendar helper + existing training-slot tests: **5 passed**; `npm run test:notifications`: **1 passed**; `npm run test:versions`: **14 passed**.
- APP targeted ESLint passed for touched product TS/TSX files. API controller/new tests lint passed. API service lint reports two pre-existing errors in untouched code (`no-empty-object-type` on normalizedWhatsappGroupInvitationUpdate's existing `{}` union, and unused `_ignored` in existing enrichment). Those lines also exist in baseline HEAD; no unrelated fixes were made.
- `scripts/test-training-calendar-browser.mjs` bundles actual TeamsTrainers, unchanged TeamEditor and FullCalendar; replaces only data/auth/dynamic-loader boundaries with controlled fixtures. It passed overlap-lane geometry and selectable keyboard details at **320/375/760/1440px**, America/Los_Angeles browser timezone displaying Zurich 18:00, list draft preservation, event-to-editor navigation, calendar-only filtering and loading/error/empty/stale-response states. Repeated hash key regression was reproduced failing and fixed.
- Actual browser captures: `implemented-mobile.png`, `implemented-desktop.png`. These are implementation screenshots using controlled data, not live account data.
- Browser runner requires existing esbuild and Playwright tooling. Example in this workspace:

```sh
APP23_ESBUILD_MODULE=/Users/Lolo/.npm/_npx/beb367dfa21eb3f5/node_modules/esbuild APP23_PLAYWRIGHT_MODULE=/Users/Lolo/git/fcrww-api/node_modules/@playwright/test node scripts/test-training-calendar-browser.mjs
```

## Limits / Trip focus

Running localhost services remain on the unchanged primary checkouts, so a live account round-trip through these isolated changes was not performed. No release build was prepared. Verify coordinated APP/API runtime integration, real schedule/vacation data and release packaging independently before integration. Tiny overlap targets are supplemented by an equivalent full-size weekly-details disclosure; team names remain the identifier when colors are similar. Colors are deterministic, not a guarantee of unlimited unique colors.

Preliminary independent code review found one P2 (repeated hash arrays could crash filtering); browser regression verified RED→GREEN. This review is not Trip QA and does not approve merge/deployment.
