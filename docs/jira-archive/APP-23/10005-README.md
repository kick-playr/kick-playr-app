# APP-23 — Team training UX proposal

UX only, 30 October 2026. No product implementation or service changes.

## Confirmed requirements

- User confirmed **weekly training timetable**, **training only**, **automatic stable team colors** in this chat.
- Calendar/list switch between season selector and teams in menu-8. Product icons: Heroicons `CalendarDateRangeIcon` / `QueueListIcon`.
- Shared Outlook-style calendar, readable team names, times and locations; consistent filters and permissions; teams without training accessible in list.

## Visual review

Open `proposal.html` directly. At desktop width it shows a shared seven-day time grid; at 760px and below it becomes a week strip and daily agenda. Click calendar/list, team checkboxes, dates, week navigation, session details, and the UX-state selector. Data is fictitious; week navigation shifts sample dates rather than calculating real occurrences. Season arrows explain intended behavior; they do not query or switch seasons. Icons are mockup placeholders, explicitly identified in the review footer.

## Recommended design decisions (pending user/Chris review)

- Retain the existing season card and list/editor layout. Add labeled, pressed-state Calendar/List buttons immediately below it. Retain list as initial view for existing users; remember explicit view choice in the existing hash-state convention.
- Desktop: shared seven-day week grid, previous/next week and Today. Open the week containing today if within the season, otherwise its first week; clamp navigation to the season, visibly disable out-of-range arrows. Render actual occurrence dates in Europe/Zurich from slot weekday, validity interval, season dates and vacation rules. Do not show a vacation-cancelled session as ordinary training. Chris must confirm authoritative occurrence calculation/API reuse.
- Mobile: same calendar selection and week navigation, seven touch-friendly day buttons, then chronological full-width sessions. Avoid squeezing seven event columns onto a phone. Desktop and mobile preserve week, day and team filters when resizing.
- Team checkboxes are a shared filter/legend. Use a compact filter disclosure with selected-count label when there are many teams on mobile. List includes selected teams without slots and says “Noch keine Trainings erfasst”. Do not fabricate a location; show “Ort noch offen”.
- Give each team a stable automatically assigned color keyed by Bexio team ID, independent of sorting/filtering/season. Use the same mapping in calendar, list and filter legend. Colors are never permissions or status. A persisted mapping or deterministic collision strategy is an architecture decision for Chris: a naive palette modulo cannot guarantee distinct colors for arbitrary team counts.
- Overlap: desktop sessions occupy adjacent lanes with no hidden event behind another. If lanes become too narrow for labels, show a count button opening the day's full agenda; do not silently truncate essential details. Mobile simultaneous sessions remain separate chronological cards with a time-conflict label, not overlapping cards. Conflicts describe coincident times, not proven pitch-booking conflicts.
- Selecting a session opens its team details/editor for the selected season. Keep existing role checks and backend authorization. No drag/drop scheduling or new mutations in this story.

## Alternatives considered

- Seven-column desktop grid plus mobile day agenda: recommended because it supports comparison on desktop and full event details on narrow screens.
- Seven-column grid on all devices: rejected; unreadable overlap lanes on phones.
- Agenda everywhere: accessible fallback/list option, but does not meet the requested Outlook-style desktop comparison as well.
- Monthly view and matches: excluded by the user's explicit weekly/training-only choice.

## Acceptance criteria for implementation review

1. Toggle appears after the season card and before team content, with the requested Heroicons, visible German labels, accessible names and `aria-pressed` state.
2. Calendar and list share permitted teams and filters; toggling/resizing preserves season and filter state. Calendar date remains stable when returning from list.
3. Session entries include team name, time range, location/fallback, stable team color and keyboard/touch access to details.
4. Weekly occurrences honor season boundaries, slot validity, vacations and Europe/Zurich daylight-saving changes. No matches or invented events.
5. Overlaps stay individually reachable at high density. Overflow count opens a complete accessible agenda with full labels and details.
6. List retains teams without schedules and access to the existing team editor.
7. Season-empty, no-selected-team, no-session-week and no-session-day states are distinct. Week-empty state links to list; no-filter state offers filter selection.
8. Initial loading announces status; background refresh keeps prior data visible with loading indication. Errors have retry and preserve view/filter state; stale data is explicitly marked. Do not expose personal data in errors.
9. Desktop and 320/375px mobile layouts do not clip essential text. Touch targets are at least 44px; controls have visible focus, labels and native keyboard operation; colors are supplemented by team names. Event rows/buttons expose complete accessible labels. List remains an equivalent nonspatial alternative.
10. No new product dependencies, API contract, permission expansion, editing workflow or implementation is approved by these mockups. Chris architecture/security sign-off is mandatory before coding.

## Current source inspected

`src/components/teams-trainers/teams-trainers.tsx` loads season/team details, merges Bexio and maintained teams, and renders `TeamEditor`. Expanded team uses `trainingTeam` hash state; trainers/previous season are loaded on editor expansion. No calendar or color mapping exists in this component. User screenshot matches this season-card/list arrangement.

## Handoff

All three product choices requested in APP-23 are now answered. UX proposal awaits review; recommendations above are not user-approved implementation details. Handoff to verified assignable Chris only, never directly to Jarvis. Preserve Freddie ownership if any new blocking product decision emerges.
