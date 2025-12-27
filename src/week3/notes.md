# Week 3 Notes - SSH, tmux, and Intro to Servers

This week we move from working locally to working on remote servers. You've been SSH'ing into CLEAR since Week 0, but now we'll understand what servers really are, configure SSH properly, and learn to create persistent workspaces that survive disconnections.

## Introduction

For the past three weeks, you've been doing all your work on CLEAR. But what is CLEAR, really? It's not "your computer in the cloud", but a shared computer(s). Your laptop is just a window into this machine.

The problem: when you close your laptop or your WiFi drops, your connection dies and your running jobs die with it. Today we fix this.

## Part 1: Understanding Servers

### What is a Server?

A server is simply a computer optimized for specific tasks that runs continuously and is typically accessed remotely. It's not fundamentally different from your laptop—it's just a computer you don't sit at.

Key characteristics:
- Always on (no sleep mode or battery concerns)
- Often shared by multiple users
- Typically has no monitor, keyboard, or mouse attached
- Optimized for specific workloads (computation, storage, etc.)

Servers are not magic, and "the cloud" is just someone else's computer (often in a data center).

### Why Use Servers?

Power:
- More CPU cores
- More RAM 
- Better GPUs for machine learning
- Faster storage systems

Reliability:
- 24/7 uptime (no shutting down for battery)
- Professional backups
- Redundant power and cooling
- Maintained by IT staff

Shared Resources:
- Expensive hardware shared across users
- Large datasets stored centrally
- Software licenses (MATLAB, etc.)
- Collaboration (shared files, shared environments)

Accessibility:
- Work from anywhere (home, lab, coffee shop)
- Switch between devices easily
- Always available
- Same environment for entire team

### The Rice Server Landscape

CLEAR (Cluster for Learning, Exploration, and Research):
- Linux cluster for teaching and research
- Fedora-based operating system
- Shared by all Rice students and faculty
- Your home directory is networked storage (accessible from all nodes)
- Primary teaching cluster

Other servers you might encounter:

| Server | Purpose |
|--------|---------|
| DAVinCI | High-performance computing (research) |
| Lab servers | Department-specific resources |
| nfs.rice.edu | Networked file storage |
| departmental servers | CS, ECE, etc. specific machines |


### CLEAR Architecture

When you SSH to CLEAR, you don't connect to a single computer—you connect to a cluster:

Login nodes:
- Where you land when you SSH
- For light work only (editing files, compiling, submitting jobs)
- Shared by many users
- DO NOT run heavy computations here

Compute nodes:
- Where heavy jobs actually run
- Accessed via job scheduler (SLURM)
- More powerful than login nodes
- Your jobs get dedicated resources

Storage:
- Your home directory is networked (accessible from all nodes)
- Changes made on one node appear everywhere
- Backed up regularly
- Has quota limits

The workflow: Log into login node → submit job to scheduler → job runs on compute node → results appear in your home directory.

### What Happens When You SSH

Understanding this is crucial:

```bash
$ ssh username@ssh.clear.rice.edu
```

Behind the scenes:
1. Your laptop opens a secure TCP connection to the server
2. Server authenticates you (password or SSH key)
3. Server starts a new shell process (bash/zsh) for you
4. That shell runs ON THE SERVER (not your laptop)
5. Your terminal displays input/output over the encrypted connection

Critical insight: The shell process lives on the server. When your connection drops, the shell dies, and everything running in it dies too.

This is why closing your laptop kills your jobs. The shell process terminates, and all child processes (your running scripts) terminate with it.

Unless you use tmux.

## Part 2: SSH Deep Dive

### Basic SSH Review

The basic SSH command:

```bash
ssh username@hostname
```

Example:
```bash
ssh netid@ssh.clear.rice.edu
```

This works, but it's tedious. Let's make it better.

### SSH Config File

The SSH config file (`~/.ssh/config`) lets you define shortcuts and settings for different servers.

Create `~/.ssh/config`:

