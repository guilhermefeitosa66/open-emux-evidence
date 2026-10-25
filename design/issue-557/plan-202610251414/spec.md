# #557 — Tablet rail, shelf, grids, reading width (design spec)

Source: maintainer's Claude Design canvas "OpenEmux Mobile", section "Tablet" (v49), boards
TabEstante, TabEstanteClaro, TabEstanteRetrato, TabSobre (reading width only), TabSetup.
PNGs here are static renders of those boards (cartridge art replaced by placeholders, strings in pt-BR as drawn).

## Navigation rail (Medium/Expanded width)
- Width 80dp (Medium) / 88dp (Expanded); background Oe.Bg (raised tone), 1dp divider on the right.
- App logo (oe_logo) on top: 34dp (Medium) / 36dp (Expanded), 28-32dp top padding; tabs start 40dp below.
- Tabs top-aligned, 12dp apart; each item 56dp min height, 76-80dp wide: icon (22dp) inside a 56x32dp pill
  (radius 16) + label (12sp). Active: pill filled with the accent tint, icon AccentText, label Text weight 600.
  Inactive: no pill, icon + label Muted, weight 500.
- Phone side rail (Compact height) keeps today's look (centred tabs, no logo).

## Shelf, Expanded (1280x800) — 01, 02
- Content padding 32dp top, 40dp sides.
- Header row: "Shelf" (Display 36sp) + "N consoles · M games" under it; right side: search field
  (340dp x 48dp pill, placeholder "Search all consoles", opens the search page) and primary "Import ROMs" (48dp, download icon).
- "Continue playing" (17sp, 600) + "See recent" link on the right; row of up to 3 cards, 104dp tall,
  radius 20, Surface bg: cartridge 112dp, title (15sp 600, 1 line, ellipsis), "GBA · 2 h ago", 44dp accent play button.
  Hidden when nothing was played yet.
- "Consoles" (17sp) + hint "Long press to reorder" (13sp Muted) on the right.
- Grid: 3 columns, 16dp gap, card 168dp tall, radius 24; card content = the shelf card of #539
  (console icon, name + short tag, count, fanned cartridges). Last cell: dashed "Import more games" card (plus icon).
- Light theme + gamepad (02): focused card has the gamepadFocus ring (#538), hint bar at the bottom:
  A Open · B Back · SELECT Search · L1 R1 Switch tab; Left on the first column moves focus to the rail.

## Shelf, Medium (800x1280 portrait, 744x1133 8") — 03
- Rail 80dp; padding 40dp top, 32dp sides. Search collapses to a 48dp round button next to "Import ROMs".
- "Continue playing": up to 2 cards (100dp tall). Consoles: 2 columns, 14dp gap, 160dp tall cards, + "Import more games".
- "Recently added" + "See all": one row of 4 cartridges with titles, shown only when there is vertical room
  (window height Expanded, i.e. >= 900dp) and at least one game.

## Reading width — 04
- About, license list, license text, setup texts and empty states: the reading block is `widthIn(max = 640.dp)`,
  centred in its pane; paragraphs keep their own max (About lead 560dp). On phones nothing changes (< 640dp).
- The About list + detail arrangement in TabSobre belongs to #558; here only the width cap.

## Setup, Expanded — 05, 06
- Max content width 1040dp, centred; two columns 72dp apart: left = logo + "OpenEmux", title (40sp),
  lead (17sp, max 440dp), warning banner; right = progress card (big %, "x of y files", 10dp bar, current file)
  and the steps list card.
- Medium: one column, stacked, capped at 640dp, centred. Compact: today's layout.

## States
- Empty library on tablet: EmptyState centred, capped at 640dp (no continue row, no grid).
- Dark and light themes from the existing Oe palette; no new colours.
