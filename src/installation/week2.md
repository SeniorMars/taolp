# Week 2 Installation Guide

Prerequisites: You should have completed Week 0 and Week 1 installations. This week focuses on terminal text editors.

Note to self: Make sure to mention how to upload files to remote servers using scp or sftp if needed.

---

## Check What You Have

Most Unix systems come with Vim pre-installed. Check first:

```bash
vim --version
```

If you see version 8.0 or higher, you're good to go. If not installed or very old, follow the instructions below.

---

## Vim Installation

### macOS

macOS includes Vim, but it's often outdated. Install the latest via Homebrew:

```bash
brew install vim
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install vim
```

### Linux (Fedora/RHEL)

```bash
sudo dnf install vim-enhanced
```

### Windows (WSL)

```bash
sudo apt install vim
```

---

## Setting Up Your .vimrc

Create a basic configuration file:

```bash
vim ~/.vimrc
```

Paste this minimal config (from the notes):

```vim
" Basic settings
set nocompatible
syntax on
filetype plugin indent on

set number relativenumber
set mouse=a
set hidden
set ignorecase smartcase
set incsearch hlsearch

" Indentation
set expandtab
set tabstop=4
set shiftwidth=4
set autoindent

" UI
set showcmd
set wildmenu
set laststatus=2

" Leader key
let mapleader = " "

" Key mappings
inoremap jk <Esc>
nnoremap <leader>w :w<CR>
nnoremap <leader>q :q<CR>
nnoremap <leader><space> :noh<CR>

" Window navigation
nnoremap <C-h> <C-w>h
nnoremap <C-j> <C-w>j
nnoremap <C-k> <C-w>k
nnoremap <C-l> <C-w>l
```

Save and quit: `:wq`

Test it:
```bash
vim test.txt
```

You should see line numbers and syntax highlighting.

---

## Neovim (Modern Alternative)

Neovim is a modern fork of Vim with better defaults, Lua configuration, and built-in LSP support. It's compatible with most Vim plugins and commands.

### Why Neovim?

- Better performance
- More active development
- Lua configuration (more powerful than Vimscript)
- Built-in LSP (Language Server Protocol)
- Better plugin ecosystem

### Installation

#### macOS
```bash
brew install neovim
```

#### Linux (Ubuntu/Debian)
```bash
sudo apt install neovim
```

#### Linux (Fedora/RHEL)
```bash
sudo dnf install neovim
```

#### Windows (WSL)
```bash
sudo apt install neovim
```

If not available, you can install to your home directory:

```bash
cd ~
wget https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz
tar xzf nvim-linux64.tar.gz
mkdir -p ~/.local/bin
ln -s ~/nvim-linux64/bin/nvim ~/.local/bin/nvim
```

Then add to your `~/.bashrc`:
```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Verify Installation

```bash
nvim --version
```

Should show version 0.9.0 or higher.

### Configuration

Neovim uses `~/.config/nvim/init.vim` (or `init.lua` for Lua config).

For Vim compatibility, create a simple init.vim:

```bash
mkdir -p ~/.config/nvim
vim ~/.config/nvim/init.vim
```

Add this to use your existing Vimrc:

```vim
set runtimepath^=~/.vim runtimepath+=~/.vim/after
let &packpath = &runtimepath
source ~/.vimrc
```

Or start fresh with Lua config:

```bash
vim ~/.config/nvim/init.lua
```

```lua
-- Basic settings
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.mouse = 'a'
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.hlsearch = true
vim.opt.wrap = false
vim.opt.expandtab = true
vim.opt.tabstop = 4
vim.opt.shiftwidth = 4

-- Leader key
vim.g.mapleader = ' '

