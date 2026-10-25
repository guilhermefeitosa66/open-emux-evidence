# Gamepad mode — design spec (issue #538, Android)

All values in dp. Colors are the existing `Oe` tokens (`ui/Theme.kt`), so both themes follow.

## Focus styles (`ui/Gamepad.kt`)
| Style | Used on | Look |
|---|---|---|
| Outside ring (default) | artwork tiles, shelf cards, buttons, chips, swatches, sheet tiles | ring 3 dp `Oe.AccentText`, 3 dp outside the item; halo 6 dp `Oe.AccentBright` at 28 %; focused item drawn above its neighbours (`zIndex`) |
| Inside highlight (`inset`) | rows inside a grouped card (settings, BIOS, search results) | `Oe.Raised` background + 2.5 dp ring inside the row; never scaled |

Scale-up: 1.05 on cartridge/cover tiles (grid and landscape row), 1.02 on shelf cards, none elsewhere.
Reach past the item: `FocusOutset = 12 dp` (3 offset + 3 ring + 3 halo, rounded up) plus the scale growth `(scale - 1) * size / 2`.
Captions in horizontal rows: focused item's title `Oe.Text` SemiBold, the others `Oe.Muted` (today all are `Oe.Text`).

## Scrolling
- Horizontal ROM rows (landscape library): focused item centered in the row. The row's content padding is the stop, so the first/last items rest at the padding, never with empty space before the first.
- Vertical lists and grids (shelf, portrait grid, search, settings, BIOS, detail, pause sheet grids): no centering; the focused item is kept inside a band 24 dp from the top and 24 dp above the hint bar.
- Chip / swatch / state rows (`horizontalScroll`): padding inside the scroll (content padding), min 20 dp, so the first/last ring is never cut; same margin bring-into-view as above, horizontally.

## Hint bar
- Height 40 dp everywhere (today 36 dp in the tab-bar layout, 40 dp elsewhere).
- Badge 22 dp high, radius 11; `A` filled `Oe.AccentBright` with white text, the rest `Oe.Raised` with `Oe.Text`; multi-letter badges (`L1 R1`, `SELECT`, `START`) are pills with 7 dp side padding.
- Order: A, then X / Y, then B, then shoulder and menu buttons.
- While shown, vertical scroll containers add its height to their bottom content padding.

## Accessibility
- Ring contrast ≥ 3:1 against `Oe.Bg` and `Oe.Surface` in both themes (AccentText #B79CFF on #121216; #6D28D9 on #F6F5F9).
- No new strings; hint labels already exist.
- Touch: the highlight hides on the first touch (`InputMode.gamepad = false`), as today.

## Adaptive
- Phone portrait (bottom tab bar), phone landscape (80 dp rail): hint bar above the tab bar / beside the rail, as today.
