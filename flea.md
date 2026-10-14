# Flea File Manager

Flea is a keyboard-first file manager built for Omarchy. The commands below
install it and make it the default file manager, file chooser, and app opened
by Omarchy's file-manager shortcut.

## Install From Omarchy

Install the signed package from Omarchy's repository:

```sh
omarchy pkg add flea
```

Do not install both `flea` and `flea-bin`. The AUR package is an alternative
that may provide a newer prebuilt release:

```sh
omarchy pkg aur add flea-bin
```

## Make Flea Default

Run this from a terminal inside the graphical Omarchy session:

```sh
flea --default
```

This sets Flea as the handler for:

- Opening directories
- Omarchy's `Super+Shift+F` file-manager shortcut
- Open and Save dialogs through `xdg-desktop-portal`

The portal service is restarted by the command, so the change should apply to
new file dialogs immediately.

Verify the directory handler:

```sh
xdg-mime query default inode/directory
```

Expected result:

```text
com.thisisgm.flea.desktop
```

Launch Flea directly when needed:

```sh
flea
```

Press `?` inside Flea to see the current keyboard shortcuts. Common defaults
include `j`/`k` to move, `h`/`l` to go up or open a folder, `Enter` to open,
`Space` for Quick Look, and `Ctrl+1`/`Ctrl+2`/`Ctrl+3` for list, column, and
grid views.

## Roll Back Or Remove

Keep Flea installed but restore the previous file-manager and portal settings:

```sh
flea --default off
```

Remove the Omarchy repository package after rolling back the defaults:

```sh
omarchy pkg drop flea
```

If the AUR package was installed instead, remove that package with the AUR
helper used on the machine. Do not remove the package before running
`flea --default off`; the desktop integration can otherwise remain registered.

## Remove Nautilus

After installing Flea, Nautilus and its Python extension were removed because
Flea does not depend on them:

```sh
sudo pacman -R nautilus nautilus-python
xdg-mime default com.thisisgm.flea.desktop inode/directory
```

The directory handler now resolves to `com.thisisgm.flea.desktop`, and the
Nautilus application entry is no longer present in the application menu.

## Sources

- Flea upstream: <https://github.com/thisisgm/flea>
- Flea installation details: <https://github.com/thisisgm/flea/blob/main/docs/install.md>