-- Key mappings
vim.keymap.set('i', 'jk', '<Esc>')
vim.keymap.set('n', '<leader>w', ':w<CR>')
vim.keymap.set('n', '<leader>q', ':q<CR>')
vim.keymap.set('n', '<leader><space>', ':noh<CR>')
```

### Using Neovim

Commands are identical to Vim:

```bash
nvim filename.txt    # Instead of vim
```

Or alias it:

```bash
# Add to ~/.bashrc or ~/.zshrc
alias vim='nvim'
alias vi='nvim'
```

---

## Helix (Different Paradigm)

Helix is a modern modal editor inspired by Kakoune. It has different keybindings than Vim but similar philosophy.

### Why Helix?

- Selection-first editing (select, then act)
- Multiple cursors built-in
- No configuration needed (works great out of the box)
- Built-in LSP, tree-sitter syntax highlighting
- Written in Rust (fast and safe)

### Differences from Vim

- Different keybindings (not Vim-compatible)
- Selection before action (vs. Vim's action then motion)
- No modes named Normal/Insert (called Normal/Select)
- Different learning curve

### Installation

#### macOS
```bash
brew install helix
```

#### Linux (Ubuntu/Debian)

```bash
# Add PPA
sudo add-apt-repository ppa:maveonair/helix-editor
sudo apt update
sudo apt install helix
```

Or build from source:

```bash
git clone https://github.com/helix-editor/helix
cd helix
cargo install --path helix-term
```

#### Linux (Fedora)
```bash
sudo dnf install helix
```

#### Other

Build from source to your home directory:

```bash
cd ~
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

git clone https://github.com/helix-editor/helix
cd helix
cargo install --path helix-term --locked
```

Then add to `~/.bashrc`:
```bash
export PATH="$HOME/.cargo/bin:$PATH"
```

### Verify Installation

```bash
hx --version
```

### Basic Usage

```bash
hx filename.txt
```

Key differences from Vim:
- `i` enters insert mode (same)
- `Esc` returns to normal mode (same)
- But motion then action: `w` selects word, then `d` deletes selection
- `:q` to quit (same)
- `:w` to save (same)

### Configuration

Helix works great without configuration, but you can customize:

```bash
mkdir -p ~/.config/helix
hx ~/.config/helix/config.toml
```

Basic config:

```toml
theme = "onedark"

[editor]
line-number = "relative"
mouse = true
cursorline = true
auto-save = false

[editor.cursor-shape]
insert = "bar"
normal = "block"
select = "underline"

[keys.normal]
# Add custom keybindings if desired
```

### Should You Use Helix?

For this class, we focus on Vim because:
- More widely available
- More resources and community
- Transferable to other tools (Vim mode everywhere)

But Helix is worth trying if you:
- Want a modern editor without configuration
- Like the selection-first paradigm
- Want built-in LSP without setup

You can learn both, but start with Vim for this course.

---

## Running vimtutor

The best way to learn Vim is the built-in tutorial.

### For Vim:
```bash
vimtutor
```

### For Neovim:
```bash
nvim +Tutor
```

### For Helix:
```bash
hx --tutor
```

Complete at least lessons 1-4. Takes about 30 minutes.

---

## Verification Checklist

Test your setup:

```bash
# Create test file
echo "Hello, Week 2!" > test.txt

# Edit with Vim
vim test.txt
# Press i, type something, press Esc, type :wq

# Edit with Neovim (if installed)
nvim test.txt

# Edit with Helix (if installed)
hx test.txt

# Clean up
rm test.txt
```

Check your config works:

```bash
# Vim
vim ~/.vimrc
# Should see line numbers and syntax highlighting

# Neovim
nvim ~/.config/nvim/init.vim
# or
nvim ~/.config/nvim/init.lua
```

---

## Common Issues

### "vim: command not found"

Install Vim following the instructions above for your system. It might also be named `vi` on some systems.

### "E325: ATTENTION" message

Vim found a swap file from a previous session. Choose:
- `O` - Open read-only
- `R` - Recover and continue editing
- `D` - Delete swap file (if you're sure)
- `Q` - Quit

### Colors look wrong

Enable 256 colors in Vim:
```vim
set t_Co=256
```

Or use a better terminal (iTerm2, Alacritty, WezTerm).

### Can't use system clipboard

Check if Vim was compiled with clipboard support:
```bash
vim --version | grep clipboard
```

Should show `+clipboard`. If it shows `-clipboard`, reinstall Vim:
```bash
# macOS
brew install vim

# Linux
sudo apt install vim-gtk3
```

