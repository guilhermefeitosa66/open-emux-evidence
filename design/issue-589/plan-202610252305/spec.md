# #589 Share nearby — desktop UI spec

Mockups approximate libadwaita; implement with the real widgets below, default Adwaita spacing (no custom CSS unless noted).

## Share dialog (`ShareDialog`, `Adw.Dialog`, content 480×640, follows Adw.Dialog bottom-sheet on narrow windows)
- `Adw.ToolbarView` + `Adw.HeaderBar` inside an `Adw.NavigationView` (requires libadwaita ≥1.4; floor is 1.5).
- Title "Share Nearby", subtitle "{count} games · {size}" (`Adw.WindowTitle`). Receive mode: title "Receive Games", no subtitle.
- Root page, `Adw.PreferencesPage`:
  1. Group (no title): `Adw.ActionRow` "What to Send", subtitle = first titles joined + "· {n} files", `go-next-symbolic`, activatable → pushes **What to Send**. Hidden in receive mode.
  2. Group "Devices on This Network", header suffix `Adw.Spinner` (`Gtk.Spinner` fallback) while discovery runs. One `Adw.ActionRow` per peer: prefix icon by platform (`computer-symbolic` / `phone-symbolic` / `tablet-symbolic`, all in the bundled fallback set), title = name, subtitle "{Platform} · found by {paths}" ("mDNS", "beacon", "address", joined with " and "). Activates → send. Incompatible peer: insensitive, subtitle "Update OpenEmux on this device · version {peer}, this one speaks {ours}", `dialog-warning-symbolic` suffix. Hidden in receive mode.
  3. Group "This Device": row "Visible as “{name}”", subtitle "Others see this device while this window is open"; row "Connect by Address" → pushes **Connect by Address**.
- Empty state: after **15 s** with no compatible peer the devices group is replaced by an `Adw.StatusPage` (`network-wireless-no-route-symbolic`), title "No Devices Found", description "Open Share Nearby on the other device too, on the same Wi-Fi network.", bullet text: "Guest and public networks often keep devices from seeing each other. A phone hotspot usually works." / "A firewall on this computer can block incoming connections (TCP {tcp} and UDP {udp})." Pill button "Connect by Address…". Footer "Still searching" with spinner. A peer appearing swaps back.
- Unavailable (`availability()["share"] != ok`): `Adw.StatusPage` "Share Nearby Is Unavailable", "This copy of OpenEmux is missing a component it needs ({name})."; mDNS unavailable alone → no UI change except the paths shown.

## What to Send (pushed page)
- `Adw.PreferencesGroup` with one `Adw.ExpanderRow` per game: prefix `Gtk.CheckButton` (inconsistent state when partially checked), cover thumbnail 28×38, title, subtitle "{console} · {size}".
- Child rows `Adw.ActionRow` + prefix check: "Game" (file name · size; multi-file "{n} files"), "Battery Save" ("Saved {date}"), "Save States" ("{n} states · sent only if the other device has the same core"), "Cover", "Cartridge Label", "Library Details" ("Favorite, cartridge color, core and shader"). Rows for items the game doesn't have are not shown. All checked by default.
- Excluded games (from `build_manifest().excluded`): unchecked, insensitive, subtitle by reason: `unit_incomplete` "Can't be sent: a track file is outside the game's folder or missing", `unsupported_console` "Can't be sent: this console isn't supported for sharing", `too_many_items` "Can't be sent: too many files in one transfer".
- Bottom bar (`Adw.ToolbarView` bottom): "Total" / "{size} · {n} files" and a "Done" button (pops). Unchecking everything disables sending (device rows insensitive, tooltip "Choose something to send").

## Connect by Address (pushed page)
- Group "This Device", description "On the other device, scan the code or type this address.": QR (`Gtk.Picture` of a texture rendered from the `openemux-share://` URI, 168 px, white quiet zone, also in dark mode), "Address" `host:port` (monospace, selectable), "Fingerprint" grouped by 4 (first 16 groups shown, full in tooltip), "Copy Address" button (copies the full URI). No QR library → the picture is hidden, text stays.
- Group "Another Device", description "Type the address shown on the other device's Share Nearby screen.": `Adw.EntryRow` "Address" with an apply button "Connect"; error below in `error` style: "Couldn't reach {host}. Check the address and that Share Nearby is open there." / "That doesn't look like an address." / "That device answered with a different fingerprint." Success adds the peer (path "address") and returns to root.

