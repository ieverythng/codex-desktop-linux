# User-Local Desktop Integration

This folder packages a user-local install layout for `codex-desktop-linux`.

It adds:

- a stable install root under `~/.local/opt/codex-desktop-linux`
- self-contained maintenance scripts under `~/.local/opt/codex-desktop-linux/bin`
- thin launch/check/update/version wrappers under `~/.local/bin`
- a desktop entry under `~/.local/share/applications`
- an icon extracted from the local `Codex.dmg`
- metadata tracking for the wrapper repo and cached `Codex.dmg`
- a weekly `systemd --user` timer for unattended update checks and rebuilds

## Files

The package is laid out as reusable payload files. The installer copies them into:

- `~/.local/opt/codex-desktop-linux/bin/`
- `~/.local/opt/codex-desktop-linux/lib/codex-desktop-linux/`
- `~/.local/bin/` wrappers
- `files/.local/share/applications/codex-desktop.desktop`
- `files/.config/systemd/user/codex-desktop-update.service`
- `files/.config/systemd/user/codex-desktop-update.timer`

## Expected Placement

If installing manually, copy the files to:

- `~/.local/opt/codex-desktop-linux/bin/`
- `~/.local/opt/codex-desktop-linux/lib/codex-desktop-linux/`
- `~/.local/bin/` wrappers that exec into `~/.local/opt/codex-desktop-linux/bin/`
- `~/.local/share/applications/`
- `~/.config/systemd/user/`

The preferred git checkout location is:

- `~/workspace/codex-desktop-linux`

The installed maintenance scripts record the repo path in user state and use that checkout for `git pull`, while rebuilding runtime assets into `~/.local/opt/codex-desktop-linux` via `CODEX_INSTALL_ROOT` / `CODEX_INSTALL_DIR`.

## Install

From the repository root:

```bash
./contrib/user-local-install/install-user-local.sh
```

The installer:

1. copies standalone helper scripts into `~/.local/opt/codex-desktop-linux`
2. installs thin wrappers into `~/.local/bin`
3. makes the scripts executable
4. reloads the user `systemd` daemon if available
5. enables the weekly timer if the user bus is reachable
6. refreshes desktop metadata if available
7. records local metadata and extracts the icon if `Codex.dmg` already exists

## Commands

After installation:

```bash
codex-desktop
codex-desktop-check-update
codex-desktop-update
codex-desktop-version
```

## Notes

- The icon is not committed as a binary asset here. It is generated locally from `Codex.dmg`.
- The helper scripts track both upstream wrapper changes and upstream `Codex.dmg` headers.
- The helper scripts are copied into `~/.local/opt` and do not run from the git checkout directly.
- The weekly timer runs `codex-desktop-update --quiet`.
