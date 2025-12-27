---
title: "The Art of Lazy Programming"
sub_title: "Week 3 — SSH, tmux, and the Remote Research Pipeline"
author: "SeniorMars"
---

<!--
speaker_note: |
  Time budget (60 min):
    0–5   cold open: the laptop is a lie
    5–15  understanding servers (what/why/Rice landscape)
    15–28 SSH deep dive (config, keys, port forwarding)
    28–43 tmux: your persistent lab bench
    43–55 complete workflow demo (the research pipeline)
    55–60 homework + best practices

  Demo prep:
    - Have CLEAR access working
    - Prepare long-running script (progress_sim.py)
    - Have sample data files ready
    - Test port forwarding beforehand
    - Practice tmux choreography
-->

<!-- jump_to_middle -->

Your laptop is a lie
===

<!-- pause -->

Everything you've done so far—Week 0, Week 1, Week 2—has been on a server.

<!-- pause -->

CLEAR isn't "your computer in the cloud."

<!-- pause -->

**It's a shared computer**

<!-- pause -->

**Your laptop?** Just a window into the real machine.

<!-- end_slide -->

The problem
===

You're working on an important experiment.

<!-- pause -->

It takes 6 hours to run.

<!-- pause -->

**What happens when:**
- Your laptop dies?
- WiFi drops?
- You close the lid?
- You want to go home?

<!-- pause -->

**Your job dies with your connection.**

<!-- pause -->

**Today: We fix this.**

<!-- end_slide -->

What you'll learn
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**The Tools:**
- SSH (properly configured)
- tmux (persistent sessions)
- rsync (smart file transfer)
- Port forwarding (access remote services)

<!-- column: 1 -->

**The Pattern:**
1. Connect to server
2. Create persistent workspace
3. Run long jobs
4. Disconnect safely
5. Reconnect anytime
6. Transfer results

<!-- reset_layout -->

<!-- pause -->

**By the end:** You'll run experiments that survive laptop death.

<!-- end_slide -->

Agenda
===

<!-- incremental_lists: true -->

