# Issue 508 — cover fit spec

Scope: the cover grid ("Covers" view mode) on a console page. Cartridge view and the
compact list are unchanged.

## Rule
- The card keeps the console's box-art shape (`CONSOLE_COVER_ASPECTS`), so the lattice stays even.
- Per cover, compare its aspect with the card's: `crop = 1 - min(a_img/a_card, a_card/a_img)`.
  - `crop <= 0.06` → **fill** (`Gtk.ContentFit.COVER`), edge to edge, as today. At most ~3% per edge
    is trimmed: logos and rating bands survive.
  - `crop > 0.06` → **fit** (`Gtk.ContentFit.CONTAIN`): the whole cover, centred, on the existing
    `.rom-cover-backdrop` box the size of the card. The picture's size request shrinks to the
    scaled art so `.rom-cover`'s 8px radius and shadow hug the art, not the empty bands.
- No cover → the placeholder, unchanged.

## Components
`Gtk.Overlay` (cover_overlay) → `Gtk.Box.rom-cover-backdrop` (card-size, 8px radius,
`alpha(@card_shade_color, 0.35)`) → `Gtk.Picture.rom-cover` (halign/valign CENTER). Play
overlay, favorite star, artwork badge and selection ring stay sized to the card box.

## States
- Default: fill (matching cover), fit (mismatched cover), placeholder (no cover).
- Light and dark: backdrop uses the theme's `@card_shade_color`, no new colors.
- Narrow window / zoom: geometry scales with zoom; the rule is ratio based, so it holds at
  every zoom level and column count.
- Error (undecodable file): placeholder, unchanged.

## Accessibility
No new strings, no new focusable widgets, keyboard focus and selection ring unchanged.
