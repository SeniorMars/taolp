# Week 1 Installation Guide

**Prerequisites**: You should have completed the [Week 0 Installation Guide](./week0.md) and have SSH access to CLEAR.

This week focuses on shell scripting, so you'll need tools for writing, testing, and debugging bash scripts.

---

## Required Tools

### 1. ShellCheck (Script Linter)

ShellCheck is essential for learning bash. It catches common mistakes and suggests improvements.

#### macOS
```bash
brew install shellcheck
```

#### Linux (Ubuntu/Debian)
```bash
sudo apt install shellcheck
```

#### Windows (WSL)
```bash
sudo apt install shellcheck
```

#### CLEAR
ShellCheck should already be available. Test with:
```bash
shellcheck --version
```

If not installed, you can download the binary:
```bash
# In your home directory on CLEAR
cd ~
wget https://github.com/koalaman/shellcheck/releases/download/stable/shellcheck-stable.linux.x86_64.tar.xz
tar -xf shellcheck-stable.linux.x86_64.tar.xz
mkdir -p ~/.local/bin
mv shellcheck-stable/shellcheck ~/.local/bin/
```

Then add to your `~/.bashrc`:
```bash
export PATH="$HOME/.local/bin:$PATH"
```

#### Verify Installation
```bash
shellcheck --version
```

---

### 2. Text Editor

You need a way to write scripts. Choose **one** of the following:

#### Option A: nano (Easiest for beginners)
Already installed on most systems. No setup needed.

```bash
nano myscript.sh
```

**Basic commands:**
- `Ctrl+O` - Save
- `Ctrl+X` - Exit
- `Ctrl+K` - Cut line
- `Ctrl+U` - Paste

#### Option B: vim (More powerful, steeper learning curve)
Already installed. We'll cover vim in Week 3, but you can start learning now.

```bash
vim myscript.sh
```

**Survival guide:**
- Press `i` to enter insert mode
- Type your script
- Press `Esc` to exit insert mode
- Type `:wq` and press Enter to save and quit
- Type `:q!` and press Enter to quit without saving

#### Option C: VS Code with Remote SSH (Recommended for local editing)

If you prefer working on CLEAR with a GUI editor:

