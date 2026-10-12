# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What's here

No build system, package manager, linter or tests. Every app is a single self-contained HTML file
(inline CSS + JS, CDN scripts only). To try one, open it in a browser or serve the folder
(`python3 -m http.server`) and load the page; use Playwright/Chromium for screenshots.

Two unrelated projects share the repo:

### `tantrums-queue/index.html` — 4stakk (pickleball open-play queue)

Mobile-first queue app for The Tantrums club; site name **4stakk**, club branding stays The Tantrums.
`tantrums-queue/README.md` describes features in detail. It is also published as a Claude artifact
(https://claude.ai/artifact/Q6MGtSkko6m5bHfbX5FQDk); the repo copy can be ahead of it, so republish
or re-upload to the standalone site after changes.

Architecture inside the one `<script type="module">`:
- **`Q` module (IIFE)** — pure rotation logic, no DOM. Every mutating action calls `snap(s)` first
  (undo history, last 12) and mutates the state object it is given. Fairness rests on per-player
  `prio` (games counted), plus `met` / `mates` pair counters used by `pick()` and `split()` to avoid
  repeat groups and partners. `settle()` recomputes the locked "Up next" group (`s.next`) and must
  run after every action.
- **UI layer** — `act(fn)` = run action → `Q.settle(s)` → `save()` → `render()`. All UI changes go
  through it. `render()` rebuilds `#app` from `views[tab]()` template strings (or `tvView()` when
  the hash is `#tv`); events are handled by delegated `click`/`change`/`input`/`submit` listeners
  switching on `data-act`. Escape user text with `esc()`.
- **Persistence** — `localStorage` key `tantrums-queue` (keep it: renaming loses users' saved
  sessions). When running as a Claude artifact, `window.claude.use("db")` syncs the whole state as
  one doc `sessions/current` so other phones/TV follow live; standalone, that branch is a no-op.
  `downloads` capability is used only for "Save as file" of the leaderboard PNG.
- Destructive buttons use `confirmTap` (two taps) because artifacts block `confirm()`.
- Brand tokens are CSS vars on `:root` (red `#E63A2E`, orange `#FF8A1F`, cream `#FFF4E2`, ink
  `#151515`); the leaderboard PNG (`statsImage()`) repeats them in canvas code, so change both.
- The page registers `sw.js`, which does not exist in the repo (the failure is caught).

### `vid.html` / `video-generator.html` — "Dailies — AI Video Generator"

Two near-identical versions of the same page. Uploaded video files are stored in Supabase
(supabase-js v2 from jsDelivr): files in a Storage bucket, rows in table `video_history`
(`price`, `storage_path`, `added_at`, …), with a running total cost and a history tab.
Differences: `vid.html` adds a history sort dropdown and uses the base project URL with the legacy
anon JWT; `video-generator.html` sets `SUPABASE_URL` to `.../rest/v1/` (supabase-js expects the base
URL, so that version's client calls are likely broken) with a publishable key. Confirm with the
user which file is current before editing; keep changes mirrored or consolidate.