```ssh
Host clear
    HostName ssh.clear.rice.edu
    User your_netid
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

Now instead of typing `ssh netid@ssh.clear.rice.edu`, you just type:

```bash
ssh clear
```

Config file options explained:

- `Host`: The shortcut name you'll use
- `HostName`: The actual server address
- `User`: Your username on that server
- `ServerAliveInterval`: Send keepalive every 60 seconds
- `ServerAliveCountMax`: Disconnect after 3 failed keepalives

The keepalive options prevent your connection from timing out during idle periods.

### Advanced SSH Config

Multiple servers:

```ssh
Host clear
    HostName ssh.clear.rice.edu
    User netid
    ServerAliveInterval 60

Host davinci
    HostName davinci.rice.edu
    User netid
    ServerAliveInterval 60

Host lab
    HostName lab.cs.rice.edu
    User netid
    ProxyJump clear
```

ProxyJump (jump host):
- Connects to `lab` by first connecting through `clear`
- Useful for servers only accessible from Rice network
- Syntax: `ProxyJump intermediate_host`

Wildcards for common settings:

```ssh
Host *.rice.edu
    User netid
    ServerAliveInterval 60
    
Host clear
    HostName ssh.clear.rice.edu
    
Host davinci
    HostName davinci.rice.edu
```

This applies `User` and `ServerAliveInterval` to all Rice hosts.

### Port Forwarding: The Game Changer

Port forwarding lets you access services running on the server as if they were running on your laptop.

Local port forwarding (most common):

Basic syntax:
```bash
ssh -L local_port:localhost:remote_port user@host
```

Example - Jupyter notebook:

On server:
```bash
jupyter notebook --no-browser --port=8888
```

On laptop (new terminal):
```bash
ssh -L 8888:localhost:8888 clear
```

In browser:
```
http://localhost:8888
```

What's happening:
- Server runs Jupyter on port 8888
- SSH forwards your laptop's port 8888 to server's port 8888
- You access localhost:8888 on your laptop
- Traffic goes through SSH tunnel to server
- Server does computation, laptop just displays

More examples:

Web development server:
```bash
# Server runs Flask on port 5000
ssh -L 5000:localhost:5000 clear
```

Database access:
```bash
# Server runs PostgreSQL on port 5432
ssh -L 5432:localhost:5432 clear
```

Multiple ports:
```bash
ssh -L 8888:localhost:8888 -L 5000:localhost:5000 clear
```

Add to SSH config for convenience:

```ssh
Host clear-jupyter
    HostName ssh.clear.rice.edu
    User netid
    LocalForward 8888 localhost:8888

Host clear-web
    HostName ssh.clear.rice.edu
    User netid
    LocalForward 8080 localhost:8080
```

Now:
```bash
ssh clear-jupyter
# Automatically sets up port forwarding
```

Remote port forwarding (less common):

Makes your laptop's service accessible from server.

```bash
ssh -R 8080:localhost:3000 clear
```

What's happening:
- Your laptop runs service on port 3000
- Server's port 8080 forwards to your laptop's port 3000
- Useful for webhooks, testing, etc.

Dynamic port forwarding (SOCKS proxy):

Turn server into proxy for all connections:

```bash
ssh -D 8080 clear
```

Configure browser to use SOCKS proxy at `localhost:8080`.

All browser traffic now goes through CLEAR. Useful for:
- Accessing resources only available from Rice network
- Accessing journal articles
- Internal tools
- VPN alternative

### SSH Connection Management

Keep connections alive:

```ssh
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    TCPKeepAlive yes
```

Reuse connections (multiplexing):

```ssh
Host *
    ControlMaster auto
    ControlPath ~/.ssh/control-%r@%h:%p
    ControlPersist 10m
```

Benefits:
- First connection creates a master
- Subsequent connections reuse it (much faster)
- Persists 10 minutes after last connection closes

Compression for slow connections:

```ssh
Host slow-server
    Compression yes
```

### SSH Security Best Practices

Never share your private key. Ever.

Use passphrases on private keys.

Use strong key types:
- Ed25519 (preferred, modern)
- RSA 4096-bit (if Ed25519 not supported)
- Avoid DSA, ECDSA

Protect private key permissions:
```bash
chmod 600 ~/.ssh/id_ed25519
```

Use different keys for different purposes:
```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_clear -C "clear access"
ssh-keygen -t ed25519 -f ~/.ssh/id_github -C "github"
```

Specify key in config:
```ssh
Host clear
    IdentityFile ~/.ssh/id_clear
    
