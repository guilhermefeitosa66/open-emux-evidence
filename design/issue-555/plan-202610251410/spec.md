# Issue #555 — On-screen D-pad: drag like a touch stick (spec)

All lengths in the D-pad's own 120-unit grid (the one `SkinRenderer.dpad()` already draws in: `u = size / 120`).
The skin's design units are 390 wide in portrait; D-pad sizes per skin stay as they are (84–150).

## Skin image (painted, static)
- Cross arms, rounded ends and the four arrow triangles: unchanged (`colors.dpad`, `colors.mark`).
- The centre hub (`colors.hub`, r 9) is **no longer painted**: the resting circle takes its place.

## Floating circle (movable descriptor image)
- Circle radius **14**, fill `colors.mark` at 70 % opacity, 2-unit stroke of `colors.mark` (opaque),
  soft shadow (offset 1.5 down, ~25 % black), drawn centred on a transparent square PNG.
- PNG side = descriptor rect = **2 × 44 = 88** units (the engine stretches a descriptor image to its rect).
- Travel: the circle's centre follows the thumb, clamped to ±44 on each axis (engine clamps to the range),
  so its edge stops at 58 — the arm tips. Diagonals clamp to the square corner (inside the D-pad's box).
- At rest / on release: back at the centre (engine clears movable deltas on release). It stays visible
  (a "resting" hub), it does not disappear.

## Touch
- Start zone: square, half-side **67.2** (1.12 × 60 — the same touch area the nine cells cover today).
- Once a finger holds it, the zone grows to the whole screen and wins over every other control for that
  finger (`range_mod` + `range_mod_exclusive`); other fingers are unaffected.
- Direction: 8 ways from the angle between the thumb and the D-pad centre; distance does not matter.
  Diagonal sectors from the engine's default diagonal sensitivity (80): cardinals ≈ 50°, diagonals ≈ 40°.
- Dead zone: circle of radius **11** (0.25 × 44) around the centre gives no direction.
- Output: digital, as today (`up`/`down`/`left`/`right`, diagonals = two directions).

## States (see dpad-drag-states.png)
1. At rest — circle centred, nothing pressed.
2. Up-right inside — circle under the thumb, UP + RIGHT.
3. Thumb past the right edge — circle clamped, RIGHT keeps steering.
4. Thumb far up-left, off the pad — circle at the corner, UP + LEFT.
5. Light skin, thumb inside the dead zone — no direction.

## No new strings, settings or screens. Accessibility unchanged (game overlay).
Portrait, landscape, keypad overlays, compact panel and NDS layouts all use the same D-pad drawing.
