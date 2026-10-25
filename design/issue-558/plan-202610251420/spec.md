# #558 — Tablet list + detail: Settings, game list, game detail (design spec)

Source: maintainer's Claude Design canvas "OpenEmux Mobile", section "Tablet" (v49), boards
TabAjustes, TabSobre, TabRoms, TabRoms8, TabDetalhe. PNGs are static renders of those boards
(cartridge/state art replaced by placeholders, logo replaced by a square, strings in pt-BR as drawn).
Rail, colours and tokens are #557's; this spec only adds the panes.

## Settings, list + detail (Medium/Expanded width, not short) — 01, 02, 03, 04
- Layout: rail | list pane | detail pane. List pane 400dp fixed on Expanded, 1px `Oe.Surface2` divider
  on its right; on Medium (600-840dp) the list pane is 320dp. Detail pane = the rest.
- List pane: padding 28dp top, 20dp sides. Title "Settings" (32sp Display). BIOS banner card (radius 18,
  44dp amber icon tile, "BIOS" + "N consoles need files", chevron) — same content as the phone banner.
  Section heading "CONSOLES" (13sp, 600, tracking .08em, Muted), grouped card (radius 18, `Oe.Surface`)
  of console rows 58dp: 38dp colour chip with the short tag, name 15sp, "<core> · <shader>" 13sp Muted,
  amber "BIOS" badge when the console misses a required BIOS. Heading "GENERAL": rows 48dp
  (Language, Theme, ROM folder — label left, value right, Muted) and "About and licenses".
- Selected row: background accent tint (`#2D2346` dark; the light equivalent token), label weight 600.
  Selection is shown, not a separate focus ring; the gamepad ring (#538) draws on top when focused.
- Detail pane: padding 36dp top, 48dp sides; content `widthIn(max = 640.dp)`, start-aligned.
  - Console defaults (01): overline "CONSOLE DEFAULTS" (13sp accent), title = console name (30sp),
    "Applies to every game of this console that has no setting of its own." Cartridge art 120dp at the top
    right when the console has one. "Default core" + "Download others" link; cores as 2-column radio cards
    ("RECOMMENDED" badge); "Default shader" as a 5-column thumbnail picker; a grouped card with BIOS
    (file + state) and "Default skin" rows. = today's console-settings screen content, re-arranged.
  - Theme (02): title "Theme", one-line explanation, 3 theme cards in a row (System / Light / Dark).
  - Language: title, explanation, radio list. BIOS (03): title, explanation, "Choose folder" primary
    button, list of BIOS files with state.
  - About and licenses (04): logo 64dp + "OpenEmux" (30sp) + "Version x · Android", lead paragraph
    (max 560dp), pill buttons (Source code, Report a problem, Privacy), "Open source licenses" + hint
    "Tap to read the full text", licenses in a 2-column grid of 56dp cards; tapping one replaces the
    detail pane content with the license text (Back returns to About in the pane).
- Empty detail (nothing selected yet): the first console is pre-selected on open (board 01), so the
  pane is never empty. If there are no consoles, Theme is pre-selected.

## Game list with detail pane (Expanded width) — 05
- Layout: rail | list (weight 1, padding 20dp top / 28dp sides) | detail pane 440dp fixed
  (`Oe.Surface` darker tone `#17171C`, 1px left divider, radial accent glow behind the cartridge).
- List header: back link "< Shelf", title (32sp) "All games", "84 games in 4 consoles", round Search and
  Sort buttons (44dp); filter chips row (All, Favourites, one per console) + view switcher
  (Carts / Covers / List, 44x34 segments).
- Groups by console: header row (36x28 chip, name 17sp, "31 games · mGBA", "Open shelf" link right),
  1px divider, then a 4-column grid (8dp gap) of cartridges with the title under (13sp, ellipsis).
  Selected cartridge: `#231D36` background + 2dp inset accent ring, radius 16.
- Detail pane: top-right round buttons 44dp: "Open full screen" (expand icon -> full detail route),
  favourite star, more (overflow menu = the phone detail menu). Cartridge 236dp centred, title 23sp,
  "<console> · <core> · <time played>", primary "Continue" (52dp, weight 1) + "Restart" (120dp),
  "Saved states" + "See all (N)" -> full detail, a row of the 3 newest states (62dp thumbs, tag + age),
  grouped card of per-game rows (Core [DEFAULT badge], Controller skin, Shader, Cheats) -> each opens the
  existing per-game picker.

## Game list, 8" table style (Medium width, Expanded height) — 06
- No side pane (744dp is too narrow). Same header/chips/switcher; List view shows a table:
  column header (GAME / TIME / LAST PLAYED, 12sp, uppercase, Muted, hidden from accessibility),
  group header per console, rows 64dp: 76dp cartridge, title 15sp + file name 12sp Muted, time played
  (96dp column), last played (80dp column), favourite star (24dp). Tap -> full detail route (07).

## Game detail, two columns (Medium/Expanded width, not short) — 07
- Left column 460dp: back link with the console name, cartridge 400dp with a floor shadow, then the
  "Cartridge colour" card (9 swatches, 44dp touch targets, ring on the selected one).
- Right column `widthIn(max = 560.dp)`, 44dp top padding: "GBA · mGBA" overline, title 34sp,
  "6 h 40 min played · last played 2 h ago", favourite + more buttons; "Continue" (56dp) + "Restart"
  (160dp); "Saved states" + "See all (N)"; adaptive grid of state cells (4 per row here, 78dp thumbs,
  no fixed 420dp height); "Settings for this game" card (Core, Controller skin, Shader, Cheats);
  footer "file.gba · 8 MB · Games/ROMs/GBA" (13sp Muted).
- Gap between columns 48dp; content centred with 48dp side padding.

## Gamepad
- Right from the rightmost list item (or any list item, Settings) moves focus into the detail pane's
  first focusable; B, or Left from the detail pane's first column, returns focus to the list item that
  opened it. L1/R1 keep switching console/tab as today. Hint bar unchanged.

## Light theme
- Same layout; tokens from Theme.kt light palette (selected row = light accent tint, panes = light
  Surface/Bg). No separate board: verified on the emulator.