Host github.com
    IdentityFile ~/.ssh/id_github
```

## Part 3: tmux - Terminal Multiplexer

### The Problem tmux Solves

Without tmux:
```bash
$ ssh clear
$ python long_script.py
# Running...
# WiFi drops / laptop closes
# Connection dies
# Script killed
# All progress lost
```

With tmux:
```bash
$ ssh clear
$ tmux new -s work
$ python long_script.py
# Running...
# WiFi drops / laptop closes
# Connection dies
# BUT script still running on server
$ ssh clear
$ tmux attach -s work
# Back to running script!
```

tmux creates a persistent session on the server that survives disconnections.

### What is tmux?

tmux = terminal multiplexer

Think of it as:
- Window manager for your terminal
- Virtual desktop that lives on the server
- Workspace that persists when you disconnect

Three hierarchical concepts:

Session:
- Top level container
- Like a "project" or "workspace"
- Persists on server
- Can have multiple windows

Window:
- Like a tab in your browser
- One per task/file
- Can split into panes

Pane:
- Split window (side-by-side or stacked)
- Each runs its own shell
- Navigate between them

Hierarchy: Session → Windows → Panes

### tmux Basics

Install tmux (if needed):
```bash
# Usually already installed on servers
sudo apt install tmux   # Ubuntu
sudo yum install tmux   # Fedora/RHEL
brew install tmux       # macOS
```

Create a session:
```bash
tmux new -s session_name
```

If you don't name it, tmux assigns a number (0, 1, 2...).

Always name your sessions descriptively:
```bash
tmux new -s ml-training
tmux new -s data-pipeline
tmux new -s debugging
```

Detach from session (keep it running):
```
Ctrl-b d
```

Explanation:
- `Ctrl-b` is the "prefix" key (all tmux commands start with this)
- `d` means detach

List sessions:
```bash
tmux ls
```

Output:
```
ml-training: 3 windows (created Tue Jan 21 10:30:15 2025)
data-pipeline: 1 window (created Tue Jan 21 11:15:42 2025)
```

Attach to session:
```bash
tmux attach -t session_name
```

Or shorthand:
```bash
tmux a -t session_name
```

Kill session (when done):
```bash
tmux kill-session -t session_name
```

Kill all sessions:
```bash
tmux kill-server
```

### tmux Key Bindings

All tmux commands start with the prefix: `Ctrl-b`

Press `Ctrl-b`, release, then press the command key.

Essential commands:

| Keys | Action |
|------|--------|
| `Ctrl-b ?` | Show all key bindings |
| `Ctrl-b d` | Detach from session |
| `Ctrl-b c` | Create new window |
| `Ctrl-b ,` | Rename current window |
| `Ctrl-b n` | Next window |
| `Ctrl-b p` | Previous window |
| `Ctrl-b 0-9` | Switch to window by number |
| `Ctrl-b w` | List windows |
| `Ctrl-b &` | Kill current window |
| `Ctrl-b %` | Split pane vertically |
| `Ctrl-b "` | Split pane horizontally |
| `Ctrl-b arrow` | Navigate between panes |
| `Ctrl-b o` | Cycle through panes |
| `Ctrl-b x` | Kill current pane |
| `Ctrl-b z` | Toggle pane zoom (fullscreen) |
| `Ctrl-b {` | Move pane left |
| `Ctrl-b }` | Move pane right |
| `Ctrl-b space` | Cycle through layouts |

Session management:

| Keys | Action |
|------|--------|
| `Ctrl-b s` | List sessions |
| `Ctrl-b $` | Rename session |
| `Ctrl-b (` | Previous session |
| `Ctrl-b )` | Next session |

Helpful features:

| Keys | Action |
|------|--------|
| `Ctrl-b [` | Enter scroll/copy mode (use arrows/page up/down) |
| `q` | Exit scroll mode |
| `Ctrl-b :` | Enter command mode |

### Creating a Research Workspace

Typical layout:

