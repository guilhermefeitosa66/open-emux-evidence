# Update banner (Android) — UI spec, issue #541

Components (Compose, existing app styling in ui/Theme.kt `Oe` / `Type`):

- **UpdateBanner** on `ShelfScreen` (ui/LibraryScreens.kt), first `item {}` right next to `ArtworkSyncPill`,
  same horizontal padding (20dp) and `AnimatedVisibility(expandVertically + fadeIn)` as the pill.
  Takes no space when there is nothing to show.
  - Container: `Oe.AccentTint` background, 1dp `Oe.AccentSoft` border, 16dp corners, 14x16dp padding, 12dp gap.
  - Row: 24dp download icon (stroke, `Oe.AccentText`, decorative) + column:
    title `Type.Section` "Version %1$s available"; subtitle `Type.Small` "You have %1$s".
  - Actions, end-aligned, 8dp gap, 44dp min height: text button "Not now" (`Oe.AccentText`),
    filled button "Download" (`Oe.Accent`, white `Type.Button`, 12dp corners).
  - Both buttons use `Modifier.gamepadFocus(...)`; D-pad Up from the first shelf card reaches the banner,
    Left/Right moves between actions, A activates, B leaves as usual. Initial focus stays on the first shelf card
    (the banner never steals focus).
  - Accessibility: container `semantics { heading / liveRegion = Polite }`, buttons have their text as label.
  - Light/dark: tokens already flip with `Oe.dark` (see frames 1 and 2). Contrast: title Text on AccentTint ≥ 12:1, AccentText on AccentTint ≥ 4.5:1 in both themes.
- **Settings › General**: `SwitchRow("Check for updates", "Ask GitHub for a newer version when the app opens")`, default on.
- **About screen** (ui/AboutScreens.kt): under the existing "Version %1$s" line, a status tag:
  "Up to date" (`Oe.OkBg`/`Oe.OkText`) after a successful check, or "Version %1$s available"
  (`Oe.AccentSoft`/`Oe.AccentOnSoft`, clickable, opens the release page). Hidden when the check is off, still running, or failed.
- Never shown in the game process / during gameplay (the banner lives only on the shelf).

Frames: update-banner-states.png (1 dark, 2 light, 3 gamepad focus, 4 switch + About status;
frame 4 groups the two for illustration, in the app they are in Settings › General and the About screen).
