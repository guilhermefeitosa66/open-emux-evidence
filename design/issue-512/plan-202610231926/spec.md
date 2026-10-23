# Issue #512 — "Verify cores" UI spec

## 1. Preferences › System › "Initial Setup" group (existing `Adw.PreferencesGroup`)
New last row, after "Finish initial setup":
- `Adw.ActionRow`, activatable. Prefix icon `emblem-ok-symbolic` is taken by the status row, so use
  `system-run-symbolic` (fallback: `view-refresh-symbolic`). Suffix `go-next-symbolic`, like the row above.
- Title: **Verify cores** (`settings.system.cores.verify.title`)
- Subtitle: **Check that every console has its core and download the missing ones**
  (`settings.system.cores.verify.subtitle`)
- Hidden under Flatpak (cores belong to the RetroArch Flatpak there, as in `_step_retroarch_cores`).
- Running state: row `set_sensitive(False)`, subtitle **Checking cores…**
  (`settings.system.cores.verify.running`), a `Gtk.Spinner` replaces the chevron. Restored when the job ends
  (also if the dialog was closed and reopened meanwhile: build the row from the window's running flag).
- Progress of an actual download goes to the window task banner (existing `self.tasks`), text
  **Downloading missing cores** (`status.cores.repairing`) with the usual (n/total) and Cancel.

## 2. Results — `Adw.Toast` on the Preferences dialog if open, else on the main window toast overlay
- Nothing missing: **All cores are installed** (`toast.cores.verify.ok`)
- Downloaded: **Downloaded {count} missing cores** (`toast.cores.verify.downloaded`)
- Partial/failed: **Could not download {count} cores: {error}** (`toast.cores.verify.failed`), timeout 6 s
- Updater disabled (`retroarch.updater.enabled: false`): **Core downloads are turned off** (`toast.cores.verify.disabled`)
- Already running (deferred download or repair): **Cores are already being downloaded** (`toast.cores.verify.busy`)

## 3. Automatic repair at start-up (no new widget)
Main window open, bootstrap `completed`, not Flatpak, updater enabled, and some curated core is missing from
every core search dir → same job starts in the background under the task banner
**Restoring missing cores** (`status.cores.restoring`). Library stays usable; no dialog, no first-boot window.
Success → toast **Restored {count} missing cores** (`toast.cores.restored`). Failure → silent (log only), retried
at the next launch, like the deferred download.

## Accessibility / adaptive
Rows are standard Adwaita rows: keyboard focusable, Enter activates, title/subtitle are the accessible name and
description. No layout change at narrow widths beyond what `Adw.PreferencesDialog` already does. Light and dark
come from libadwaita; no new CSS.
