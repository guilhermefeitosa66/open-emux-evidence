# #545 Back up and restore (Android) — UI spec

Built only from existing Compose pieces in `ui/Components.kt` / `ui/SettingsScreens.kt`:
`SectionHeader`, `Card`, `ListRow`, `BackLink`, `PrimaryButton`, `SecondaryButton`, the switch row
from #515, `Oe.*` colours, `Type.*` styles, `gamepadFocus`. Touch targets >= 44dp.

## 1. Settings → General
New `ListRow` between "Controls" and "About": title **Back up and restore**, subtitle
**Keep your saves and settings when you reinstall**, chevron; opens route `backup`.

## 2. Back up and restore screen (`BackupScreen`)
- `BackLink("Settings")`, `Type.Display` title **Back up and restore**.
- Intro: **Save everything OpenEmux keeps to one file you own, and bring it back on a new install or a new phone.**
- `SectionHeader("Export")`, a card with three rows:
  - **Saves and states** — "Saves, save states, playtime, screenshots" — size.
  - **Settings and artwork** — "Settings, favorites, labels, BIOS" — size.
  - **Include ROMs** — "<n> games · <size>" — switch, on by default (whole row toggles, `Role.Switch`).
  Sizes are computed off the main thread; while computing the value shows "…".
- `PrimaryButton("Export data · <total>")` → SAF `CreateDocument("application/zip")`, suggested
  name `OpenEmux backup <yyyy-MM-dd>.zip`.
- Note: **Cores and covers are downloaded again after a restore. Your RetroAchievements account is not saved: sign in again.**
  With ROMs off the note becomes: **Without ROMs, copy your games back with Import ROMs after the restore: saves and states find them by name.**
- `SectionHeader("Import")`, `SecondaryButton("Import data")` → SAF `OpenDocument` (`application/zip`, `application/octet-stream`).
- Note: **Replaces the library, saves and settings on this phone with the ones in the file.**
- Note (bold lead): **Uninstalling to update?** On Android 10 and newer, tick "Keep app data" in the uninstall dialog and reinstall the same app.

## 3. Progress and result
- Exporting/importing: the export card is replaced by one row **Exporting…** / **Importing…** with the
  current folder and "<done> of <total>", a linear bar (`Oe.AccentBright` on `Oe.Surface2`), and
  `SecondaryButton("Cancel")` (cancel deletes the partial document / staging, data untouched).
  Buttons disabled while running; the screen keeps the screen on (`FLAG_KEEP_SCREEN_ON`).
- Export done: `Oe.OkBg`/`Oe.OkText` box, live region polite: **Exported to <file name> · <size>**.
- Import done: **Restored <n> games, <m> saves and states. OpenEmux will restart now.** then restart after ~1.5 s.

## 4. Import confirmation and errors
- After the file is picked and its manifest read: a bottom sheet (same `ModalBottomSheet` style as the
  theme/language sheets): title **Replace your data?**, line **From OpenEmux <version>, made on <date> · <size>**,
  a card with "<n> games", "Saves and states", "Settings and artwork" and their sizes (rows absent from the
  archive are omitted), note **The library, saves and settings on this phone are replaced. OpenEmux restarts when it is done.**,
  `PrimaryButton("Replace and restore")`, `SecondaryButton("Cancel")`.
- Errors (`Oe.DangerBg`/`Oe.Danger` box, live region assertive), all ending with "Nothing was changed.":
  - **This file is not an OpenEmux backup, or it is damaged. Nothing was changed.**
  - **This backup was made by a newer OpenEmux. Update the app first. Nothing was changed.**
  - Free space (warn colours): **Not enough free space: the backup needs <x> and <y> is free. Nothing was changed.**
  - Export failure: **The backup could not be written. <reason>** and the partial document is deleted.

## Adaptive / themes / a11y
- Screen scrolls (landscape, side rail): same column width as the other Settings sub-screens.
- Light and dark from `Oe`; the switch has `contentDescription` "Include ROMs".
- Gamepad: A activates, B goes back, as on the other Settings screens (`GamepadButtons` hints "Choose"/"Back").
