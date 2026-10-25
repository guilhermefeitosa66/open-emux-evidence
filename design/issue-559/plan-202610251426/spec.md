# #559 Tablet gameplay skin and pause side panel: design spec

Source: Claude Design canvas, section "Tablet" (v49), boards TabJogoGBA, TabJogoRetrato, TabJogoNDS, TabPausa.
Measurements are in dp on a 10" tablet (1280x800 landscape, 800x1280 portrait). The PNGs are static renders of those boards (the pause board's game image replaced by a gradient).

## Tablet skin, landscape (TabJogoGBA)
- Picture centred: 816x544 at (232, 92); side columns 232dp each.
- Strip directly under the picture, same width, 56dp tall: Save, Load, Turbo, 2x (equal slots). Turbo and 2x lit when on (#554 style).
- L / R: 200x52 pills, radius 14, 16dp from the top corners.
- Menu button: 64x44 pill under L (left column, y 88).
- Console name: small caps label centred in the right column (y ~102).
- D-pad: 184dp, left column, vertically centred (~y 348-532).
- A / B: 88dp circles, right column, diagonal (B lower-left, A upper-right), around y 384-540.
- Select / Start: 60x24 pills with labels under them, lower part of the left / right columns (y ~600).

## Tablet skin, portrait (TabJogoRetrato)
- Picture across the full width at the top (aspect-fit, 800x533 for GBA).
- Strip under it, full width, 56dp.
- Row under the strip: L (240x52) left, menu button centred, R right (24dp margins).
- D-pad 200dp lower-left; A / B 96dp lower-right; Select / Start centred below them; console name at the bottom.

## NDS, landscape (TabJogoNDS)
- Two screens side by side (desmume "side" layout), 412x309 each with a 16dp gap, centred; the bottom screen takes touch.
- Strip under both screens, spanning them.
- Layout button: a 44dp pill above the screens with the screens icon and the current layout name; cycles the layouts as the phone strip's screens key does.
- X / A / B / Y diamond of 64dp circles in the right column; L / R 188x52.

## Pause side panel (TabPausa)
- Panel 440dp wide on the right, full height, radius 28 on the left corners, shadow to the left.
- Header: cover, title, "Playing for N min", close button (44dp).
- Save state (accent) and Load: two 56dp buttons side by side.
- Action tiles in a 4-column grid (64dp tiles, label under): the tiles the phone sheet already has.
- Rows: Restart (and any other existing row of the phone sheet's main page). Quit pinned at the bottom (danger tint).
- Left area: the paused game stays visible (dimmed), a "PAUSED" chip, and a "Save states" row of up to 4 recent thumbnails with "Tap to load".
- Gamepad: focus ring on every control, d-pad moves within the panel; B back / resumes; START resumes.
