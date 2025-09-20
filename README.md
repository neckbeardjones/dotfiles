# Dotfiles Cheatsheet

This README provides quick commands for managing dotfiles (`nvim`, `tmux`, `zsh`) in `~/dotfiles` using GNU Stow and Git, with backups to https://github.com/neckbeardjones/dotfiles.

## Prerequisites
- **Install Tools**:
  ```bash
  brew install stow git
  ```
- **GitHub Repo**: Ensure https://github.com/neckbeardjones/dotfiles exists.
- **Personal Access Token (PAT)**: Generate at https://github.com/settings/tokens with `repo` scope for Git pushes.

## Directory Structure
```
~/dotfiles/
├── nvim/.config/nvim/  # Neovim config (init.lua, lua/, doc/)
├── tmux/.tmux.conf     # tmux config
├── zsh/.zshrc          # Zsh config
└── .gitignore          # Optional: ignore *.swp, .DS_Store
```

## Using Stow
Stow creates symlinks from `~/dotfiles` to your home directory (e.g., `~/.config/nvim`, `~/.tmux.conf`).

### Initial Setup
1. **Stow All Configs**:
   ```bash
   cd ~/dotfiles
   mv ~/.config/nvim ~/.config/nvim.bak 2>/dev/null || true
   mv ~/.tmux.conf ~/.tmux.conf.bak 2>/dev/null || true
   mv ~/.zshrc ~/.zshrc.bak 2>/dev/null || true
   stow nvim tmux zsh
   ```
   Creates:
   - `~/.config/nvim -> ~/dotfiles/nvim/.config/nvim`
   - `~/.tmux.conf -> ~/dotfiles/tmux/.tmux.conf`
   - `~/.zshrc -> ~/dotfiles/zsh/.zshrc`

2. **Verify Symlinks**:
   ```bash
   ls -l ~/.config/nvim ~/.tmux.conf ~/.zshrc
   ```

3. **Test Configs**:
   - Neovim: `nvim`, run `:checkhealth` and `:Lazy sync` (for Lazy.nvim).
   - tmux: `unset TMUX && tmux`, press `Ctrl-s I` for plugins.
   - Zsh: `source ~/.zshrc`.

### Fix Stow Conflicts
If `stow` errors (e.g., file exists):
```bash
mv ~/.config/nvim ~/.config/nvim.bak
stow -R nvim  # Force overwrite
```

## Adding a New Config
Example: Add `~/.gitconfig` to `~/dotfiles`.
1. **Move to `~/dotfiles`**:
   ```bash
   mkdir ~/dotfiles/git
   mv ~/.gitconfig ~/dotfiles/git/.gitconfig
   ```

2. **Stow**:
   ```bash
   cd ~/dotfiles
   stow git
   ```

3. **Verify**:
   ```bash
   ls -l ~/.gitconfig  # Should link to ~/dotfiles/git/.gitconfig
   ```

## Updating a Config
Example: Edit `~/.tmux.conf`.
1. **Edit**:
   ```bash
   nvim ~/dotfiles/tmux/.tmux.conf
   ```

2. **Re-stow**:
   ```bash
   cd ~/dotfiles
   stow -R tmux
   ```

3. **Test**:
   ```bash
   tmux source ~/.tmux.conf
   ```

## Backing Up to Git
1. **Add and Commit**:
   ```bash
   cd ~/dotfiles
   git add .
   git commit -m "Update dotfiles: <describe changes>"
   ```

2. **Push to GitHub**:
   ```bash
   git push
   ```
   - Username: `neckbeardjones`
   - Password: Your PAT from https://github.com/settings/tokens
   - Or use GitHub CLI: `brew install gh && gh auth login`

3. **Verify**: Check https://github.com/neckbeardjones/dotfiles.

## Restoring on a New Machine
1. **Clone**:
   ```bash
   git clone https://github.com/neckbeardjones/dotfiles.git ~/dotfiles
   ```

2. **Stow**:
   ```bash
   cd ~/dotfiles
   stow nvim tmux zsh
   ```

3. **Install Plugins**:
   - Neovim: `nvim`, run `:Lazy sync`.
   - tmux: `tmux`, press `Ctrl-s I`.

## Troubleshooting
- **Stow Conflict**: Backup conflicting files (e.g., `mv ~/.zshrc ~/.zshrc.bak`) and re-stow.
- **Git Auth**: Regenerate PAT or use `gh auth login`.
- **tmux Copy Mode**: To copy commands:
  - `Ctrl-s [`, move with `j`/`k`/`G`, select with `v`, copy with `y` (system) or `Enter` (tmux), paste with `Cmd-V` or `Ctrl-s ]`.