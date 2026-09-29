# Herdr Notes

Configuration file: `~/.config/herdr/config.toml`

Helper executable: `~/.local/bin/herdr-smart-nav`

Herdr version used during setup: `0.8.2`.

## Current Setup

The configuration mirrors the tmux layout model:

- tmux session -> Herdr workspace
- tmux window -> Herdr tab
- tmux pane -> Herdr pane
- New panes inherit the current working directory with `new_cwd = "follow"`.
- Prefix: `Ctrl-Space`.
- `prefix+h` and `Alt-Enter`: top/bottom split.
- `prefix+v` and `Alt-Shift-Enter`: left/right split.
- `prefix+x` or `Alt-Escape`: close pane.
- `prefix+z`: zoom.
- `prefix+;`: last pane.
- `Ctrl-Alt` plus an arrow focuses a neighboring pane.
- `Ctrl-Alt-Shift` plus an arrow resizes a pane.
- `prefix+c/r/k` creates, renames, and closes tabs.
- `prefix+1..9` or `Alt-1..9` switches tabs.
- `prefix+Shift-c/r/k` creates, renames, and closes workspaces.
- `Alt-Up/Down` switches workspaces.
- Terminal theme, blue accent, no pane gaps, no outer borders, no scrollbars.
- Mouse capture is enabled.
- Pane/tab/workspace closing does not ask for confirmation.
- Desktop notifications use the system delivery method.

## Smart `Ctrl-h/j/k/l` Navigation

Herdr has four direct custom bindings for `Ctrl-h/j/k/l`. Each invokes:

```text
herdr-smart-nav <direction> <key>
```

The helper asks Herdr for the current pane process. If the foreground process
is Neovim, it forwards the original key into that pane. Otherwise it focuses
the neighboring Herdr pane in the requested direction.

Neovim also checks `HERDR_ENV=1`. Inside Neovim it first moves between Neovim
windows. If there is no window in that direction, it runs:

```sh
herdr pane focus --current --direction <left|right|up|down>
```

This produces tmux-style behavior across both nested Neovim splits and Herdr
panes without changing tmux behavior.

## Recovery

1. Restore `~/.config/herdr/config.toml`.
2. Restore `~/.local/bin/herdr-smart-nav` and make it executable:

   ```sh
   chmod +x ~/.local/bin/herdr-smart-nav
   ```

3. Confirm the helper is on `PATH`:

   ```sh
   command -v herdr-smart-nav
   ```

4. Validate the configuration:

   ```sh
   herdr config check
   ```

5. Reload a running Herdr server:

   ```sh
   herdr server reload-config
   ```

   Alternatively, restart Herdr. Restart Neovim too if its plugin condition
   was changed.

## Exit

- Detach from Herdr: `Ctrl-Space`, then `d`.
- Stop Herdr and its managed panes from outside Herdr:

  ```sh
  herdr server stop
  ```

`Ctrl-Space`, then `q` reloads the Herdr configuration; it does not quit.

## Useful Checks

```sh
herdr --version
herdr config check
herdr pane process-info --current
```
