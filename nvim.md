# Neovim Configuration Notes

Repository: `git@github.com:gbosiger/nvim.git`

Expected active location: `~/.config/nvim`

This is the Omarchy variant of a LazyVim configuration. Do not edit
`/usr/share/omarchy` or `/usr/share/omarchy-nvim`; those are package files and
can be replaced by Omarchy updates.

## Installation / Recovery

Back up an existing configuration, then clone the repository into the active
location:

```sh
mv ~/.config/nvim ~/.config/nvim.backup.$(date +%Y%m%d-%H%M%S)
git clone git@github.com:gbosiger/nvim.git ~/.config/nvim
nvim
```

If the repository already exists:

```sh
git -C ~/.config/nvim pull --ff-only
nvim
```

The first launch bootstraps `lazy.nvim` under Neovim's data directory and
installs plugins. `lazy-lock.json` records the plugin revisions.

## Required Environment

- Omarchy-provided Neovim/LazyVim base configuration.
- Git and network access for the initial plugin bootstrap.
- `ripgrep` and `fd` for the Telescope/Snacks search workflow.
- A working compiler/toolchain for plugins that need native builds.
- Rust tooling (`rustup`, Cargo, `rustfmt`) for Rust development.
- Ollama running locally for CodeCompanion.
- Ollama models:

  ```sh
  ollama pull qwen3:8b
  ollama pull qwen2.5-coder:7b
  ```

- `wl-copy` and `wl-paste` for the Wayland clipboard integration.
- tmux and/or Herdr if cross-pane navigation is desired.

Mason automatically ensures these tools:

- `codelldb`
- `lua-language-server`
- `luacheck`
- `rust-analyzer`
- `stylua`

Treesitter automatically installs parsers for Bash, Lua, Markdown,
Markdown-inline, Rust, and TOML. Bash LSP and Lua LSP are explicitly enabled;
Rust LSP comes through the LazyVim Rust extra and rustaceanvim.

## Important Customizations

- CodeCompanion uses local Ollama models:
  - Chat and command interactions: `qwen3:8b`.
  - Inline edits: `qwen2.5-coder:7b`.
- CodeCompanion adapters are defined under `opts.adapters.http`.
- Tmux navigation loads when `TMUX` is set.
- Herdr navigation loads when `HERDR_ENV=1`.
- `Ctrl-h/j/k/l` first navigates Neovim windows. At a Neovim edge, the Herdr
  setup focuses the neighboring Herdr pane.
- The generated `lua/plugins/theme.lua` is intentionally Git-ignored and is
  managed by Omarchy's current theme system.

## Checks After Restoration

Inside Neovim, run:

```vim
:checkhealth
:Lazy
:Mason
:WhichKey
```

For navigation, test `Ctrl-h/j/k/l` in both a Neovim split and at the edge of
the Neovim pane. In a tmux pane, verify the `TMUX` environment variable; in a
Herdr pane, verify `HERDR_ENV=1`.

## Current Local Git Reminder

The current checkout has the CodeCompanion adapter migration and Herdr-aware
navigation committed. On another machine, inspect the checkout before pulling
or replacing it, and do not discard unrelated local changes.
