# #585 Android multi-select and bulk delete — UI spec

Design artifact: https://claude.ai/artifact/YcMg4UDoWpdnxDfenJbuDg

## Components (Compose, existing app look: `Oe` colors, `Type`, `Icons` in ui/Theme.kt + ui/Components.kt)
- SelectionBar (new, ui/LibraryScreens.kt): 56dp (52dp in landscape) row pinned to the top of the library screen,
  above the scrolling content, background `Oe.Surface`, 1dp bottom border `Oe.Border`, status-bar padding.
  - Left: 44dp round button, `Icons.Close`, content description "Cancel selection".
  - Title: "N selected" (`Type.Section` at 17sp), weight 1.
  - "Select all": pill text button 40dp tall, `Oe.Control` bg, `Oe.AccentText` label, `Type.Section` 14sp.
    Disabled (alpha 0.4, not focusable) when every visible ROM is already selected.
  - Remove: `RoundIconButton(Icons.Trash, "Remove selected", tint = Oe.Danger, background = Oe.DangerBg)`.
- Tile selection marker (grid Carts/Covers, landscape row): 24dp circle at top-left, 6dp inset.
  - Selected: `Oe.Accent` fill, white `Icons.Check` 16dp, 2dp `Oe.Bg` halo; tile gets a 3dp `Oe.AccentBright` ring
    (corner 10dp); caption in `Oe.Text`.
  - Unselected while selecting: 20dp hollow circle, 2dp white @85% border on black @35%; tile at alpha 0.78,
    caption `Oe.Muted`.
  - Outside selection mode: no marker at all (screen unchanged).
- List row: leading 24dp check circle (selected `Oe.Accent` + check; unselected 2dp `Oe.Chevron` outline) before the
  box art; selected row background `Oe.AccentSoft`, corner 12dp. Favorite star stays on the right.
- Confirmation: existing `AlertDialog` pattern from DetailScreen (ImmersiveWindow, `Type.Title` 20sp title, `Type.Small` body,
  TextButtons "Cancel" and "Remove" in `Oe.Danger`).

## Copy (EN)
- "%d selected" (plural `library_selected_count`)
- "Select all", "Cancel selection", "Remove selected"
- Dialog title (plural): "Remove %d game?" / "Remove %d games?"
- Dialog body (plural): one: "The ROM file is deleted from the device. Saves and states are kept in case you import it again."
  other: "The ROM files are deleted from the device. Saves and states are kept in case you import them again."
- Buttons: "Cancel", "Remove" (reuse existing strings if present)
- Gamepad hints while selecting: "A Select", "X Select all", "Y Remove", "B Done"; normal mode adds "Start Select".

## States
- Default (not selecting): unchanged screen + "Start  Select" hint in gamepad mode.
- Selecting, N >= 1: bar replaces nothing, overlays the top; the header (back link, search, sort) is hidden under it.
- Last ROM deselected: selection mode ends, bar goes away.
- After remove: dialog closes, selection ends, tiles disappear; if the filter becomes empty the existing empty text shows.
- Narrow/landscape: same bar at 52dp; landscape row keeps its layout.
- Light and dark: tokens above (`Oe` picks both).

## Accessibility
- Tiles in selection mode expose `Role.Checkbox` + `toggleableState`/`selected` semantics and onClickLabel "Select"/"Deselect".
- Icon buttons carry content descriptions above; all targets >= 44dp.
- Selection is never conveyed by color only: check mark + ring.
- Gamepad focus ring (gamepadFocus) stays and sits outside the selection ring.
