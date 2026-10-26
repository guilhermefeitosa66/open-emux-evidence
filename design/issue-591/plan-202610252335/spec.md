# #591 Share nearby (Android) — UI spec

Design artifact: https://claude.ai/artifact/4eKuU1nA1X57fQ24QBWcH7 (11 artboards; colours are the app's `Oe` tokens, fonts `Type.*`).

Built only from what the app already has: `Card`, `ListRow`, `SwitchRow`-style rows, `PrimaryButton`, `SecondaryButton`, `BackLink`, `OeTextField`, `Tag`, `EmptyState`, `ModalBottomSheet(containerColor = Oe.Sheet)`, `CircularProgressIndicator`, `Modifier.gamepadFocus`, `GamepadButtons`/`HintBar`. Paths for new icons follow the `Icons` object (24-unit viewBox, stroke 2).

## New icons (`Icons` in `Components.kt`)
- `Share` — three nodes: circles r2.5 at (6,12) (18,6) (18,18), lines `M8.2 10.8l7.6-3.6M8.2 13.2l7.6 3.6`.
- `Desktop` — `rect 3,4 18x12 r2` + `M8 20h8M12 16v4`.
- `Phone` — `rect 6,2.5 12x19 r2.5` + `M11 18.5h2`.
- `Tablet` — `rect 4,3 16x18 r2.5` + `M11 18h2`.
- `WifiOff` — `M2 8.5a15 15 0 0 1 20 0M5.5 12a10 10 0 0 1 13 0M9 15.5a5 5 0 0 1 6 0` + dot (12,19) + `M3 3l18 18`.
- `Qr` — `M4 4h6v6H4zM14 4h6v6h-6zM4 14h6v6H4zM14 14h2M20 14v2M14 18h2v2M18 18h2v2`.
- `Copy` — `rect 8,8 12x12 r2` + `M16 8V5a1 1 0 0 0-1-1H5a1 1 0 0 0-1 1v10a1 1 0 0 0 1 1h3`.
- `Warning` — `M12 3l9.5 17h-19z M12 10v4M12 17v.5`; `Info` — circle r9 + `M12 7.5v5.5M12 16.5v.5`.
- Notification small icon: a monochrome vector drawable `ic_stat_share` (download-arrow `M12 4v12M7 11l5 5 5-5M5 20h14`).

## 01 Share screen (route `share`)
- `BackLink("Library")`, title `Type.Display` "Share Nearby", subtitle `Type.Small` "{count} games · {size}" (plural; "1 game · 3.1 MB").
- Card: one `ListRow` "What to Send", subtitle "{first title}, {second title} and {n} more · {files} files", chevron → page 02. Hidden in receive mode.
- Section header (`Type.SectionCaps`) "DEVICES ON THIS NETWORK" + an 18 dp `CircularProgressIndicator` while discovery runs.
- Card of peer rows (min height 64 dp): 40 dp tile (`Oe.AccentTint`, radius 12, platform icon in `Oe.AccentText`), name (`Type.Body`), subtitle "{Desktop|Phone|Tablet} · found by {paths}" where paths = "mDNS", "beacon", "address", joined "mDNS and beacon" (order mdns, beacon, manual). Tap = send to that device (→ 07 waiting). Incompatible peer: `enabled = false` (alpha `DISABLED_ALPHA`), subtitle "{Platform} · update OpenEmux on this device", second line "Version {peer}, this one speaks {ours}". Peers keyed by (id, fp) in a `LazyColumn` so focus survives updates.
- Section "THIS DEVICE": row "Visible as “{name}”" / "While this screen is open. Rename it in Settings." (not clickable); row "Connect by Address" with `Qr` leading icon → page 03.
- Receive mode (from Settings › Receive Games): title "Receive Games", subtitle "Ready to receive. Open Share Nearby on the other device." No What to Send row; peers still listed (read-only, no tap action, no chevron).
- When nothing is selected in What to Send, peer rows are disabled and a `Type.Small` line reads "Choose at least one item to send."

