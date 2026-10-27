# Issue 609: Android shelf card, design spec

Design artifact: https://claude.ai/artifact/6NNzJAiTMHHMKWpeRGt5kR

## Why the icon looks small today
Console icons are 32x32 px `drawable-nodpi` bitmaps; `ConsoleIcon` snaps to a whole multiple of the bitmap
so the pixel art stays crisp. On a 3x phone the regular card asks for 28dp (84 px) and gets 64 px (~21dp);
the compact card asks for 18dp (54 px) and gets 32 px (~11dp). Asking for 64dp gives 192 px = 6x, a full 64dp.

## Shared rules (both options)
- Card: same colors (`Console.shelf(dark)`, Favorites gradient), corner 22dp phone / 24dp tablet, focus ring and scale unchanged.
- Icon tile: `ShelfIconTile`, white at 16% (dark) / 62% (light), corner = 28% of the tile.
- Text column: tag (12sp caps, 72% ink), name (2 lines max then ellipsis), "N games" (13sp, 78% ink).
- No compact variant with the icon inline: the icon is never smaller than 64dp on a phone.

| | Phone portrait / landscape | Tablet |
|---|---|---|
| Option A card height | 104dp | 148dp |
| Option A tile / icon | 76dp / 64dp | 112dp / 96dp |
| Option A name | 20sp | 24sp |
| Option B card height | 124dp | 168dp |
| Option B tile / icon | 76dp / 64dp | 96dp / 64dp |
| Option B name | 18sp | 21sp |
| Option B fan | 70% of today's fan | 68% of today's fan |

## Option A: no cartridges
Icon tile at the start (16dp in), 16dp gap, text column fills the rest. Nothing at the end of the card.
Long names ("Nintendo (NES) / Famicom", "Sega Genesis / Mega Drive") fit on one or two lines in every layout.

## Option B: big icon plus a smaller fan
Same icon tile at the start; the fan of the three most recently played games keeps today's placements
scaled down and vertically centred; text ends 8dp before the fan. On tablet (three columns, ~350dp cards)
long names wrap to two lines and ellipsize ("Sega Genesis /…").

## Images
1. 01: phone portrait dark, today / A / B
2. 02: phone portrait light, A / B
3. 03, 04: phone landscape (two columns), A and B, dark and light
4. 05, 06: tablet landscape (three columns), A dark, B light