1. **What servers really are** (not magic)
2. **SSH deep dive** (config, keys, tunneling)
3. **tmux** (your persistent lab bench)
4. **Complete workflow demo** (the research pipeline)
5. **Server hygiene** (don't be "that user")

<!-- incremental_lists: false -->

<!-- pause -->

Let's start with understanding what we're working with.

<!-- end_slide -->

<!-- jump_to_middle -->

Part 1
===

**Understanding Servers**

*They're just computers you don't sit at*

<!-- end_slide -->

What is a server?
===

**A server is:**
- A computer optimized for specific tasks
- Always on (no sleep mode)
- Often shared by many users
- Usually has no monitor/keyboard/mouse

<!-- pause -->

**It's NOT:**
- Magic
- "The cloud" (someone else's computer)
- Fundamentally different from your laptop

<!-- pause -->

**Think of it as:** A really powerful computer in a closet somewhere that you access remotely.

<!-- end_slide -->

Why use servers?
===

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Power:**
- More CPU cores
- More RAM
- Better GPUs
- Faster storage

<!-- pause -->

**Reliability:**
- 24/7 uptime
- Professional backups
- Redundant power
- Climate controlled

<!-- column: 1 -->

**Shared Resources:**
- Expensive hardware
- Large datasets
- Software licenses
- Collaboration

<!-- pause -->

**Accessibility:**
- Work from anywhere
- Switch devices
- Share with team
- Always available

<!-- reset_layout -->

<!-- end_slide -->

The Rice server landscape
===

**CLEAR (Cluster for Learning, Exploration, and Research)**

<!-- pause -->

What you've been using:
- Linux cluster for teaching and research
- Fedora-based
- Shared by all Rice students
- Your home directory: networked storage

<!-- pause -->

**Other servers you might encounter:**

| Server | Purpose |
|--------|---------|
| DAVinCI | High-performance computing (HPC) |
| Lab servers | Department-specific resources |
| nfs.rice.edu | File storage |
| Personal VPS | Your own cloud instance |

<!-- end_slide -->

CLEAR architecture
===

When you `ssh clear`, you're connecting to:

<!-- pause -->

**Login nodes** - Where you land, for light work only

<!-- pause -->

**Compute nodes** - Where heavy jobs actually run

<!-- pause -->

**Storage** - Your home directory (networked across all nodes)

<!-- pause -->

**Why this matters:**
- Don't run heavy jobs on login nodes
- Your files are accessible from any node
- Jobs can run on any compute node

<!-- pause -->

**The workflow:** Login node → submit job → compute node runs it

<!-- end_slide -->

What happens when you SSH
===

```bash
$ ssh username@ssh.clear.rice.edu
```

<!-- pause -->

**Behind the scenes:**
1. Your computer opens a secure connection
2. Server authenticates you (password or key)
3. Server starts a shell process for you
4. That shell runs on the server (not your laptop)
5. Your terminal just displays the input/output

<!-- pause -->

**Critical insight:** The shell is running on the server.

**That's why:** When you disconnect, the shell dies, and your jobs die with it.

<!-- pause -->

**Unless...** you use tmux.

<!-- speaker_note: |
  This is the key concept. Make sure they understand the process is remote.
-->

<!-- end_slide -->

<!-- jump_to_middle -->

Part 2
===

**SSH Deep Dive**

*Stop typing passwords*

<!-- end_slide -->

SSH config: Quality of life
===

**Currently, you type:**

```bash
ssh username@ssh.clear.rice.edu
```

<!-- pause -->

**Every. Single. Time.**

<!-- pause -->

**Better way:** SSH config file

Create `~/.ssh/config`:

```ssh
Host clear
    HostName ssh.clear.rice.edu
    User your_netid
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

<!-- pause -->

**Now just type:**

```bash
ssh clear
```

<!-- pause -->

**Magic.**

<!-- end_slide -->

SSH config: More tricks
===

**Multiple servers:**

```ssh
Host clear
    HostName ssh.clear.rice.edu
    User netid
    
Host davinci
    HostName davinci.rice.edu
    User netid
    
Host lab
    HostName lab.cs.rice.edu
    User netid
    ProxyJump clear
```

<!-- pause -->

**ProxyJump?** Connect to `lab` by going through `clear` first.

Useful for servers only accessible from Rice network.

<!-- end_slide -->

SSH keys: Never type passwords
===

**Currently:** Type password every connection + DUO every time

<!-- pause -->

**Better:** SSH keys (cryptographic authentication)

<!-- pause -->

**How it works:**
1. Generate key pair (public + private)
2. Copy public key to server
3. Server recognizes your private key
4. No password needed

<!-- pause -->

**Generate keys:**

```bash
ssh-keygen -t ed25519 -C "your_email@rice.edu"
```

Press Enter for default location, optionally set passphrase.

<!-- end_slide -->

Copy key to server
===

**Easy way:**

```bash
ssh-copy-id clear
```

<!-- pause -->

**Manual way:**

```bash
cat ~/.ssh/id_ed25519.pub | ssh clear "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

<!-- pause -->

**Test it:**

```bash
ssh clear
```

<!-- pause -->

**If it works:** No password prompt!

<!-- pause -->

**Security note:** Protect your private key like a password.

<!-- end_slide -->

SSH agent: Don't type passphrase repeatedly
===

If you set a passphrase on your key (good security):

```bash
# Start SSH agent
eval "$(ssh-agent -s)"

# Add key once per session
ssh-add ~/.ssh/id_ed25519
```

<!-- pause -->

**Now:** Type passphrase once, use key all session.

<!-- pause -->

**Make it permanent** (add to `~/.bashrc`):

```bash
# Start SSH agent if not running
if ! pgrep -u "$USER" ssh-agent > /dev/null; then
    ssh-agent > ~/.ssh-agent-env
fi
if [[ -f ~/.ssh-agent-env ]]; then
    source ~/.ssh-agent-env > /dev/null
fi
```

<!-- end_slide -->

Port forwarding: The game changer
===

**Problem:** You're running a web server on CLEAR (port 8080)

You want to view it in your laptop's browser.

<!-- pause -->

**Solution:** Port forwarding

<!-- pause -->

**Local port forwarding:**

```bash
ssh -L 8080:localhost:8080 clear
```

<!-- pause -->

**What this does:**
- Forwards your laptop's port 8080
- To the server's port 8080
- Through the SSH tunnel

<!-- pause -->

**Now:** Open `localhost:8080` in your browser → accesses CLEAR's port 8080

<!-- end_slide -->

Port forwarding: Jupyter example
===

**Scenario:** Running Jupyter notebook on CLEAR

<!-- pause -->

**On CLEAR:**

```bash
jupyter notebook --no-browser --port=8888
```

<!-- pause -->

**In a new terminal on your laptop:**

```bash
ssh -L 8888:localhost:8888 clear
```

<!-- pause -->

**In your browser:**

```
localhost:8888
```

<!-- pause -->

**Result:** Jupyter running on CLEAR, viewing on laptop.

**Power of this:** Server does the computation, laptop just displays.

<!-- end_slide -->

Port forwarding in SSH config
===

**Add to `~/.ssh/config`:**

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

<!-- pause -->

**Now:**

```bash
ssh clear-jupyter
# Automatically forwards port 8888
```

<!-- pause -->

**This is how you run:**
- Jupyter notebooks
- Web servers
- Databases
- Any service with a port

<!-- end_slide -->

Remote port forwarding
===

**Opposite direction:** Make your laptop's port accessible from server.

<!-- pause -->

**Use case:** Testing webhook that calls your local development server.

```bash
ssh -R 8080:localhost:3000 clear
```

<!-- pause -->

**What this does:**
- Server's port 8080 → your laptop's port 3000

<!-- pause -->

**Less common, but powerful when you need it.**

<!-- end_slide -->

Dynamic port forwarding (SOCKS proxy)
===

**Make server act as proxy for all connections:**

```bash
ssh -D 8080 clear
```

<!-- pause -->

**Configure browser to use SOCKS proxy at `localhost:8080`**

<!-- pause -->

**Now:** All browser traffic goes through CLEAR.

<!-- pause -->

**Use case:** Access resources only available from Rice network.

<!-- pause -->

**Advanced, but incredibly useful for accessing journals, internal sites, etc.**

<!-- end_slide -->

<!-- jump_to_middle -->

Part 3
===

**tmux: Your Persistent Lab Bench**

*Disconnect without fear*

<!-- end_slide -->

The problem tmux solves
===

**Without tmux:**

```bash
$ ssh clear
$ python long_experiment.py
# Running...
# WiFi drops
# Connection lost
# Job killed
# 😭
```

<!-- pause -->

**With tmux:**

```bash
$ ssh clear
$ tmux new -s experiment
$ python long_experiment.py
# Running...
# WiFi drops
# Connection lost
# Job still running
# 😎
```

<!-- end_slide -->

What is tmux?
===

**tmux = terminal multiplexer**

<!-- pause -->

Think of it as:
- Window manager for your terminal
- Workspace that persists on the server
- Virtual desktop that survives disconnections

<!-- pause -->

**Three concepts:**

| Concept | Description |
|---------|-------------|
| **Session** | Workspace (like a project) |
| **Window** | Tab within a session |
| **Pane** | Split window (side-by-side views) |

<!-- end_slide -->

tmux basics
===

**Create session:**

```bash
tmux new -s mysession
```

<!-- pause -->

**Detach (disconnect but keep running):**

```
Ctrl-b d
```

<!-- pause -->

**List sessions:**

```bash
tmux ls
```

<!-- pause -->

**Reattach:**

```bash
tmux attach -t mysession
```

<!-- pause -->

**That's it.** You now have persistent sessions.

<!-- end_slide -->

tmux key bindings
===

**All commands start with:** `Ctrl-b` (the prefix)

<!-- pause -->

**Essential commands:**

| Keys | Action |
|------|--------|
| `Ctrl-b c` | Create new window |
| `Ctrl-b n` | Next window |
| `Ctrl-b p` | Previous window |
| `Ctrl-b %` | Split pane vertically |
| `Ctrl-b "` | Split pane horizontally |
| `Ctrl-b arrow` | Navigate between panes |
| `Ctrl-b d` | Detach session |
| `Ctrl-b x` | Kill pane |

<!-- pause -->

**Mnemonic:** c=create, n=next, p=previous, d=detach

<!-- end_slide -->

The research workspace layout
===

**Typical setup:**

```
┌─────────────────┬──────────────┐
│                 │              │
│   Vim           │  Run script  │
│   (code)        │  (output)    │
│                 │              │
├─────────────────┴──────────────┤
│   Monitor (htop, logs, etc.)   │
└────────────────────────────────┘
```

<!-- pause -->

**How to create:**

```bash
tmux new -s work
Ctrl-b %        # Split vertically
Ctrl-b "        # Split bottom pane horizontally
```

<!-- pause -->

**Now:** Edit code (left), run it (top right), monitor (bottom)

<!-- end_slide -->

tmux configuration
===

**Create `~/.tmux.conf`:**

```bash
# Remap prefix to Ctrl-a (easier to reach)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Better split commands
bind | split-window -h
bind - split-window -v

# Vim-like pane navigation
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Mouse support
set -g mouse on

# Start windows at 1, not 0
set -g base-index 1

# Reload config
bind r source-file ~/.tmux.conf
```

<!-- pause -->

**Reload:** `Ctrl-b r` (or restart tmux)

<!-- end_slide -->

tmux + vim = power combo
===

**Why they work together:**

<!-- pause -->

1. Both modal/keyboard-driven
2. Both use similar navigation (hjkl)
3. Both customize via text files
4. Both run in terminal

<!-- pause -->

**Workflow:**
- tmux manages sessions and panes
- Vim edits code in one pane
- Script runs in another pane
- Monitor in third pane

<!-- pause -->

**Together:** Complete remote development environment.

No GUI needed. Works over slow connections. Persistent.

<!-- end_slide -->

tmux session management
===

**Name your sessions descriptively:**

```bash
tmux new -s ml-training
tmux new -s data-pipeline
tmux new -s debugging
```

<!-- pause -->

**Switch between sessions:**

```bash
tmux switch -t ml-training
```

<!-- pause -->

**Or use the session chooser:**

```
Ctrl-b s
```

<!-- pause -->

**Kill a session when done:**

```bash
tmux kill-session -t ml-training
```

<!-- pause -->

**List all sessions:**

```bash
tmux ls
```

<!-- end_slide -->

tmux for pair programming
===

**Two people, one terminal:**

<!-- pause -->

**Person A (session owner):**

```bash
tmux new -s pairing
```

<!-- pause -->

**Person B (joins session):**

```bash
ssh clear
tmux attach -t pairing
```

<!-- pause -->

**Now:** Both see the same screen, both can type.

<!-- pause -->

**Great for:**
- Debugging together
- Teaching
- Code review
- Interviews

<!-- end_slide -->

<!-- jump_to_middle -->

Part 4
===

**The Complete Research Pipeline**

*Demo: From connection to results*

<!-- end_slide -->

The scenario
===

You need to:
1. Run an experiment that takes 30 minutes
2. Monitor its progress
3. Go to class while it runs
4. Come back and get results
5. Transfer results to your laptop

<!-- pause -->

**Let's do it live.**

<!-- speaker_note: |
  This is the main demo. Take your time. Show each step clearly.
-->

<!-- end_slide -->

Step 1: Connect with your config
===

**On your laptop:**

```bash
ssh clear
```

<!-- pause -->

**That's it.** No password, no long hostnames.

<!-- pause -->

**You're now on a CLEAR login node.**

Check where you are:

```bash
hostname
pwd
whoami
```

<!-- end_slide -->

Step 2: Create tmux session
===

```bash
tmux new -s experiment
```

<!-- pause -->

**Set up your workspace:**

```bash
# Split vertically
Ctrl-b %

# Left pane: create experiment script
vim experiment.py
```

<!-- pause -->

**Right pane:** Will run the script

<!-- pause -->

**Bottom pane (create it):**

```bash
Ctrl-b "
```

Will monitor logs.

<!-- end_slide -->

Step 3: The experiment script
===

**Create `experiment.py`:**

```python
#!/usr/bin/env python3
import time
import random
import json
from datetime import datetime

results = []
total_steps = 100

for i in range(total_steps):
    # Simulate computation
    time.sleep(0.5)
    
    # Generate fake result
    result = {
        "step": i + 1,
        "value": random.random() * 100,
        "timestamp": datetime.now().isoformat()
    }
    results.append(result)
    
    # Print progress
    progress = (i + 1) / total_steps * 100
    print(f"Progress: {progress:.1f}% - Step {i+1}/{total_steps}")
    
    # Write incremental log
    with open("progress.log", "a") as f:
        f.write(f"Step {i+1} completed\n")

# Save final results
with open("results.json", "w") as f:
    json.dump(results, f, indent=2)

print("Experiment complete!")
```

<!-- end_slide -->

Step 4: Run with logging
===

**Top-right pane:**

```bash
python3 experiment.py | tee output.log
```

<!-- pause -->

**What `tee` does:**
- Shows output on screen
- AND writes to file

<!-- pause -->

**Bottom pane (monitor logs):**

```bash
tail -f progress.log
```

<!-- pause -->

**Now you see:**
- Code (left)
- Running output (top right)
- Progress log (bottom)

<!-- speaker_note: |
  Show all three panes working together.
-->

<!-- end_slide -->

Step 5: Detach and disconnect
===

**Experiment is running. Time for class.**

<!-- pause -->

**Detach from tmux:**

```
Ctrl-b d
```

<!-- pause -->

**Exit SSH:**

```bash
exit
```

<!-- pause -->

**Close your laptop. Go to class.**

<!-- pause -->

**The experiment keeps running on the server.**

<!-- end_slide -->

Step 6: Reconnect later
===

**After class, back at your laptop:**

```bash
ssh clear
tmux attach -t experiment
```

<!-- pause -->

**Boom.** Right back where you left off.

<!-- pause -->

**Check if it's done:**

```bash
# In a pane
ls -lh results.json
```

<!-- pause -->

**There's your results file.**

<!-- end_slide -->

Step 7: Transfer results
===

**Don't use `scp` for directories. Use `rsync`.**

<!-- pause -->

**On your laptop (new terminal):**

```bash
rsync -avP clear:~/experiment/results.json ./
```

<!-- pause -->

**What the flags mean:**
- `-a` - Archive mode (preserves everything)
- `-v` - Verbose (show what's happening)
- `-P` - Progress + partial (resume if interrupted)

<!-- pause -->

**Transfer entire directories:**

```bash
rsync -avP clear:~/experiment/ ./experiment/
```

<!-- pause -->

**The trailing `/` matters!**

<!-- end_slide -->

rsync tips
===

**Sync to server:**

```bash
rsync -avP ./local_data/ clear:~/remote_data/
```

<!-- pause -->

**Sync from server:**

```bash
rsync -avP clear:~/remote_data/ ./local_data/
```

<!-- pause -->

**Dry run (see what would happen):**

```bash
rsync -avPn clear:~/data/ ./data/
```

<!-- pause -->

**Exclude files:**

```bash
rsync -avP --exclude '*.tmp' --exclude '.git' ./data/ clear:~/data/
```

<!-- pause -->

**rsync is smart:** Only transfers changed files.

<!-- end_slide -->

Better: Using tee for everything
===

**Capture all output:**

```bash
python experiment.py 2>&1 | tee full_log.txt
```

<!-- pause -->

**What `2>&1` does:** Redirect stderr to stdout (captures errors too)

<!-- pause -->

**Even better pattern:**

```bash
{
    date
    echo "Starting experiment"
    python experiment.py
    echo "Experiment completed"
    date
} 2>&1 | tee experiment_$(date +%Y%m%d_%H%M%S).log
```

<!-- pause -->

**Now:** Timestamped log file with complete record.

<!-- end_slide -->

Monitoring long jobs
===

**While job runs, monitor resources:**

<!-- pause -->

**CPU/Memory:**

```bash
htop
```

<!-- pause -->

**Disk usage:**

```bash
du -sh *
df -h
```

<!-- pause -->

**Watch a file update:**

```bash
watch -n 1 'tail -5 progress.log'
```

<!-- pause -->

**Process status:**

```bash
ps aux | grep python
```

<!-- end_slide -->

<!-- jump_to_middle -->

Part 5
===

**Server Hygiene**

*Don't be "that user"*

<!-- end_slide -->

Check your disk usage
===

**Shared servers have quotas.**

<!-- pause -->

**Check your usage:**

```bash
du -sh ~/*
```

<!-- pause -->

**Or more detailed:**

```bash
du -h ~ | sort -h | tail -20
```

<!-- pause -->

**Check total quota:**

```bash
quota -s
```

Or on CLEAR:

```bash
fs lq
```

<!-- end_slide -->

Clean up after yourself
===

**Find large files:**

```bash
find ~ -type f -size +100M
```

<!-- pause -->

**Find old files:**

```bash
find ~ -type f -mtime +90
```

<!-- pause -->

**Delete safely:**

```bash
# Don't do this
rm -rf ~/data/*

# Do this
ls ~/data/*
# Verify what you're deleting
rm ~/data/specific_file.txt
```

<!-- pause -->

**Compress instead of delete:**

```bash
tar -czf old_data.tar.gz old_data/
rm -rf old_data/
```

<!-- end_slide -->

Kill your jobs
===

**When you're done, kill processes:**

```bash
# Find your processes
ps aux | grep $USER

# Kill by PID
kill 12345

# Kill by name
pkill -f python
```

<!-- pause -->

**Kill all your Python processes:**

```bash
pkill -u $USER python
```

<!-- pause -->

**Force kill (if gentle kill doesn't work):**

```bash
kill -9 12345
```

<!-- pause -->

**Check they're actually dead:**

```bash
ps aux | grep $USER
```

<!-- end_slide -->

Respect shared resources
===

**Don't run heavy jobs on login nodes:**

<!-- pause -->

```bash
# Bad (login node)
ssh clear
python heavy_computation.py
```

<!-- pause -->

```bash
# Good (submit to compute node)
ssh clear
sbatch job_script.sh
```

<!-- pause -->

**On CLEAR, use the job scheduler for heavy work.**

We'll cover this more in later weeks.

<!-- end_slide -->

File permissions
===

**Make scripts executable:**

```bash
chmod +x script.sh
```

<!-- pause -->

**Check permissions:**

```bash
ls -la
```

<!-- pause -->

**Common permission patterns:**

```bash
chmod 755 script.sh    # rwxr-xr-x (executable by all)
chmod 644 data.txt     # rw-r--r-- (readable by all)
chmod 700 private.sh   # rwx------ (only you)
```

<!-- pause -->

**Mnemonic:** 7=rwx, 6=rw-, 5=r-x, 4=r--

<!-- end_slide -->

Reproducibility checkpoint
===

**Save environment info with results:**

```bash
{
    echo "Experiment run at: $(date)"
    echo "Git commit: $(git rev-parse HEAD)"
    echo "Python version: $(python --version)"
    echo "Hostname: $(hostname)"
    echo "User: $(whoami)"
} > metadata.txt
```

<!-- pause -->

**Better yet, make it a function:**

```bash
save_metadata() {
    cat > metadata.txt << EOF
Date: $(date)
Git: $(git rev-parse HEAD 2>/dev/null || echo "not in git repo")
Python: $(python --version 2>&1)
Host: $(hostname)
User: $(whoami)
Working dir: $(pwd)
EOF
}
```

<!-- pause -->

**Add to your `.bashrc`, call before every experiment.**

<!-- end_slide -->

<!-- jump_to_middle -->

Best Practices Summary
===

<!-- end_slide -->

The remote research pattern
===

**1. Configure once:**

```bash
# ~/.ssh/config
Host clear
    HostName ssh.clear.rice.edu
    User netid
    IdentityFile ~/.ssh/id_ed25519
```

<!-- pause -->

**2. Connect:**

```bash
ssh clear
```

<!-- pause -->

**3. Create persistent workspace:**

```bash
tmux new -s project
```

<!-- end_slide -->

The remote research pattern (cont.)
===

**4. Set up environment:**

```bash
# Left pane: code
vim experiment.py

# Right pane: run
python experiment.py | tee log.txt

# Bottom pane: monitor
tail -f progress.log
```

<!-- pause -->

**5. Detach safely:**

```
Ctrl-b d
exit
```

<!-- pause -->

**6. Reconnect anytime:**

```bash
ssh clear
tmux attach -t project
```

<!-- end_slide -->

The remote research pattern (cont.)
===

**7. Transfer results:**

```bash
rsync -avP clear:~/project/results/ ./results/
```

<!-- pause -->

**8. Clean up:**

```bash
# On server
tmux kill-session -t project
rm -rf temp_files/
pkill -f python
```

<!-- pause -->

**This pattern works for:**
- Experiments
- Data processing
- Long compilations
- Training ML models
- Any long-running task

<!-- end_slide -->

Pro tips
===

**Name things descriptively:**

```bash
tmux new -s ml-training-$(date +%Y%m%d)
```

<!-- pause -->

**Use screen recorder:**

```bash
script -a session.log
# All terminal output saved
```

<!-- pause -->

**Automate common setups:**

```bash
# setup_workspace.sh
tmux new -s $1 -d
tmux send-keys -t $1 'vim' C-m
tmux split-window -t $1 -h
tmux split-window -t $1 -v
tmux attach -t $1
```

<!-- pause -->

**Run it:**

```bash
./setup_workspace.sh myproject
```

<!-- end_slide -->