## 02 What to Send (route `share/items`; on a wide window the right column of 11)
- `BackLink("Share Nearby")`, title `Type.Title`.
- One `Card` per game: header row with a 22 dp tri-state checkbox (checked / mixed `—` / empty; `Oe.Accent` fill, `Oe.Chevron` 2 dp border when empty), title `Type.BodyMedium`, subtitle "{CONSOLE} · {k} of {n} items · {size}" (or "{n} items" when all), expand chevron. Expanded child rows (min 44 dp, 20 dp checkbox, divider `Oe.Divider`): Game, Battery Save, Save States ({n}) with "Only the ones the other device's core can load", Cover, Cartridge Label, Library Details with "Favorite, cartridge color, core, shader, play time". Only items the game has. Size on the right.
- Excluded game (the manifest builder left it out): unchecked, disabled, subtitle "{CONSOLE} · {reason}" (reason strings below).
- Bottom bar (`Oe.Sheet`, top border `Oe.Border`): total size `Type.Section`, "{g} games · {f} files" `Type.Small`, `PrimaryButton("Done")` → back.
- Gamepad: A toggles the focused row, X toggles the whole focused game, B back. Hints in `HintBar`.

## 03 Connect by Address (route `share/address`)
- Section "THIS DEVICE": card with the QR (white 12 dp padding frame, radius 12, 156 dp; `contentDescription` "QR code with this device's address"), `host:port` in monospace 20 sp, fingerprint in two lines of 4 groups of 4 (monospace 12 sp, `Oe.Muted`), `SecondaryButton("Copy Address", icon = Copy)` → clipboard + toast "Address copied", hint "Scan this with OpenEmux on the other device, or type the address there."
- Section "ANOTHER DEVICE": `OeTextField(label = "Address")`, keyboard `Uri`, IME action Go; error line in `Oe.Danger`; hint "Or point your camera app at the code on the other device's screen."; `PrimaryButton("Connect")` (spinner inside while checking).
- Errors: "That doesn't look like an address." (parse) · "Couldn't reach {address}. Check that it is on the same network." (connect) · "That device answered with a different identity. Check the address." (fingerprint mismatch) · "That device runs another version of OpenEmux (version {peer}, this one speaks {ours})."
- Success: back to 01 with the peer added ("found by address").

## 04 Nothing found / unavailable / warning
- After **15 s** with no compatible peer, the device card is replaced by: 64 dp tile with `WifiOff`, "No Devices Found" (`Type.Title`), "Open “Share Nearby” or “Receive Games” on the other device, on the same Wi-Fi."; a card with two notes: (Warning, `Oe.Warn`) "**Guest and public Wi-Fi** often keep devices apart. Turn on your phone's hotspot and connect the other device to it." and (Desktop) "**On a computer,** the firewall may block OpenEmux. Allow TCP 53319 and UDP 53318."; `SecondaryButton("Connect by Address")`; footer spinner "Still searching…". A peer appearing swaps back to the list.
- Unavailable (the share layer reports it), instead of every section: `EmptyState` with title "Share Nearby Isn't Available" and the reason: `tls13` "Share Nearby needs Android 10 or newer." · `keystore` "Couldn't create this device's key. Restart the app and try again." · no Wi-Fi/LAN "Connect to Wi-Fi to share games." (rechecked when connectivity changes).
- Multicast lock refused: warning banner (`Oe.WarnBg`/`Oe.WarnText`) above the list: "Wi-Fi didn't allow the network search. Some devices may not show up; use Connect by Address."

## 05 Incoming offer (over any screen of the main activity)
- `ModalBottomSheet` (`Oe.Sheet`, drag handle), **not dismissable by drag or outside tap**: `sheetState` refuses `Hidden`; system back = Decline.
- Title "Incoming Games" (`Type.Title`); lead "**{device}** wants to send you {n} games ({size})." (plural).
- Code card (`Oe.AccentTint`, radius 18): eyebrow "VERIFICATION CODE" (`Type.Eyebrow`), the code as "482 913" monospace bold 34 sp letter-spacing 6, "Accept only if {device} shows the same code."
- Card with one row per game: `ConsoleChip`, title, items summary "Game, save, 2 states, label · 3.1 MB".
- Notes: warning line "{n} save states skipped: made with a core this device doesn't have." (plural); "{title}: this device doesn't support {console}."; conflict button (`Oe.WarnBg`, `Oe.WarnText`) "Review {n} Conflicts" → 06.
- "Expires in 0:42" (`Type.Small`, centred), ticking every second; at 0 the sheet closes and a toast says "{device}'s offer expired."
- Buttons: `SecondaryButton("Decline")` (initial focus) and `PrimaryButton("Accept")`. Accept → service starts → sheet shows the progress state (07) and then the result (08).
- While a game runs (the `:game` process is in front) or the app is in the background: a high-priority notification "Incoming games from {device}" / "Tap to review. Code {code}." whose tap opens MainActivity with the sheet. No full-screen intent.

