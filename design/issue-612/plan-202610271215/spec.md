# 612 — Receive games in the Import tab (spec)

## Import tab (`ImportScreen`, `ui/SettingsScreens.kt`)
- Action stack (Column, 20dp side padding, 10dp spacing), top to bottom:
  1. PrimaryButton "Choose files" (Icons.File, 54dp) — unchanged
  2. SecondaryButton "Choose a whole folder" (Icons.Folder, 54dp) — unchanged
  3. **new** SecondaryButton "Receive games" (Icons.Share, 54dp, fillMaxWidth) → `Routes.SHARE_RECEIVE`
- Same `else` branch as the other two: while an import is copying (spinner row), all three are hidden,
  so leaving the screen cannot cancel a running import.
- Gamepad: SecondaryButton already carries `gamepadFocus(corner = 16.dp)`; D-pad down from
  "Choose a whole folder" lands on it, A opens the receive screen. Hint bar unchanged (A Choose, B Back, L1 R1 Tabs).
- Portrait, landscape and tablet: the Import tab has a single column layout; the button follows it.
- Light/dark: Oe.Surface2 background, Oe.Text label and icon (existing tokens).

## Receive screen (`ui/share/ShareScreen.kt`, receive = true)
- BackLink label: "Import" (`settings_tab_import`) instead of "Settings"; B / back pops to the Import tab.

## Settings
- "Share Nearby" section keeps only the "Device name" row (now the first row: `divider = false`).
- The "Receive Games" row and its hint are removed.

## Strings
- Add `settings_import_receive` = "Receive games" (sentence case, like its siblings) in values + 7 locales.
- Remove `settings_share_receive`, `settings_share_receive_hint` from values + 7 locales.
