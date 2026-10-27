# 614 — Share result pages list the games not delivered

Widgets: the existing `Adw.StatusPage` from `share_pages.result_page`. Its child becomes a
vertical `Gtk.Box` (spacing 24): an `Adw.PreferencesGroup` (only when something is missing) with one
`Adw.ActionRow` per game, then the existing button box. Rows: title = game title, subtitle =
"<console display name> · <reason>", `use_markup` False, not activatable, not focusable beyond the
default row behaviour. The status page scrolls itself, so a long list never clips at 360 px.

| State | Icon | Title (EN) | Description |
|---|---|---|---|
| all arrived | emblem-ok-symbolic | "1 Game Sent" / "{count} Games Sent" (unchanged) | "{device} has them now." |
| some missing | dialog-warning-symbolic | "{sent} of {total} Games Sent" | "{device} has {sent} of them. These didn't go:" |
| none arrived | dialog-warning-symbolic | "No Games Sent" | "{device} didn't take any of them:" |
| receiver, all | emblem-ok-symbolic | "{count} Games Received" (unchanged) | unchanged + replaced count |
| receiver, some missing | dialog-warning-symbolic | "{received} of {total} Games Received" | unchanged + replaced count |
| receiver, none | dialog-warning-symbolic | "No Games Received" | "Nothing from {device} was added to your library." |

Group titles: "Not Sent" (sender), "Not Received" (receiver).

Sender reasons (new keys, device-phrased): exists_skip "Already on {device}"; user_unchecked or
no answer "Not accepted on {device}"; incompatible_state / invalid_item "{device} can't place it";
unsupported_console "{device} doesn't support this console"; unit_incomplete "A file of the game is
missing"; too_many_items "Too many files in one transfer". Receiver reasons reuse `share.reason.*`.

Focus/a11y: Close keeps focus and stays the suggested action (Show in Library first when present,
unchanged). Light and dark come from Adwaita; no custom colours or CSS.
