# Issue 546 — on-screen analog stick (spec)

Applies to every skin with `SkinCluster.Stick` (N64, GameCube, PlayStation, PSP, and the `standard(..., stick = true)` consoles). Units are the design's (390 wide in portrait).

## Anatomy
- **Well** (static, painted into the skin PNG): circle of radius `r = size/2`, colour `cl.color ?: colors.stick`, inner shadow ring as today. The label ("C") stays on the well.
- **Knob** (its own PNG, movable descriptor image): circle of radius `0.5 r`, colour `cl.knob ?: colors.knob`, drop shadow (blur ≤ 0.12 r, offset ≤ 0.08 r so it fits inside the image), centred on a transparent square of side `2 × 0.65 r` (the descriptor's rect, which RetroArch stretches the image to).
- **Travel**: knob centre moves up to `0.65 r` from the well centre (the engine clamps the offset to the descriptor's range). Full analog deflection is reached exactly at the end of travel (`saturate_pct = 1.0`), which matches today's saturation point (0.65 of the drawn radius).
- **Touch zone**: `1.3 r` around the well centre (`reach = 2.0` on a range of `0.65 r`), shrunk per direction so it never reaches a neighbouring control's touch rect or the game picture.
- **Held zone**: once the thumb holds the stick, the zone doubles (`range_mod = 2.0`) and the stick has priority over anything else under that thumb (`range_mod_exclusive`). Past the travel the value stays at full deflection in that direction.
- **Release**: knob snaps back to centre (engine behaviour for movable descriptors).

## Sizes
- PSP left stick: 72 → 96 (same as PlayStation). Others unchanged (N64 112, GC 112 + C 72, PS 96 + 96, standard 130).

## States
- At rest: knob centred.
- Partial push: knob offset proportional to the analog value.
- Full push / thumb beyond the rim: knob at end of travel along the thumb's direction.
- Hardcore / keypad / NDS overlays: unchanged.

## Accessibility
- No new strings, no new settings. The touch target grows (≥ 1.3 × the drawn well).
