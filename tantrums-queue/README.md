# The Tantrums — Open Play Queue

Pickleball open-play queue app for The Tantrums club. Single self-contained page (`index.html`),
mobile-first, built in an earlier Claude session and published as a Claude artifact:
https://claude.ai/artifact/Q6MGtSkko6m5bHfbX5FQDk (shared with anyone who has the link).

`index.html` here is the live artifact's source as of version `1791713567-e07e`.

## What it does

**Tabs:** Courts · Queue · Log · Stats · Settings, plus a header **Undo** (last 12 actions).

- **Courts** — each court card shows both teams, a countdown timer (beep + vibrate at 0, then counts
  overtime), optional points boxes, and "Top won" / "Bottom won". A typed score overrides a mis-tapped
  winner. Tap a player's name to swap in someone waiting (the swapped-out player sits out and keeps
  their place). Empty courts have "Fill court".
- **Up next panel** — locks the next 4 (or next 2 challengers in winners-stay) for whichever court
  finishes first, then shows the rest of the line in order.
- **Queue** — add a player with level B / I / A; adding an existing roster name checks them in.
  Sit out / Back in, check out (✕, stays in roster). **Roster** below lists players not here today
  with Check in and Delete; typing in the name box filters it.
- **Log** — every finished game; edit points or switch the winner and stats recalculate.
- **Stats** — leaderboard sorted by wins, then point differential, then fewer games.
- **Settings** — number of courts (1–12), rotation mode (Paddle stack: all 4 off; Winners stay:
  max 2 wins in a row), balance skill levels, game timer minutes, export leaderboard as a branded
  1080px PNG (iPhone: press-and-hold → Save to Photos; "Save as file" elsewhere), TV screen,
  New session, Delete roster. Destructive buttons use tap-twice-to-confirm.
- **TV screen** (`#tv` on the link) — big read-only Now playing / Up next board, highlights a newly
  called court for 30s, live clock.

## Rotation logic (the `Q` module, pure functions)

- Fewest games first, then longest waiting; the front of the line always plays.
- Among near-ties, picks the group that has shared a court least (`met` counts) and, with balance on,
  closest in level. Team split avoids repeat partners (`mates`) and evens team levels.
- Newcomers and players returning from sitting out start level with the current minimum games, so
  nobody gets a catch-up streak.
- If the only 4 waiting are the same 4 who just played, the court is **held** ("Mixing") until another
  court finishes so groups mix ("Play anyway" overrides).

## Storage / sync

- Saved to `localStorage` (`tantrums-queue`) after every tap.
- Inside the Claude artifact it also syncs to the artifact database (`db` capability, doc
  `sessions/current`) so a second phone / the TV follows live; header shows SAVING… / SAVED ✓ /
  NOT SAVED. Undo history is trimmed if a save is too large.
- Artifact capabilities declared: `db`, `downloads`.
- Registers `sw.js` (service worker) if present; it is not included here.

## Branding

Red `#E63A2E`, orange `#FF8A1F`, cream `#FFF4E2`, ink `#151515`; rounded heavy display font;
club mascot embedded as base64 JPEG in the header.
