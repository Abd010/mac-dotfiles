# mac-dotfiles

Personal macOS dotfiles. Each file sits at the same path it has under `$HOME`
and is symlinked into place, so editing the live config edits this repo.

Neovim config is a separate repo:
[Abd010/kickstart.nvim](https://github.com/Abd010/kickstart.nvim) → `~/.config/nvim`.

## What's here

| File | What it configures |
| --- | --- |
| `.zshrc` | zsh + oh-my-zsh, fnm, Java version switching, starship |
| `.zprofile` | Homebrew shellenv |
| `.tmux.conf` | tmux: vim-style panes, window reordering, office/personal session views, TPM plugins |
| `.config/starship.toml` | Starship prompt (AWS segment only while a falcon session is active) |
| `.config/ghostty/config` | Ghostty terminal (FiraCode Nerd Font, Catppuccin Mocha) |
| `.config/karabiner/karabiner.json` | Karabiner-Elements key remaps |
| `.local/bin/tmux-sessionizer` | fzf project picker → tmux session (`prefix + f`) |

## Setup on a new Mac

### 1. Homebrew packages

```sh
brew install tmux neovim starship fnm git-delta fzf git-lfs ripgrep fd
brew install --cask ghostty karabiner-elements font-fira-code-nerd-font
```

### 2. Third-party clones (not vendored)

These are cloned from upstream rather than committed here.

```sh
# oh-my-zsh (--keep-zshrc so it doesn't overwrite ours)
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended --keep-zshrc

# oh-my-zsh custom plugins (listed in `plugins=(...)` in .zshrc)
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting

# TPM, the tmux plugin manager
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

The remaining tmux plugins (tmux-sensible, vim-tmux-navigator, tmux-resurrect,
tmux-continuum, catppuccin/tmux) are declared in `.tmux.conf`; TPM installs
them when you press `prefix + I` inside tmux.

### 3. Clone and symlink

Back up any existing versions first — `ln -sf` replaces them.

```sh
git clone https://github.com/Abd010/mac-dotfiles.git ~/mac-dotfiles
cd ~/mac-dotfiles
for f in .zshrc .zprofile .tmux.conf .config/starship.toml .config/ghostty/config \
         .config/karabiner/karabiner.json .local/bin/tmux-sessionizer; do
  mkdir -p "$HOME/$(dirname "$f")"
  ln -sf "$PWD/$f" "$HOME/$f"
done
```

Some `.zshrc` lines are specific to the Salesforce laptop (gimlet, aisuite,
hard-coded JDK paths) and need adjusting on other machines.

## Git config (not tracked)

`~/.gitconfig` stays local because tools (gimlet, `git lfs install`) write
machine-specific values into it. What it sets up, to recreate by hand:

- **delta as the pager**: `core.pager = delta` and
  `interactive.diffFilter = delta --color-only`, with `delta.side-by-side`,
  `delta.line-numbers`, `delta.navigate` (`n`/`N` jump between files) and the
  `Catppuccin Mocha` syntax theme.
- **SSH commit signing**: `commit.gpgsign = true`, `gpg.format = ssh`,
  `user.signingkey = ~/.ssh/id_ed25519.pub`, plus
  `gpg.ssh.allowedSignersFile = ~/.ssh/allowed_signers` so
  `git log --show-signature` can verify signatures.
- **`push.autoSetupRemote = true`**: the first `git push` on a new branch sets
  its upstream automatically.
- **Global ignore** (`~/.config/git/ignore`): `**/.claude/settings.local.json`
  and `**/.claude/.cc-writes/`.
