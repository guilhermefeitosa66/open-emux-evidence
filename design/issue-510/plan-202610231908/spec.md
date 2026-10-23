# Artwork manager: "Current" tab (#510), UI spec

## Where
`ArtworkManagerWindow` (`src/openemux/ui/artwork_manager.py`). A new first tab, **Current**,
in the existing `Adw.ViewStack`, before Cover / Label / Import. The window always opens on it,
whether it was opened from "Manage cover" or "Manage label".

## Hierarchy
```
Adw.Window (existing, 980x720 default)
└ Adw.ToolbarView
  ├ top: Adw.HeaderBar, title widget Adw.ViewSwitcher (policy WIDE)
  ├ content: Adw.ViewStack
  │  └ "current" (icon emblem-photos-symbolic, title "Current")  ← NEW, first
  │     Gtk.ScrolledWindow (hscrollbar never)
  │     └ Adw.Clamp (maximum-size 760, margins 24 / 12 when narrow)
  │        └ Gtk.Box vertical, spacing 24
  │           ├ _ArtSection "Cover"  (always)
  │           └ _ArtSection "Label"  (only when label_supported)
  ├ bottom: Adw.ViewSwitcherBar (stack, revealed only when narrow)  ← NEW
  └ bottom: Gtk.ActionBar (existing Cancel | Save, Done)
```

`_ArtSection` (one per art kind):
```
Gtk.Box vertical, spacing 8
├ Gtk.Label  title "Cover"/"Label", css "heading", xalign 0
└ Gtk.Box horizontal (vertical when narrow), spacing 18, css "card", margins 18 inside
   ├ preview: Gtk.Overlay on a box with css "rom-cover-backdrop" (existing class),
   │  sized like the console's grid card (cover_size_for_console at the results zoom) for
   │  Cover, 200x150 box for Label; FixedSizePicture with ContentFit.CONTAIN, css "rom-cover"
   │  (missing: image-missing-symbolic 48px, dim, + "No cover yet")
   └ info: Gtk.Box vertical, spacing 14, hexpand
      ├ Gtk.Box horizontal spacing 8: source title (css "title-4"-ish bold 15px) + source pill
      │  (Gtk.Label css "artwork-source-pill" + "synced" | "manual" | "local" | "missing")
      ├ details Gtk.Label css "dim-label", wraps
      ├ actions Gtk.Box (horizontal, wraps via Gtk.FlowBox-free layout: Gtk.Box with
      │  homogeneous=False; vertical + full width when narrow)
      └ reset row (only when art exists AND source != synced): Gtk.Separator, then
         hint Gtk.Label (dim, wraps) + Gtk.Button flat, css "destructive-action" text colour
```

## States and exact text (EN; every string goes through i18n)

| State | Title | Pill | Details | Buttons |
|---|---|---|---|---|
| synced | Synced | Automatic | `PNG · 640 × 480 · Synced Oct 20, 2026` | Pick Another Match… · Replace From File… · Adjust… |
| manual | Chosen by you | Manual | `PNG · 512 × 731 · Saved Oct 21, 2026` | same + reset row |
| local (file with no record, or a record that no longer matches the file) | Local file | Local | `JPG · 600 × 840 · Found in the library folder` | same + reset row |
| missing cover | No cover yet | Missing | Sync it from the artwork mirror, search for one or import a file. | Sync Now · Search Covers… · Import From File… |
| missing label | No label yet | Missing | Without a label the cartridge shows its shell only. | Sync Now · Search Labels… · Import From File… |
| unreadable file | (source as above) | (as above) | The file could not be read | same as its source |
| syncing (reset / sync now running) | unchanged | unchanged | unchanged | the pressed button reads "Syncing…" and is insensitive; the other buttons of that section stay usable |

Reset row: hint "Go back to the cover the artwork sync finds for this game." / "Go back to the
label the artwork sync finds for this game."; button "Reset to Synced Cover" / "Reset to Synced Label".

Reset confirmation, `Adw.AlertDialog`:
- heading "Reset to the Synced Cover?" / "Reset to the Synced Label?"
- body "The cover you chose will be replaced by the one the artwork sync finds for this game. If nothing is found, the current cover stays." (label variant swaps cover → label)
- responses "Cancel" (default, close) and "Reset" (`Adw.ResponseAppearance.DESTRUCTIVE`).

After a reset that found nothing: toast "No synced artwork was found for {name}. The current one stays."

Footer: on the Current tab "Save" is insensitive (nothing pending) and "Done" closes without a
toast. On the other tabs both behave as today. A save on any tab refreshes the Current tab.

## Actions
- **Pick Another Match… / Search Covers… / Search Labels…** → switch to the Cover/Label tab;
  if it has no results and is not running, start the by-name search.
- **Replace From File… / Import From File…** → Import tab's file chooser, destination preset to
  this kind; on a chosen file the stack switches to Import with it loaded.
- **Adjust…** → the current file is loaded into the Import tab (crop / flip / rotate on a
  working copy, as today), destination preset to this kind, stack switches to Import.
- **Sync Now** (missing) / **Reset** (after confirmation) → the single-ROM sync for that kind
  (`replace_existing=True`), then the section re-reads its state.

## Adaptive
`Adw.Breakpoint` on the window, condition `max-width: 560sp`:
- header title widget hidden (window title shows), `Adw.ViewSwitcherBar` revealed at the bottom;
- section boxes vertical; preview centred; action buttons stacked, full width;
- Clamp margins 12.

## Accessibility
- Preview `Gtk.Picture` gets an accessible label: "Cover of {name}" / "Label of {name}"; the
  missing placeholder: "No cover yet"/"No label yet".
- Section card is a `Gtk.AccessibleRole.GROUP` labelled by its title.
- Focus order: section title skipped (not focusable), then buttons in reading order; on open
  the first button of the section matching the menu entry used ("Manage cover" / "Manage label")
  grabs focus.
- Pills carry text, never colour alone. Contrast checked in light and dark for the pill colours:
  success/accent/neutral backgrounds at 15-28% alpha with the matching `*_fg_color`.

## Styles (style.css, new)
`.artwork-source-pill` (12px, 2px 8px padding, radius 99px) and four variants using Adwaita
named colours: `.synced` success, `.manual` accent, `.local` and `.missing` neutral
(`alpha(currentColor, .1)`). Reuses `.card`, `.heading`, `.dim-label`, `.rom-cover`,
`.rom-cover-backdrop`, `.destructive-action`.
