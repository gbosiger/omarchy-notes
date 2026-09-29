# Tmux Notes

Configuration file: `~/.config/tmux/tmux.conf`

This is a user copy of Omarchy's stock configuration. The only intentional
custom block currently added to the stock file is the smart pane navigation
section below. If Omarchy's stock configuration changes, start from the new
`/usr/share/omarchy/config/tmux/tmux.conf` and reapply that block rather than
blindly replacing the stock file.

## Current Setup

- Prefix keys: `Ctrl-Space` and `Ctrl-b`.
- `Ctrl-Space` sends the prefix through to nested tmux when needed.
- `prefix+h`: split top/bottom (`split-window -v`).
- `prefix+v`: split left/right (`split-window -h`).
- `Alt-Enter`: split top/bottom.
- `Alt-Shift-Enter`: split left/right.
- `prefix+x` or `Alt-Escape`: kill the current pane.
- `Ctrl-Alt-h/j/k/l` is not used; pane focus uses `Ctrl-Alt` plus the arrow keys.
- `Ctrl-Alt-Shift` plus an arrow resizes the pane by 5 cells.
- Windows use `prefix+c`, `prefix+r`, `prefix+k`, and `Alt-1..9`.
- Sessions use `prefix+Shift-c/r/k` and `Alt-Up/Down` for switching.
- Mouse support is enabled.
- Pane and window indexes start at 1; windows are renumbered automatically.
- Scrollback is 50,000 lines and escape time is short for responsive key handling.
- The status bar is at the top with blue accents and hostname/session/window information.

## Smart Pane Navigation

The root-level `Ctrl-h/j/k/l` bindings are the important custom addition:

```tmux
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|n?vim?x?)(diff)?$'"
bind-key -n C-h if-shell "$is_vim" 'send-keys C-h' 'select-pane -L'
bind-key -n C-j if-shell "$is_vim" 'send-keys C-j' 'select-pane -D'
bind-key -n C-k if-shell "$is_vim" 'send-keys C-k' 'select-pane -U'
bind-key -n C-l if-shell "$is_vim" 'send-keys C-l' 'select-pane -R'
```

When the active tmux pane runs Vim/Neovim, tmux forwards the key into the
editor. Otherwise it selects the neighboring tmux pane. This is paired with
the Neovim plugin configuration documented in `herdr.md` and `nvim.md`.

## Recovery

1. Restore or recreate `~/.config/tmux/tmux.conf`.
2. Validate it:

   ```sh
   tmux source-file ~/.config/tmux/tmux.conf
   tmux display-message "tmux config loaded"
   ```

3. If tmux is already running, reload with `Ctrl-Space`, then `q`.
4. Keep a separate backup of this file as well as the Neovim and Herdr notes;
   the tmux file is a user-level configuration and is not stored in the
   Neovim repository.

## Exit

- Detach: `Ctrl-Space`, then `d`.
- Kill the current pane: `Ctrl-Space`, then `x`.
- Kill the current window: `Ctrl-Space`, then `k`.
- Kill the current session: `Ctrl-Space`, then `K`.
