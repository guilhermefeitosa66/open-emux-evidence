# #515 RetroAchievements on Android: UI spec

Built only from what `ui/Components.kt` and `ui/Theme.kt` already have (`Card`, `ListRow`,
`SectionHeader`, `BackLink`, `PrimaryButton`, `SecondaryButton`, `Tag`, `Oe.*`, `Type.*`).
New pieces: one switch row and one text field, both themed with `Oe` colours.

## 1. Settings: new section
- New `SectionHeader("Achievements")` between "Appearance" and "Consoles".
- One `Card` with one `ListRow`: leading 40dp rounded box (`Oe.AccentTint`) with a trophy stroke
  icon (`Icons.Trophy`, new path in `Icons`), label "RetroAchievements", subtitle "Not signed in" /
  "Signed in as <user>" / "Signed in as <user> · tracking off", chevron. Opens `Routes.ACHIEVEMENTS`.

## 2. Achievements screen, signed out (`AchievementsScreen`)
Column, `statusBarsPadding`, scrollable, 20dp side padding (same as `BiosScreen`).
- `BackLink("Settings")`, then `Type.Display` "RetroAchievements".
- Intro (`Type.Body`, `Oe.Muted`): "Sign in once and every game you play unlocks achievements on
  your RetroAchievements profile. Your password is only used to sign in and is never saved."
- Field "Username": single line, `KeyboardType.Ascii`, `ImeAction.Next`, autofill `Username`.
- Field "Password": `PasswordVisualTransformation`, eye toggle (44dp, contentDescription
  "Show password"/"Hide password"), `ImeAction.Done` = sign in, autofill `Password`.
  Fields: 52dp tall, radius 14dp, `Oe.Surface`, 1dp `Oe.Border` (`Oe.Danger` on error).
- Error box (only after a failure): `Oe.DangerBg`, text `Oe.Danger`, radius 14dp, alert icon,
  `liveRegion = Polite`. Text = the service's own message when it sent one, else
  "RetroAchievements could not be reached. Check your connection." or "Enter your username and password."
- `PrimaryButton("Sign in")`, 54dp, full width. While the request runs: label "Signing in…",
  `enabled = false`, fields read-only.
- Footer (`Type.Small`): "No account yet? Create one at retroachievements.org" (opens
  `https://retroachievements.org/createaccount.php` in the browser).

## 3. Achievements screen, signed in
- Account card (`Oe.Surface`, radius 20dp, 14dp padding): 48dp circle `Oe.AccentSoft` with the
  first letter (`Oe.AccentOnSoft`), "Signed in as" (`Type.Small`) over the username
  (`Type.Section` 16sp), trailing pill button "Sign out" (40dp, `Oe.Control`).
- `SectionHeader("While playing")` + `Card` with two switch rows (min 64dp, whole row toggles,
  `Modifier.toggleable(role = Role.Switch)`):
  - "Track achievements", subtitle "Unlock achievements in every game that has them". Default on
    right after signing in.
  - "Hardcore mode", subtitle "Counts for more points. Loading states and fast forward are turned
    off". Default **off**. Disabled (alpha 0.45) while tracking is off.
- Notes (`Type.Small`): "Changes apply the next time a game starts. Achievements show at the top of
  the screen as you unlock them." and "See your progress at retroachievements.org" (link).
- Sign out: immediate (no confirm dialog); clears username + token, the screen returns to state 2
  with the username field prefilled.

## 4. Pause sheet with hardcore on (`game/PauseSheet.kt`)
- Header subtitle gets a `Tag("HARDCORE", Oe.WarnBg, Oe.WarnText)` after "Playing for …".
- "Load" big button and the save-states list row: disabled look (`Oe.Surface2` / `Oe.Faint`),
  not focusable. "Save state" stays (RetroArch allows saving in hardcore).
- "Fast forward" tile at alpha 0.45, no action.
- Warn box under the tiles (`Oe.WarnBg`/`Oe.WarnText`): "Hardcore mode is on: loading a state and
  fast forward are off for this game. Turn it off in Settings › RetroAchievements."

## 5. In game
- RetroArch draws its own widgets (login, game summary, unlocks). Anchor top centre
  (`cheevos_appearance_anchor = "1"`, `cheevos_appearance_padding_auto = "true"`): in portrait the
  game picture sits at the top and the skin at the bottom; in landscape the shoulder buttons sit
  in the top corners, so top centre is the only spot clear in both.

## Themes, sizes, a11y
- Every colour comes from `Oe`, so dark and light follow `ThemePref` (images 3 and 4).
- Phone-only layout; in landscape the screen scrolls (same as `BiosScreen`). Gamepad: every row
  and button uses `gamepadFocus`; the hint bar reads "A Choose · B Back".
- Touch targets ≥ 44dp; switch rows announce as switches with their state.
