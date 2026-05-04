# Training Log

A personal fitness, nutrition, and run tracking PWA. Single-file HTML/CSS/JS — no build step, no backend, deploys to GitHub Pages and runs as a web app on iPhone.

## What's in the repo

- `index.html` — the entire app (~250KB, ~5500 lines)
- `icon.png` — home-screen icon (sunset-gradient dumbbell)
- `README.md` — this file

## Features

**Training**
- 5 default days (push/pull/legs split, fully editable)
- Per-set weight + rep logging with done-checkbox
- Exercise cards with primary + secondary muscle tracking, per-muscle accent stripe, and a PR badge when last session beats e1RM
- Tap-to-rename exercises, reorder via ↑↓, temp/permanent swap
- Persistent cues (survive week advance) + per-session notes
- Week navigation: ‹ / › — `›` on current week saves logs into history and advances the plan
- Volume sidebar with 15 granular muscle groups (incl. Glute Med, Glute Max separately) + a 7-day runs section
- Add/remove days from the day drawer

**Nutrition**
- Calories, protein, fat, carbs (with goal bars where applicable)
- Date navigator collapses to a chip showing "Today" or "Jan 15"; expand to pick a date or jump back
- Quick-add: name → cal → protein → carb → fat, all keyboard-flowed
- Open Food Facts search, barcode scanner (ZXing), photo logging (Claude Vision)
- Custom foods + meal templates

**Runs**
- Run types: Easy / Moderate / Tempo / Interval / Long / Sprint / Recovery
- Pace auto-calculates from distance + duration
- Interval & sprint detail fields (count, work pace, rest, top speed)
- Run slots with planned target vs logged actual

**Goals**
- Cut / Maintain / Bulk phases
- Profile (age, height, sex, activity)
- Calorie target follows the active phase
- JSON export/import for backup

**Progress**
- Overview: this week, weight, top 5 lifts, phase progress, last run
- Body: weight log + history
- Lifts: top 5 most-logged exercises, per-exercise charts
- Phase: on-track indicator (green/red, ±0.8lb tolerance)
- Runs: 8-week calendar + volume by type
- Review: Claude-powered weekly coaching with stale banner

## How it works

State lives in `window.storage` (Claude artifact storage). Keys are namespaced with `nl_`:

```
nl_plan        — day/exercise structure + week number
nl_logs        — current-week set logs
nl_exhist      — per-exercise history across weeks
nl_meals       — meal logs by date
nl_bodylog     — weight entries
nl_profile     — age/height/sex/activity/phase
nl_photos      — progress photos
nl_tempSwaps   — temporary exercise swaps
```

Week advance archives the current `logs` into `exhist[exId]`, increments `plan.week`, and clears `logs`. Cues stay on the exercise; session notes are saved into the history entry.

## Deploying

```bash
git add index.html icon.png
git commit -m "Update app"
git push
```

GitHub Pages re-deploys in ~60 seconds. Hard-refresh in Safari to bust the cache.

## Adding to home screen (iPhone)

1. Open the deployed site in Safari
2. Share → Add to Home Screen
3. The icon will be the sunset dumbbell
4. Launching from the home screen runs in standalone mode (no Safari chrome)

If the icon doesn't update after a deploy: long-press the home-screen icon → Remove App → re-add from Safari.

## Known things to watch

- iOS standalone mode handles the safe-area inset slightly differently from in-Safari. The CSS has a `@media (display-mode: standalone)` block that adjusts `.training-topbar` padding so the day strip doesn't hide behind the Dynamic Island.
- Storage is per-browser. Clearing Safari data or removing the home-screen app erases data — use Goals → Backup → Export to save a JSON dump first.
- The Claude review feature calls `https://api.anthropic.com/v1/messages` directly and assumes auth is handled by the artifact runtime. It won't work on a regular GitHub Pages deploy without an API key proxy.

## Stack

- Vanilla JS, no framework
- DM Sans + Inter from Google Fonts
- ZXing for barcode decoding (CDN)
- Open Food Facts v2 API for nutrition lookups
- Single file, single page, single source of truth