## Waiting / progress (sender, pages replacing root, no back button)
- Waiting: spinner, "Waiting for {device} to accept…", code `482 913` (monospace, `title-1`), "The same code should appear on the other device.", "Cancel".
- Progress: header "Sending to {device}", current game title, `Gtk.ProgressBar` (fraction = done/total bytes), "{done} of {total}", "File {i} of {n}", optional "Skipped by the other device: {summary}", destructive "Cancel Transfer".
- Receiver progress: same page in the offer dialog, header "Receiving from {device}".

## Incoming offer (`IncomingOfferDialog`, `Adw.Dialog`, 480×720, presented over the active window — main window, Preferences or the share dialog)
- Header "Incoming Games". Lead: "**{device}** wants to send you {count} games ({size})."
- Code card (`.card`): "Verification code", code in `title-1` monospace, "Accept only if {device} shows the same code."
- Group "Games": row per game, subtitle "{console} · {items}", suffix size.
- Notes (warning style): "{n} save states were made with a core you don't have and will be skipped." ; unsupported console: "{title} is for a console this device doesn't support and will be skipped."; link row "Review {n} Conflicts" when conflicts exist → pushes the conflicts page.
- "Expires in m:ss" (1 s tick); at 0 the dialog closes with a toast "The offer from {device} expired".
- Bottom: "Decline" | "Accept" (suggested). Closing the dialog = Decline.

## Already in Your Library (conflicts page)
- Description "Choose what happens to each file you already have. A save is never replaced unless you choose so or the incoming one is newer."
- One `Adw.ComboRow` per conflicting item: title "{game} · {kind label}" ("Game", "Battery Save", "State, Slot {n}", "Auto State", "Cover", "Cartridge Label"), subtitle "Yours: {date} · Incoming: {date}" (+ " (newer)"), model "Keep Newest", "Keep Both", "Replace", "Skip"; default from #588's policies. Cover/label offer only "Skip"/"Replace".
- Group "Skipped": incompatible states "Made with {core} {version}, which you don't have".
- Disk space error (`.error` banner/`Adw.Banner` style box, `role=alert`): "**Not enough space.** These games need {needed} in {path}, and {free} is free. Free up {missing} or uncheck some games on the other device." Accept insensitive; recomputed when choices change.

## Done / failed (status pages)
- Received: `emblem-ok-symbolic`, "{n} Games Received", "From {device}. They're in your library now.", summary "{n} battery saves replaced by a newer one · {n} save states skipped", pill "Show in Library" (suggested; opens the console page of the first game).
- Sent: "{n} Games Sent", "{device} has them now."
- Failed: `dialog-error-symbolic`, titles "Transfer Cancelled" / "Transfer Failed" / "Offer Declined" / "Offer Expired" / "Can't Connect", body per error code (see plan), "Nothing was saved on this device." on the receiver, pill "Close".

## Entry points
- Game context menu: "Share Nearby…" after "Add to Collection"; with a multi-selection containing the game: "Share {count} Games Nearby…". Selection bar: button with `network-wireless-symbolic`, tooltip "Share Nearby".
- Console sidebar menu: "Share All Games Nearby…" (after "Sync Covers"), hidden for virtual scopes without games.
- Primary menu: "Receive Games…" first, then a section break.

## Preferences
- Page System, new group "Share Nearby", description "The name other devices see when you share games over the local network.", `Adw.EntryRow` "Device Name" with show-apply-button; empty = hostname (shown as placeholder text in the subtitle "Leave empty to use {hostname}").

## Accessibility
- All icon-only buttons get tooltips + accessible labels; the code is read as digits ("4 8 2 9 1 3") via accessible description; progress bar has an accessible label; focus lands on "Decline" in the offer dialog (safe default), on the first device row in the share dialog.
- Contrast: warning text uses Adwaita `warning` / `error` style classes, not raw colors.