```
┌─────────────────────┬──────────────────┐
│                     │                  │
│   Editor            │   Run Script     │
│   (vim/nano)        │   (output)       │
│                     │                  │
├─────────────────────┴──────────────────┤
│   Monitor (htop, logs, tail)           │
└────────────────────────────────────────┘
```

Create this layout:

```bash
# Create session
tmux new -s work

# Split vertically (left and right)
Ctrl-b %

# Select left pane
Ctrl-b arrow-left

# Open editor
vim script.py

# Select right pane
Ctrl-b arrow-right

# Split horizontally (top and bottom)
Ctrl-b "

# Top-right: run script
python script.py

# Bottom: monitor
Ctrl-b arrow-down
htop
```

Or use tmux commands:

```bash
tmux new -s work
tmux split-window -h
tmux split-window -v
tmux select-pane -t 0
```

### tmux Configuration

Create `~/.tmux.conf`:

```bash
# Change prefix to Ctrl-a (easier to reach than Ctrl-b)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Better split commands (| and -)
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Vim-like pane navigation
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Resize panes with vim-like keys
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Enable mouse support
set -g mouse on

# Start window numbering at 1 (not 0)
set -g base-index 1
setw -g pane-base-index 1

# Renumber windows when one is closed
set -g renumber-windows on

# Increase scrollback buffer
set -g history-limit 10000

# Don't rename windows automatically
set -g allow-rename off

# Reload config file
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Better colors
set -g default-terminal "screen-256color"

# Status bar customization
set -g status-position bottom
set -g status-bg colour234
set -g status-fg colour137
set -g status-left '#[fg=colour233,bg=colour245,bold] #S '
set -g status-right '#[fg=colour233,bg=colour245,bold] %Y-%m-%d %H:%M '

# Pane border
set -g pane-border-style fg=colour238
set -g pane-active-border-style fg=colour51

# Window status
setw -g window-status-current-style fg=colour51,bg=colour238,bold
```

Reload config:
```bash
tmux source-file ~/.tmux.conf
```

Or from within tmux:
```
Ctrl-b :source-file ~/.tmux.conf
```

### tmux Workflow Patterns

Pattern 1: One session per project

```bash
tmux new -s thesis
tmux new -s coursework
tmux new -s research
```

Switch between them:
```
Ctrl-b s  # List sessions, choose with arrows
```

Pattern 2: Persistent development environment

```bash
# On server
tmux new -s dev

# Set up panes
Ctrl-b %      # Split vertically
Ctrl-b "      # Split bottom horizontally

# Left: editor
# Top-right: run code
# Bottom: git, tests, etc.

# Detach
Ctrl-b d

# Later, reconnect
tmux attach -t dev
```

Pattern 3: Long-running job monitoring

```bash
tmux new -s training

# Start job with logging
python train_model.py 2>&1 | tee training.log &

# New pane to monitor
Ctrl-b "
tail -f training.log

# Another pane for system resources
Ctrl-b "
htop

# Detach and go to class
Ctrl-b d

# Check later
tmux attach -t training
```

Pattern 4: Pair programming

Person A (creates session):
```bash
ssh clear
tmux new -s pairing
```

Person B (joins session):
```bash
ssh clear
tmux attach -t pairing
```

Both see the same screen, both can type. Great for:
- Debugging together
- Teaching
- Code review
- Technical interviews

### tmux Copy Mode

Scroll back through output:

```
Ctrl-b [        # Enter copy mode
```

In copy mode:
- Arrow keys or vi keys (hjkl) to navigate
- Page Up/Down to scroll
- `/` to search
- `q` to exit

Copy text (vi mode):
```
Ctrl-b [        # Enter copy mode
Space           # Start selection
(move cursor)
Enter           # Copy selection
Ctrl-b ]        # Paste
```

Enable vi mode in `~/.tmux.conf`:
```bash
setw -g mode-keys vi
```

### tmux and vim Together

Both use similar philosophies and keybindings. Configure them to work together.

