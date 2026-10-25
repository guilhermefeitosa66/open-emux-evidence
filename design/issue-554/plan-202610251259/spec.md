# #554 quick-action strip: toggle states (spec)

Surface: the skin's quick-action strip (save, load, turbo, fast forward [, NDS screens]),
drawn by SkinRenderer.strip() into the RetroArch overlay. Not Compose.

## States per toggle (turbo, fast forward)
- Off: exactly today's look (icon in `colors.icon` on `colors.strip`).
- On: an "active pill" behind the icon, icon redrawn in white on top.
- Hardcore (fast forward only): today's 45% icon, no touch area, never the pill.
- NDS screen layout: cycles, no on state, unchanged.

## Active pill (design units, 1 unit = portraitW/390 px)
- 46 x 30, corner radius 10, centred on the slot's icon centre; clamp width to slot width - 8.
- Fill #7C3AED (app accent, Oe.Accent). Inner ring 1.5 units #D4C4FF (Oe.AccentOnSoft dark).
- Icon: same path, size and stroke as the off icon, colour #FFFFFF.
- Contrast: ring vs every strip colour >= 3:1 (darkest #0B0B0D, lightest #4A1014/#3A2160);
  white icon on #7C3AED = 5.7:1.
- Same pill in portrait (bottom/top strip) and landscape (side strip), on the opaque and the
  see-through (0xCC alpha) strip.

## Behaviour
- Pill follows the engine state, whatever toggled it: strip, pause sheet, gamepad hotkey.
- New session: both off. Survives pause/resume, rotation, background/foreground.
- Hidden with the rest of the overlay when a gamepad is connected.
- Accessibility: the overlay has no a11y tree; the pause sheet keeps the labelled toggle tile
  ("Fast forward", active state) as the accessible path.
