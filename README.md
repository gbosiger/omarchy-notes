# Omarchy Migration Notes

This directory is the handoff checklist for reproducing the useful, portable
parts of this Omarchy setup on another laptop. It is intentionally a list of
instructions and source locations, not a copy of the whole home directory.

## Agent Instructions

When applying these notes on a new machine:

1. Inspect the current Omarchy version and existing files before changing them.
2. Keep Omarchy package files under `/usr/share/omarchy` and `/usr/share/omarchy-nvim` untouched.
3. Prefer Omarchy's user override files under `~/.config/hypr/` and user-level configuration files.
4. Install packages before restoring configuration that depends on them.
5. Make one logical change at a time and validate it immediately.
6. Do not copy credentials, browser profiles, caches, VM state, or runtime state.
7. Do not apply Alienware, NVIDIA, monitor, or other hardware-specific notes unless the target machine requires them.
8. Preserve unrelated user changes and ask before overwriting an existing configuration.

## Portable Setup

Restore these items from their documented sources:

- Neovim: `nvim.md`
- tmux: `tmux.md`
- Herdr and cross-pane navigation: `herdr.md`
- Shell and terminal appearance: `~/.bashrc`, `~/.bash_profile`, `~/.config/ghostty/config`, `~/.config/foot/foot.ini`, and `~/.config/starship.toml`
- Git preferences: `~/.config/git/config` (review the identity before applying)
- Compose shortcuts: `~/.XCompose` (review personal text before applying)

The important behavior is the shared `Ctrl-h/j/k/l` navigation:

- tmux forwards the keys to Vim/Neovim and otherwise changes tmux panes.
- Neovim changes its own splits first and crosses to the outer pane at an edge.
- Herdr uses `herdr-smart-nav` to forward keys to Neovim or focus a Herdr pane.

## Optional Omarchy Overrides

Review these individually rather than copying the directory wholesale:

- `~/.config/hypr/hyprland.lua`
- `~/.config/hypr/bindings.lua`
- `~/.config/hypr/input.lua`
- `~/.config/hypr/looknfeel.lua`
- `~/.config/hypr/autostart.lua`
- `~/.config/hypr/monitors.lua`
- Selected files under `~/.config/omarchy/hooks/`, `extensions/`, and `plugins/`

The Alienware-specific plugin and scripts are intentionally excluded:

- `~/.config/omarchy/plugins/gebo.alienware/`
- `~/.local/bin/omarchy-alienware`
- `~/.local/bin/alienfx-cli`
- `~/.local/share/alienfx/`

Do not restore `~/.local/state/omarchy/`; it is generated state. Re-select the
desired theme and regenerate state on the target machine.

## Package Baseline

Use the notes and the target machine's needs to create a fresh package list.
Do not blindly install the entire source machine's explicit package list. At a
minimum, the navigation setup needs:

- `neovim`, `git`, `tmux`, `herdr`, `jq`
- `ripgrep`, `fd`, and a Nerd Font for the Neovim workflow
- `wl-clipboard` for Wayland clipboard integration
- Rust tooling if Rust development is wanted

Neovim's additional Mason-managed tools and Ollama models are documented in
`nvim.md`.

## Hardware Notes

Read `suspend-and-nvidia.md` only for the current Alienware m15 R4. It is a
diagnostic record, not a portable setup instruction. Do not transfer its boot,
NVIDIA, monitor, or suspend settings to a new laptop without hardware-specific
verification.

## Validation

After restoration, validate at least:

```sh
herdr config check
tmux -f ~/.config/tmux/tmux.conf -L migration-check new-session -d
tmux -L migration-check kill-server
nvim --headless '+checkhealth' '+qa'
```

Then test `Ctrl-h/j/k/l` inside Neovim splits, at a Neovim edge, and in a
regular Herdr or tmux pane. Check that the expected environment variable is
present (`HERDR_ENV=1` for Herdr and `TMUX` for tmux).