In `~/.tmux.conf`:
```bash
# Smart pane switching with awareness of vim splits
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|n?vim?x?)(diff)?$'"
bind -n C-h if-shell "$is_vim" "send-keys C-h"  "select-pane -L"
bind -n C-j if-shell "$is_vim" "send-keys C-j"  "select-pane -D"
bind -n C-k if-shell "$is_vim" "send-keys C-k"  "select-pane -U"
bind -n C-l if-shell "$is_vim" "send-keys C-l"  "select-pane -R"
```

Now `Ctrl-h/j/k/l` navigate both vim splits and tmux panes seamlessly.

### tmux Session Scripts

Automate common setups with scripts:

Create `~/bin/dev-session.sh`:

```bash
#!/usr/bin/env bash

SESSION=$1

if [ -z "$SESSION" ]; then
    echo "Usage: $0 SESSION_NAME"
    exit 1
fi

# Create session
tmux new-session -d -s "$SESSION"

# Window 1: Editor
tmux rename-window -t "$SESSION":1 'editor'
tmux send-keys -t "$SESSION":1 'vim' C-m

# Window 2: Terminal
tmux new-window -t "$SESSION":2 -n 'terminal'

# Window 3: Logs
tmux new-window -t "$SESSION":3 -n 'logs'
tmux split-window -h -t "$SESSION":3
tmux send-keys -t "$SESSION":3.1 'htop' C-m

# Select first window
tmux select-window -t "$SESSION":1

# Attach to session
tmux attach-session -t "$SESSION"
```

Usage:
```bash
chmod +x ~/bin/dev-session.sh
~/bin/dev-session.sh myproject
```

### tmux Plugins (Optional)

tmux Plugin Manager (TPM):

Install:
```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

Add to `~/.tmux.conf`:
```bash
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'  # Save/restore sessions
set -g @plugin 'tmux-plugins/tmux-continuum'  # Auto-save sessions

# Initialize TPM (keep at bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

Install plugins:
```
Ctrl-b I  # Capital I
```

Useful plugins:
- `tmux-resurrect`: Save/restore tmux sessions across reboots
- `tmux-continuum`: Automatically save sessions
- `tmux-yank`: Better copy/paste
- `tmux-open`: Open links/files from tmux

## Part 4: The Complete Research Pipeline

### The Workflow

Step 1: Configure SSH (one time)

Create `~/.ssh/config`:
```ssh
Host clear
    HostName ssh.clear.rice.edu
    User netid
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

Generate and copy SSH key:
```bash
ssh-keygen -t ed25519
ssh-copy-id clear
```

Step 2: Connect to server

```bash
ssh clear
```

Step 3: Create persistent session

```bash
tmux new -s experiment
```

Step 4: Set up workspace

```bash
# Split vertically
Ctrl-b %

# Left pane: create/edit script
vim experiment.py

# Right pane: will run the script
# (leave empty for now)

# Create bottom monitoring pane
Ctrl-b "
```

Step 5: Run job with logging

Right pane:
```bash
python experiment.py 2>&1 | tee experiment.log
```

Bottom pane:
```bash
tail -f experiment.log
```

Or monitor system:
```bash
htop
```

Step 6: Detach and disconnect

```
Ctrl-b d
exit
```

Close laptop. Go to class. Get coffee.

Step 7: Reconnect later

```bash
ssh clear
tmux attach -t experiment
```

Everything is still running.

Step 8: Transfer results

On your laptop:
```bash
rsync -avP clear:~/experiment/results.json ./
```

Or entire directory:
```bash
rsync -avP clear:~/experiment/ ./experiment_results/
```

Step 9: Clean up

```bash
# Kill the session when done
tmux kill-session -t experiment

