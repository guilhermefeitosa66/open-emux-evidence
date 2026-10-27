# 611 — Share glyph, labelled Share button, Receive under Import

## New icon: `openemux-share-symbolic`
- File: `src/openemux/ui/assets/icons/symbolic/openemux-share-symbolic.svg` (16×16, fill-only, `#2e3436` so GTK recolors it).
- Glyph = the Android app's `Icons.Share` (three nodes): dots r=2.25 at (4,8), (12,3.75), (12,12.25); two bars 1.5 px wide, round caps, (5.6,7.2)→(10.4,4.7) and (5.6,8.8)→(10.4,11.3). Bars drawn as filled outlines, not `stroke` (GTK's symbolic recoloring only touches fills reliably).
- Custom name on purpose: a host theme can never swap in a different "share" glyph.
- Used in: selection bar Share button, console sidebar menu "Share All Games Nearby…", game context menu "Share Nearby…" / "Share N Games Nearby…", the Receive dialog's "Ready to Receive" status page.
- `network-wireless-no-route-symbolic` (the "no network" status page) stays.

## Selection bar
`[3 games selected …] [Select All] [Sync Covers] [⟨share⟩ Share] [Delete] [×]`
- Share = `Gtk.Button` with `Adw.ButtonContent(icon_name="openemux-share-symbolic", label=t("share.selection.label"))`; tooltip stays `share.selection.tooltip` ("Share Nearby").
- Same style as its neighbours (no `flat`, no accent). Spacing 12 unchanged.
- Narrow (≤ ~560 px): the count label ellipsizes (`ellipsize=END`), buttons keep their labels.

## Header Import → `Adw.SplitButton`
- Main part: icon `folder-download-symbolic`, tooltip "Import ROMs", click = file picker (unchanged, zero extra clicks).
- Arrow part: `dropdown-tooltip` "More Import Options"; menu (`Gio.Menu`, one section):
  1. "Import ROMs…" → `win.import` (shows Ctrl+O accel automatically)
  2. "Receive Games…" → `win.receive-games`
- Ctrl+O unchanged.

## Primary menu
- The "Receive Games…" section is removed; the menu is app-only again (Preferences, Shortcuts, Welcome, Report a Bug, About).

## States
- Light / dark: icons are symbolic, follow the foreground. Popovers are the stock Adw popover.
- Narrow: header keeps the split button; sidebar collapses as today.
- Accessibility: split button main part has tooltip/accessible label "Import ROMs"; dropdown "More Import Options". Share button has visible text + tooltip.
