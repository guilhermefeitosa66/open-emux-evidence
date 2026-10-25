# Issue #561: tabletop posture in game: design spec

Source: the maintainer's "Foldables" boards (Claude Design canvas v51): DobFoldJogo (Fold half open, GBA),
DobFoldNDS (Fold half open, NDS), DobFoldPausa (pause menu, Fold half open), DobFlipJogo (Flip half open, GBA).
The renders are static exports (pt-BR strings as drawn, artwork as placeholders). The dashed yellow line and its
label are design annotations: the app never draws them.

## Forbidden band
- Band = horizontal FoldingFeature bounds (window px) + 12dp above and below, clamped to the window (#560 `forbiddenBand`).
- In the skin, the band is background only: the skin body colour, darkened slightly (board: #3E3479 on a #4F4296 body).
  It has no desc, no label, and the picture viewport stays out of it.
- Top half = 0..band.start, bottom half = band.end..height.

## Fold half open, GBA (DobFoldJogo, 840x900dp, band 438..462)
- Top half: picture aspect-fit, centred, with about 19dp above and below (600x400 for 3:2).
- Bottom half, wide variant (bottom-half width >= 600dp):
  - Row 1, 16dp below the band: L (92x52, r14) on the left, the strip (save, load, turbo, 2x; 52 tall, r14) in the
    middle, R on the right, all with 16dp side margins.
  - Menu pill (64x44) centred under the strip.
  - D-pad (196) on the left; B and A (92 each, diagonal) on the right; SELECT and START centred near the bottom;
    console name at the very bottom (11sp, tracking .16em, 75% opacity).

## Flip half open, GBA (DobFlipJogo, 412x915dp, band 445..469)
- Top half: picture across the full width, centred vertically; console name under the picture.
- Bottom half, compact variant (bottom-half width < 600dp):
  - Strip at full width (12dp margins, 48 tall, r12).
  - Row: L (108x44) | menu pill (56x44) | R.
  - D-pad (156) on the left; B and A (76) on the right; SELECT and START centred at the bottom.

## Fold half open, NDS (DobFoldNDS)
- Top screen in the top half, bottom (touch) screen in the bottom half, centred between the d-pad (left) and
  the XYAB diamond (right).
- L and the menu pill at the top left of the bottom half; R at the top right with "NINTENDO DS" under it.
- Strip under the bottom screen, 5 slots: save, load, turbo, 2x, and the layout key, whose label is the current
  layout. The layouts in tabletop are "One per half", "Top screen only" and "Bottom screen only".
- SELECT at the bottom left, START at the bottom right.
- Deviation, see the plan: the board draws the bottom screen smaller than the top one. One frame with a gap
  gives two screens of the same size, so both use the size that fits the bottom half.

## Pause in tabletop (DobFoldPausa)
- Top half: the game stays visible, dimmed, with a "PAUSED" chip and a card (cart, title, "<console> · <time played>")
  centred. Tapping it resumes the game. Nothing can be focused there.
- Band: background only.
- Bottom half panel (Oe surface, padding 18/28):
  - Row: "Save state" (accent, first focus, weight 1) | "Load state" (weight 1) | "Back to game" (168dp). All 58 tall, r16.
  - Tile row, 64dp tiles r18 with the label under each: the actions the phone sheet has today. Toggles are lit
    while on (accent tint + 1.5dp ring).
  - Row: "Saved states" with the count and a chevron | "Restart game" | "Quit game" (danger tint, 168dp). All 50 tall.
  - HintBar pinned at the bottom (48dp): A Select · B Back to game · START Close menu.
  - The States and Shader pages open inside the bottom half. The bottom half scrolls on its own if needed.
- Gamepad focus ring: 2.5dp accent outline at a 3dp offset, plus a 7dp glow.
- The dark app theme is drawn. The light theme uses the same layout with the light Oe tokens.