# Or keep it for later reference
```

### Using tee for Logging

`tee` writes output to both stdout and a file:

```bash
python script.py | tee output.log
```

Capture stderr too:
```bash
python script.py 2>&1 | tee output.log
```

Append instead of overwrite:
```bash
python script.py 2>&1 | tee -a output.log
```

Better pattern with timestamps:
```bash
{
    echo "===== Started at $(date) ====="
    python experiment.py
    echo "===== Finished at $(date) ====="
} 2>&1 | tee "experiment_$(date +%Y%m%d_%H%M%S).log"
```

### Monitoring Long-Running Jobs

Watch a log file update:
```bash
tail -f logfile.txt
```

Or with line numbers:
```bash
tail -f logfile.txt | nl
```

Watch command output refresh:
```bash
watch -n 1 'tail -5 progress.log'
```

Monitor last N lines:
```bash
watch -n 1 'ls -lht results/ | head -10'
```

Check if process is still running:
```bash
ps aux | grep python
```

Or:
```bash
pgrep -f experiment.py
```

Monitor resources:
```bash
htop
```

Filter by user:
```bash
htop -u $USER
```

## Part 5: rsync - Smart File Transfer

### Why rsync over scp?

scp (secure copy):
- Copies everything every time
- No progress indicator
- Can't resume interrupted transfers
- No synchronization

rsync:
- Only copies changed files
- Shows progress
- Can resume
- Synchronizes directories
- Handles deletions properly

### Basic rsync Usage

From server to laptop:
```bash
rsync -avP server:~/path/to/file.txt ./
```

From laptop to server:
```bash
rsync -avP ./file.txt server:~/path/to/
```

Flags explained:
- `-a`: Archive mode (preserves permissions, timestamps, symbolic links)
- `-v`: Verbose (show what's being transferred)
- `-P`: Progress + partial (show progress bar, keep partial files for resuming)

### rsync Patterns

Sync entire directory:
```bash
rsync -avP clear:~/project/ ./project_backup/
```

Important: The trailing `/` matters!

```bash
# Syncs contents of project into project_backup
rsync -avP clear:~/project/ ./project_backup/

# Syncs project directory itself into project_backup
rsync -avP clear:~/project ./project_backup/
# Result: ./project_backup/project/...
```

Dry run (see what would happen):
```bash
rsync -avPn clear:~/data/ ./data/
```

The `-n` flag shows what would be transferred without actually doing it.

Exclude patterns:
```bash
rsync -avP --exclude '*.tmp' --exclude '.git' --exclude '__pycache__' \
      clear:~/project/ ./project/
```

Delete on destination:
```bash
rsync -avP --delete clear:~/project/ ./project/
```

This makes destination exactly match source (deletes files not on server).

Bandwidth limit:
```bash
rsync -avP --bw-limit=1000 clear:~/large_file ./
```

Limits to 1000 KB/s.

Compress during transfer:
```bash
rsync -avPz clear:~/data/ ./data/
```

The `-z` flag compresses data during transfer (good for text files, unnecessary for already-compressed files).

### rsync for Backups

Incremental backups with hardlinks:

```bash
#!/usr/bin/env bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="$HOME/backups"
LATEST="$BACKUP_DIR/latest"

mkdir -p "$BACKUP_DIR"

rsync -avP \
      --link-dest="$LATEST" \
      clear:~/project/ \
      "$BACKUP_DIR/$DATE/"

