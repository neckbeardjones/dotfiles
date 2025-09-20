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
├── .gitignore          # Optional: ignore *.swp, .DS_Store
└── README.md           # This cheatsheet
```

## Cloning and Loading Dotfiles
### Clone the Repo
Clone your dotfiles to a new machine:
```bash
git clone https://github.com/neckbeardjones/dotfiles.git ~/dotfiles
cd ~/dotfiles
```

### Load Dotfiles with Stow
Create symlinks to apply configs:
```bash
cd ~/dotfiles
stow nvim tmux zsh
```
This creates:
- `~/.config/nvim -> ~/dotfiles/nvim/.config/nvim`
- `~/.tmux.conf -> ~/dotfiles/tmux/.tmux.conf`
- `~/.zshrc -> ~/dotfiles/zsh/.zshrc`

### Verify Symlinks
```bash
ls -l ~/.config/nvim ~/.tmux.conf ~/.zshrc
```

### Test Configs
- **Neovim**: `nvim`, run `:checkhealth` and `:Lazy sync` (for Lazy.nvim).
- **tmux**: `unset TMUX && tmux`, press `Ctrl-s I` for plugins.
- **Zsh**: `source ~/.zshrc`.

## Handling Conflicts (Existing Dotfiles Repo)
If `~/dotfiles` already exists (e.g., another repo or local files):
1. **Backup Existing Repo**:
   ```bash
   mv ~/dotfiles ~/dotfiles.bak
   ```
2. **Clone New Repo**:
   ```bash
   git clone https://github.com/neckbeardjones/dotfiles.git ~/dotfiles
   ```
3. **Merge Configs (Optional)**:
   Copy desired files from `~/dotfiles.bak` to `~/dotfiles`:
   ```bash
   cp -r ~/dotfiles.bak/nvim/.config/nvim/ ~/dotfiles/nvim/.config/nvim/
   cp ~/dotfiles.bak/tmux/.tmux.conf ~/dotfiles/tmux/.tmux.conf
   cp ~/dotfiles.bak/zsh/.zshrc ~/dotfiles/zsh/.zshrc
   ```
4. **Stow**:
   ```bash
   cd ~/dotfiles
   mv ~/.config/nvim ~/.config/nvim.bak 2>/dev/null || true
   mv ~/.tmux.conf ~/.tmux.conf.bak 2>/dev/null || true
   mv ~/.zshrc ~/.zshrc.bak 2>/dev/null || true
   stow nvim tmux zsh
   ```
5. **Commit Mmerged Changes**:
   ```bash
   git add .
   git commit -m "Merge existing dotfiles"
   git push
   ```
   Use PAT (username: `neckbeardjones`, password: PAT from https://github.com/settings/tokens).

## Adding a New Config
Example: Add `~/.gitconfig`.
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
   ls -l ~/.gitconfig
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
2. **Push**:
   ```bash
   git push
   ```
   Use PAT or `gh auth login` (install: `brew install gh`).
3. **Verify**: Check https://github.com/neckbeardjones/dotfiles.

## tmux Copy Mode (For Copying Commands)
To copy commands (e.g., `git add nvim`):
1. Enter: `Ctrl-s [`.
2. Move: `j` (down), `k` (up), `G` (bottom).
3. Select: `v`, move with `j`/`l`.
4. Copy: `y` (system clipboard, paste with `Cmd-V`) or `Enter` (tmux clipboard, paste with `Ctrl-s ]`).

## Troubleshooting
- **Stow Conflict**: Backup conflicts (e.g., `mv ~/.zshrc ~/.zshrc.bak`) and re-stow.
- **Git Auth**: Regenerate PAT or use `gh auth login`.
- **tmux Spacebar Jumps**: Use `v` instead of `Space`. Fix: Add to `~/dotfiles/tmux/.tmux.conf`:
  ```bash
  bind -T copy-mode-vi Space send-keys -X begin-selection
  ```
  Then: `stow -R tmux && tmux source ~/.tmux.conf`.