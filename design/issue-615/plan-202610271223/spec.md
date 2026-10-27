# Issue 615 — Share nearby (Android): skipped games, UI spec

Existing components only (`ui/share/TransferViews.kt`, `IncomingOfferSheet.kt`, `ShareText.kt`, `Theme.kt` `Oe`/`Type`). No new widgets, no new colors.

## 1. Conflicts page ("Already in Your Library"), multi-file ROM unit
- The conflict card of a cue/m3u (or any `part`-carrying) ROM item shows only two `PolicyChip`s: **Replace · Skip**. Default selected: **Skip**.
- Under the chips, one warn line (`NoteLine`-like row, `Icons.Warn`, `Oe.WarnText`, `Type.Small`):
  "A game with several files can’t be kept twice. Replace yours or skip it."
- Saves/states keep the four chips; art keeps "Skip · Replace" (unchanged).
- Only one conflict row per unit: the main ROM item (cue/m3u). The track/bin parts follow it and are not listed as separate conflicts.

## 2. Result card (`ResultView`) — RECEIVED
| State | Badge | Title | Body |
|---|---|---|---|
| all delivered | `Oe.OkBg` + check (unchanged) | "N Games Received" | unchanged |
| partial | `Oe.WarnBg` + `Icons.Warn` in `Oe.Warn` | "{received} of {total} Games Received" | "From {peer}. These weren’t added:" |
| none | `Oe.WarnBg` + warn | "No Games Received" | "From {peer}. Nothing new was added to your library:" |
- Below the body, when something is missing: caps section "NOT RECEIVED" (`Type.SectionCaps`), then a `Card` of `ListRow(title = game title, subtitle = "<console short> · <reason>", subtitleLines = 2)`.
- The "N saves kept (yours was newer)" line stays; the "N items skipped" line goes (replaced by the list).
- "Show in Library" only when at least one game was delivered (unchanged rule).

## 3. Result card — SENT (Android is the sender)
| State | Title | Body |
|---|---|---|
| all | "N Games Sent" (unchanged) | "{peer} has them now." |
| partial | "{sent} of {total} Games Sent" | "{peer} has {sent} of them. These didn’t go:" |
| none | "No Games Sent" | "{peer} didn’t take any of them:" |
- Section "NOT SENT", same rows. Sender reasons: "Already on {peer}", "Not accepted on {peer}", "{peer} can’t place it", "{peer} doesn’t support this console"; unit_incomplete / too_many_items / invalid reuse the `share_reason_*` strings.

## 4. Notification (`ShareNotifications.finished`)
- RECEIVED: "N games received" → when M > 0 skipped games: "N games received, M skipped". SENT: "N games sent, M skipped". No skipped: unchanged.

## Layout, scroll, accessibility
- The result card lives in the existing scrollable sheet; a long list scrolls, buttons stay reachable; gamepad focus on Close first (unchanged).
- `liveRegion = Polite` stays on the column, so the title is announced. Badge icon is decorative (the title carries the meaning).
- Titles from the peer are plain `Text` (no markup in Compose); keep `maxLines = 2` on rows.
- Wide window (tablet two-column layout): same card, no change.