# Update latest symlink
rm -f "$LATEST"
ln -s "$DATE" "$LATEST"
```

This creates incremental backups using hardlinks. Unchanged files are hardlinked (take no extra space).

## Part 6: Server Hygiene

### Check Disk Usage

Your usage:
```bash
du -sh ~/*
```

More detailed:
```bash
du -h ~ | sort -h | tail -20
```

Find large files:
```bash
find ~ -type f -size +100M -exec ls -lh {} \;
```

Or:
```bash
find ~ -type f -size +100M -exec du -h {} \; | sort -h
```

Check quota:
```bash
quota -s
```

On CLEAR:
```bash
fs lq
```

Check filesystem usage:
```bash
df -h
```

Find files by age:
```bash
# Files modified in last 7 days
find ~ -type f -mtime -7

# Files not modified in last 90 days
find ~ -type f -mtime +90
```

### Clean Up

Delete carefully:
```bash
# NEVER do this blindly
rm -rf ~/old_data/*

# DO THIS instead
ls ~/old_data/*
# Verify what you're deleting
rm ~/old_data/specific_file.txt
```

Compress instead of delete:
```bash
tar -czf old_data_$(date +%Y%m%d).tar.gz old_data/
rm -rf old_data/
```

Use trash instead of rm:
```bash
# Install trash-cli
pip install trash-cli --user

# Use trash instead of rm
trash old_file.txt

# Can recover if needed
trash-list
trash-restore
```

Clear cache directories:
```bash
rm -rf ~/.cache/*
rm -rf ~/.local/share/Trash/*
```

### Process Management

List your processes:
```bash
ps aux | grep $USER
```

Find specific process:
```bash
ps aux | grep python
```

Or:
```bash
pgrep -a python
```

Kill process by PID:
```bash
kill 12345
```

Kill by name:
```bash
pkill -f experiment.py
```

Force kill:
```bash
kill -9 12345
```

Or:
```bash
pkill -9 -f experiment.py
```

Kill all your Python processes:
```bash
pkill -u $USER python
```

Find processes using CPU:
```bash
ps aux | sort -nrk 3 | head
```

Find processes using memory:
```bash
ps aux | sort -nrk 4 | head
```

### Background Jobs

Run command in background:
```bash
python script.py &
```

List background jobs:
```bash
jobs
```

Bring job to foreground:
```bash
fg %1
```

Kill background job:
```bash
kill %1
```

Detach running process:
```bash
# Press Ctrl-Z to suspend
# Then:
bg  # Continue in background
disown  # Detach from shell
```

Better: Use tmux or nohup:
```bash
nohup python script.py > output.log 2>&1 &
```

### File Permissions

Check permissions:
```bash
ls -la
```

Make script executable:
```bash
chmod +x script.sh
```

Common permission patterns:
```bash
chmod 755 script.sh    # rwxr-xr-x (owner: read/write/execute, others: read/execute)
chmod 644 file.txt     # rw-r--r-- (owner: read/write, others: read-only)
chmod 700 private.sh   # rwx------ (owner only)
chmod 600 secret.txt   # rw------- (owner read/write only)
```

Recursive:
```bash
chmod -R 755 directory/
```

Change ownership:
```bash
chown user:group file.txt
```

### Shared Server Etiquette

Don't run heavy jobs on login nodes:
- Login nodes are shared by all users
- Heavy computation slows everyone down
- Use job scheduler (SLURM) for heavy work

Check load before running:
```bash
uptime
```

Output: `load average: 5.23, 4.15, 3.87`

If load is high (> number of CPUs), don't add more.

Use `nice` for CPU-heavy tasks:
```bash
nice -n 19 python script.py
```

This gives your process lowest priority.

Monitor your resource usage:
```bash
htop -u $USER
```

Clean up temporary files:
```bash
rm -rf /tmp/mywork_*
```

## Part 7: Reproducibility and Documentation

### Save Environment Information

Create metadata file:
```bash
{
    echo "Experiment run at: $(date)"
    echo "Hostname: $(hostname)"
    echo "User: $(whoami)"
    echo "Working directory: $(pwd)"
    echo "Git commit: $(git rev-parse HEAD 2>/dev/null || echo 'N/A')"
    echo "Python version: $(python --version 2>&1)"
    echo "Pip freeze:"
    pip freeze
} > metadata.txt
```

As a function (add to `.bashrc`):
```bash
save_metadata() {
    local outfile="${1:-metadata.txt}"
    {
        echo "Date: $(date)"
        echo "Host: $(hostname)"
        echo "User: $(whoami)"
        echo "PWD: $(pwd)"
        echo "Git: $(git rev-parse HEAD 2>/dev/null || echo 'not in repo')"
        echo "Python: $(python --version 2>&1)"
        echo "---"
        echo "Environment:"
        pip freeze
    } > "$outfile"
}
```

Usage:
```bash
save_metadata experiment_metadata.txt
```

### Log Everything

Pattern for experiments:
```bash
#!/usr/bin/env bash
# run_experiment.sh

EXPERIMENT_DIR="experiment_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$EXPERIMENT_DIR"
cd "$EXPERIMENT_DIR"

# Save metadata
{
    echo "Started: $(date)"
    echo "Host: $(hostname)"
    echo "Git: $(git rev-parse HEAD)"
    python --version
} > metadata.txt

# Run experiment with logging
python ../experiment.py \
    --config config.json \
    2>&1 | tee experiment.log

# Save completion info
echo "Completed: $(date)" >> metadata.txt
```