## 06 Already in Your Library (sheet page)
- `BackLink("Incoming Games")`, title "Already in Your Library", hint "Choose what happens to each one. A save is never replaced without asking."
- One card section per conflicting item: "{title} · {kind}", "Yours: {date}" with "· newer" in `Oe.OkText` on the newer side, "Incoming: {date}". A row of segmented chips (radio group, 36 dp, selected `Oe.AccentSoft`/`Oe.AccentOnSoft`): Keep Newest · Keep Both · Replace · Skip; art (cover/label) only Skip · Replace. Defaults from the receive plan.
- "SKIPPED" group: items the plan skips with the reason ("Made with {core} {version}, this device has {version}", "Already there", "Console not supported").
- Disk-space error (`Oe.DangerBg`, `role=alert`): "**Not enough space.** These games need {needed} and {free} is free. Free up {missing}, or uncheck some items on the other device." Accept disabled. Recomputed on every choice.

## 07 Waiting, progress, notifications
- Sender waiting (replaces 01's content, back = cancel with confirm): spinner, "Waiting for {device} to accept…", code (same style as 05), "The same code must show on {device}.", `SecondaryButton("Cancel")`. A "Preparing…" line with a progress bar while the manifest is built.
- Progress (both sides): "Sending to {device}" / "Receiving from {device}", `LinearProgressIndicator` (8 dp, `Oe.AccentBright` on `Oe.Divider`), "{done} of {total}" and "File {i} of {n}", current item "{title} · {kind}", note "You can turn off the screen or leave the app. The transfer keeps going.", danger button "Cancel Transfer" → confirm `AlertDialog` "Cancel the transfer?" / "Files received so far are discarded." / "Cancel Transfer" (danger) / "Keep Going".
- Foreground-service notification (channel "Transfers", low importance, ongoing, `setOnlyAlertOnce`): title as the progress heading, text "{n} games · {done} of {total}", determinate progress, action "Cancel". Updated at most twice a second. Removed when the service stops; a final non-ongoing notification "2 games received" / "Transfer failed" when the app is not in front.
- Incoming-offer notification (channel "Incoming games", high importance).

## 08 Results
- Success: 56 dp `Oe.OkBg` tile with Check, "{n} Games Received" / "From {device}. They're in your library, ready to play." / summary "{s} items skipped · {k} saves kept (yours was newer)"; buttons "Close" and "Show in Library" (opens the first received game's console).
- "{n} Games Sent" / "{device} has them now."
- Failure layout (`Oe.DangerBg` tile with Close): "Transfer Cancelled" / "Nothing was saved on {device}." · "Offer Declined" / "{device} declined the games." · "Offer Expired" / "{device} didn't answer in time." · "Transfer Failed" / reason from the error code · "Connection Lost" / "{device} stopped answering."

## 09 Light theme
Same screens with the light `Oe` values; nothing hard-coded.

## 10 Entry points + Settings
- Game detail "⋯" menu (`DropdownMenu`, gamepad Y): "Share Nearby" (Share icon) above "Remove from Library".
- Multi-selection bar (#585): a `RoundIconButton(Share, "Share {n} games nearby", background = Oe.AccentSoft, tint = Oe.AccentOnSoft)` before the remove button; gamepad **Select** shares the selection.
- Console sheet (long-press on a shelf): first row "Share All Games Nearby" with value "{n} games"; hidden when the console has no games.
- Settings › new section "SHARE NEARBY": `ListRow("Receive Games", subtitle "Let another device send you games")` → share screen in receive mode; `ListRow("Device Name", value = current name)` → bottom sheet with `OeTextField("Name other devices see")`, hint "Leave empty to use “{default}”.", Cancel / Save; max 64 characters, trimmed.

## 11 Wide window (`WindowLayout.wide`)
Two columns, 32 dp gap: devices + this device on the left, What to Send inline on the right (no separate page). Phone portrait: one column, What to Send as its own route.

## Accessibility
- Every icon-only button has a label; peer rows read "{name}, {platform}, found by {paths}"; the code is read digit by digit (`contentDescription` "Verification code 4 8 2 9 1 3").
- Touch targets ≥ 44 dp. Text contrast from the existing tokens.
- Gamepad: every interactive element uses `gamepadFocus`; initial focus: first peer (01), first game (02), address field (03), **Decline** (05); B = back / decline / cancel (with confirm while transferring).