1. Install [VS Code](https://code.visualstudio.com/)
2. Install the "Remote - SSH" extension
3. Connect to CLEAR through VS Code

**Benefits:**
- Syntax highlighting
- Auto-completion
- Integrated terminal
- ShellCheck integration

---

## Optional (But Useful) Tools

### bat (Better `cat` with syntax highlighting)

Useful for viewing scripts with syntax highlighting.

#### macOS
```bash
brew install bat
```

#### Linux
```bash
sudo apt install bat
```

**Note**: On some systems, the command is `batcat` instead of `bat` due to naming conflicts.

**Usage:**
```bash
bat myscript.sh  # View script with highlighting
```

---

### tldr (Simplified man pages)

Quick reference for commands instead of full man pages.

#### macOS
```bash
brew install tldr
```

#### Linux
```bash
sudo apt install tldr
```

**Usage:**
```bash
tldr tar    # Quick examples for tar
tldr find   # Quick examples for find
```

---

## Verification

Run these commands to verify your setup:

```bash
# Check bash version (should be 4.0+)
bash --version

# Check shellcheck
shellcheck --version

# Check text editor (choose one)
nano --version
vim --version

# Test script creation
echo '#!/usr/bin/env bash' > test.sh
echo 'echo "Hello, Week 1!"' >> test.sh
chmod +x test.sh
./test.sh

# Test shellcheck
shellcheck test.sh

# Clean up
rm test.sh
```

Expected output:
```bash
$ ./test.sh
Hello, Week 1!

$ shellcheck test.sh
# No output means no errors!
```

---

## Setting Up Your Environment

### Create a scripts directory

Good practice: keep scripts organized.

```bash
mkdir -p ~/scripts
cd ~/scripts
```

### Add scripts to PATH (Optional)

If you want to run your scripts from anywhere:

```bash
# Add to ~/.bashrc (or ~/.zshrc)
export PATH="$HOME/scripts:$PATH"
```

Then reload:
```bash
source ~/.bashrc
```

Now scripts in `~/scripts` can be run from anywhere (if executable).

---

## ShellCheck Integration

### Using ShellCheck

Always check your scripts before running:

```bash
shellcheck myscript.sh
```

**Example output:**
```
In myscript.sh line 3:
rm $file
   ^---^ SC2086: Double quote to prevent globbing and word splitting.
```

### Fix the issue:
```bash
rm "$file"  # Add quotes
```

### Ignore specific warnings (when you know what you're doing):

```bash
# shellcheck disable=SC2086
rm $file  # Intentionally unquoted
```

---

## Common Issues

### "Permission denied" when running script

**Problem:**
```bash
$ ./myscript.sh
-bash: ./myscript.sh: Permission denied
```

**Solution:**
```bash
chmod +x myscript.sh
```

---

### "No such file or directory" with shebang

**Problem:**
```bash
#!/bin/bash
```
Works on some systems but not others.

**Solution:** Use the portable version:
```bash
#!/usr/bin/env bash
```

---

### Scripts work locally but not on CLEAR

**Problem:** Different bash versions or missing tools.

**Solution:** Always test on CLEAR before submitting:
```bash
ssh clear
cd ~/path/to/scripts
./myscript.sh
```

---

## Editor Configuration

### nano Configuration

Create `~/.nanorc`:
```bash
set autoindent
set tabsize 4
set tabstospaces
include /usr/share/nano/*.nanorc
```

### vim Configuration (Basic)

Create `~/.vimrc`:
```bash
" Basic settings for shell scripting
syntax on
set number
set autoindent
set tabstop=4
set shiftwidth=4
set expandtab
```

---

## Testing Your Setup

Create and run this test script:

```bash
#!/usr/bin/env bash
# test_setup.sh - Verify Week 1 setup

set -euo pipefail

echo "Testing Week 1 setup..."

# Test 1: Bash version
echo -n "Bash version: "
bash --version | head -1

# Test 2: ShellCheck
echo -n "ShellCheck: "
if command -v shellcheck &> /dev/null; then
    echo "✓ Installed"
else
    echo "✗ Not found - Install shellcheck!"
    exit 1
fi

# Test 3: Text editor
echo -n "Text editor: "
if command -v nano &> /dev/null; then
    echo "✓ nano available"
elif command -v vim &> /dev/null; then
    echo "✓ vim available"
else
    echo "✗ No editor found"
    exit 1
fi

# Test 4: Script permissions
echo -n "File permissions: "
if [[ -x "$0" ]]; then
    echo "✓ Script is executable"
else
    echo "✗ Script not executable (run: chmod +x $0)"
fi

echo ""
echo "Setup complete! You're ready for Week 1."
```

Save as `test_setup.sh`, make executable, and run:
```bash
chmod +x test_setup.sh
./test_setup.sh
```

---

## Quick Reference Card

Save this for quick access:

```bash
# Make script executable
chmod +x script.sh

# Run script
./script.sh

# Check script for errors
shellcheck script.sh

# Debug script (show commands as they execute)
bash -x script.sh

# Edit script
nano script.sh    # or vim script.sh

# View script with syntax highlighting
bat script.sh     # if installed
```

---

## What's NOT Required

You **don't** need to install:
- Complex IDEs
- Git GUI tools (command line is fine)
- Docker (not until later weeks)
- Any programming languages besides bash

Keep it simple. You already have everything you need to write powerful scripts.

---

## Next Steps

1. Verify your installation with the test script above
2. Complete the Week 1 homework
3. Experiment with ShellCheck on your scripts
4. Consider learning vim basics (we'll cover it in Week 3)

**Questions?** Bring them to office hours or post in the class forum.

---

## Resources

- [ShellCheck Wiki](https://github.com/koalaman/shellcheck/wiki)
- [Bash Guide](https://mywiki.wooledge.org/BashGuide)
- [Nano Guide](https://www.nano-editor.org/dist/latest/nano.html)
- [Vim Tutorial](https://www.openvim.com/) (interactive)

**Remember**: The goal is to write working scripts, not to master every tool perfectly. Start simple and build up!
