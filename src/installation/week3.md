# Week 3 Installation Guide

Prerequisites: You should have completed Week 0-2 installations. This week requires minimal new installation since most tools are already available or will be installed on your VPS.

---

## Required Tools

1. SSH Client - You should already have this from Week 0.

### 2. tmux (Local Installation - Optional)

tmux on your laptop is optional but useful for local work.

macOS:
```bash
brew install tmux
```

Linux (Ubuntu/Debian):
```bash
sudo apt install tmux
```

Linux (Fedora/RHEL):
```bash
sudo dnf install tmux
```

Windows (WSL):
```bash
sudo apt install tmux
```

Should show `tmux 3.0` or higher.

Note: You'll also install tmux on your DigitalOcean droplet during homework.

---

### 3. rsync

Check if installed:
```bash
rsync --version
```

If missing:

macOS: Pre-installed
Linux: `sudo apt install rsync`
Windows (WSL): `sudo apt install rsync`

---

### 4. DigitalOcean Student Credits

Not an installation, but required for homework.

Steps:
1. Go to [https://education.github.com/pack](https://education.github.com/pack)
2. Sign up with Rice email
3. Get approved (usually instant)
4. Activate DigitalOcean offer ($200 credit)

You don't need this before class, but start the GitHub Student Pack application early (can take 1-2 days for approval).
