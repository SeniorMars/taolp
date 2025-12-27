# Week 3 Homework - Your First VPS

**Time estimate**: 1.5 hours maximum

**Goal**: Create your own cloud server, configure SSH properly, run a persistent web service with tmux, and prove it survives disconnections.

```admonish
Don't worry, I am also making a video walkthrough of this homework!
```

---

## Why This Matters

You've been using CLEAR (a shared server). Now you'll create and configure your own Virtual Private Server (VPS). This is how you'd deploy a real web application, run experiments on cloud hardware, or set up your own development environment.

By the end, you'll have:
- Your own cloud server (that you can recreate anytime)
- SSH configured properly
- A web server running persistently with tmux
- Understanding of how "the cloud" actually works

---

## Important Notes

**Cost**: DigitalOcean gives students $200 in free credits (valid 1 year). This homework uses ~$0.10 of credit.

**Cleanup**: DELETE YOUR DROPLET when done to save credits for future projects.

**Time limit**: If any part takes more than estimated time, ask for help.

---

## Submission Requirements

Create a directory `week3-[netid]/` containing:

**Required files:**
```
week3-netid/
├── answers.md           # Answers to questions throughout
├── browser_screenshot.png    # Your web server in browser
├── tmux_screenshot.png      # tmux session with web server running
└── deleted_screenshot.png   # Proof you deleted the droplet
```

**Optional:**
```
└── notes.md            # What broke, how you fixed it
```

---

## Exercise 1: Get DigitalOcean Student Credit (10 minutes)

I'll have you do this before class so you have credit ready.

### Step 1: GitHub Student Pack

1. Go to [https://education.github.com/pack](https://education.github.com/pack)
2. Click "Get your Pack"
3. Sign in with your GitHub account
4. Verify with your Rice email (@rice.edu)
5. Wait for approval (usually instant, sometimes 1-2 days)

If not instant, you can continue with the 60-day free trial and apply credits later.

### Step 2: Activate DigitalOcean Credit

1. Once approved, find DigitalOcean in the pack
2. Click "Get access to DigitalOcean"
3. Create a DigitalOcean account (or sign in)
4. Apply the $200 credit

Verify you have credit:
1. Go to https://cloud.digitalocean.com/
2. Click your profile (top right) → Billing
3. You should see $200 credit


---

## Exercise 2: Generate SSH Key (5 minutes)

**If you already have an SSH key from Week 3 notes, skip to Step 3.**

### Step 1: Check for Existing Key

```bash
ls ~/.ssh/id_*.pub
```

If you see `id_ed25519.pub` or `id_rsa.pub`, you already have a key. Skip to Step 3.

### Step 2: Generate New Key

```bash
ssh-keygen -t ed25519 -C "your_rice_email@rice.edu"
```

- Press Enter for default location
- Enter passphrase (optional but recommended)
- Press Enter again

### Step 3: Copy Public Key

Display your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output (starts with `ssh-ed25519`).

**Question 1**: In `answers.md`, explain what an SSH key is and why it's better than passwords.

---

## Exercise 3: Create Your Droplet (10 minutes)

### Step 1: Create New Droplet

1. Log into [https://cloud.digitalocean.com/](https://cloud.digitalocean.com/)
2. Click "Create" → "Droplets"

### Step 2: Choose Configuration

**Image**: Ubuntu 24.04 LTS (under "Marketplace" or "OS")

**Droplet Type**: Basic (should be selected)

**CPU Options**: Regular (should be selected)

**Size**: $4/month option
- 512 MB RAM
- 1 CPU
- 10 GB SSD

**Datacenter**: Choose closest to you (e.g., New York or San Francisco)

### Step 3: Add SSH Key

**Authentication**: Select "SSH Key"

Click "New SSH Key"
- Paste your public key from Exercise 2
- Name it: "my-laptop" or similar
- Click "Add SSH Key"

### Step 4: Finalize

**Hostname**: Give it a name like `rice-student-server`

**Tags**: (leave empty)

**Backups**: No (unchecked)

Click "Create Droplet"

Wait 1-2 minutes for it to be created.

### Step 5: Note IP Address

Once created, you'll see your droplet with an IP address like `123.456.78.90`

**Deliverable**: In `answers.md`, record your droplet's IP address.

**Question 2**: In `answers.md`, explain what a "droplet" is and how it differs from your laptop.

---

## Exercise 4: Connect via SSH (10 minutes)

### Step 1: First Connection

```bash
ssh root@YOUR_DROPLET_IP
```

Replace `YOUR_DROPLET_IP` with the actual IP.

You might see:
```
The authenticity of host '123.456.78.90' can't be established.
ED25519 key fingerprint is SHA256:...
Are you sure you want to continue connecting (yes/no)?
```

Type `yes` and press Enter.

If successful, you'll see:
```
Welcome to Ubuntu 24.04 LTS
root@rice-student-server:~#
```

### Step 2: Explore

```bash
# Where am I?
hostname

# What OS?
cat /etc/os-release

# How much RAM?
free -h

# Disk space?
df -h
```

**Question 3**: In `answers.md`, answer:
- What's the hostname?
- How much RAM does your droplet have?
- How much disk space is available?

### Step 3: Create Non-Root User (Security Best Practice)

Don't use root for everything. Create your own user:

```bash
# Create user (replace 'yourname' with your netid)
adduser yourname

# Add to sudo group
usermod -aG sudo yourname

# Copy SSH keys to new user
rsync --archive --chown=yourname:yourname ~/.ssh /home/yourname
```

Test the new user:
```bash
su - yourname
```

If it works, exit back to root:
```bash
exit
```

---

## Exercise 5: Configure SSH (15 minutes)

### Step 1: Create SSH Config on Your Laptop

On your laptop (not the server), edit `~/.ssh/config`:

```bash
vim ~/.ssh/config
```

Add:
```ssh
Host droplet
    HostName YOUR_DROPLET_IP
    User yourname
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

Replace:
- `YOUR_DROPLET_IP` with your actual IP
- `yourname` with the username you created

Save and quit (`:wq` in vim).

### Step 2: Test SSH Config

Disconnect from server (if connected):
```bash
exit
```

Now connect using the alias:
```bash
ssh droplet
```

Should connect without asking for IP or username!

**Question 4**: In `answers.md`, explain what the SSH config file does and why it's useful.

### Step 3: Disable Password Authentication (Security)

On the server, edit SSH config:

```bash
sudo vim /etc/ssh/sshd_config
```

Find and change these lines:
```
PasswordAuthentication no
PubkeyAuthentication yes
```

Save and restart SSH:
```bash
sudo systemctl restart sshd
```

Disconnect and reconnect to verify it still works:
```bash
exit
ssh droplet
```

---

## Exercise 6: Install tmux (5 minutes)

On your droplet:

```bash
# Update package list
sudo apt update

# Install tmux
sudo apt install tmux -y

# Verify
tmux -V
```

Should show: `tmux 3.x` or similar.

Create basic tmux config:

```bash
cat > ~/.tmux.conf << 'EOF'
# Enable mouse
set -g mouse on

# Start numbering at 1
set -g base-index 1

# Better prefix (Ctrl-a instead of Ctrl-b)
unbind C-b
set -g prefix C-a
bind C-a send-prefix
EOF
```

Test tmux:
```bash
tmux new -s test
```

You should see a green bar at bottom.

Detach:
```
Ctrl-a d
```

Reattach:
```bash
tmux attach -t test
```

Kill the test session:
```bash
tmux kill-session -t test
```

---

## Exercise 7: Create a Web Server (20 minutes)

### Step 1: Create a Simple Web App

On your droplet, create a directory:

```bash
mkdir ~/web-demo
cd ~/web-demo
```

Create a simple Flask app:

```bash
cat > app.py << 'EOF'
from flask import Flask
import socket
import os
from datetime import datetime

app = Flask(__name__)

@app.route('/')
def home():
    return f'''
    <html>
    <head><title>My First VPS</title></head>
    <body style="font-family: Arial; margin: 50px; background: #f0f0f0;">
        <h1>🎉 Hello from the Cloud!</h1>
        <p><strong>Hostname:</strong> {socket.gethostname()}</p>
        <p><strong>Server Time:</strong> {datetime.now()}</p>
        <p><strong>Running on:</strong> {os.uname().sysname} {os.uname().release}</p>
        <hr>
        <p>This server is running in a tmux session!</p>
        <p>Try disconnecting and reconnecting - I'll still be here! 😎</p>
    </body>
    </html>
    '''

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
EOF
```

### Step 2: Install Flask

```bash
# Install pip if not present
sudo apt install python3-pip -y

# Install Flask
pip3 install flask --break-system-packages
```

### Step 3: Test Locally

```bash
python3 app.py
```

You should see:
```
 * Running on http://0.0.0.0:5000
```

Test it works:
```bash
curl localhost:5000
```

Should see HTML output.

Stop the server (Ctrl-C).

---

## Exercise 8: Run Server in tmux (15 minutes)

### Step 1: Create tmux Session

```bash
tmux new -s webserver
```

### Step 2: Set Up Panes

```bash
# Split vertically
Ctrl-a %

# Left pane: will run server
# Right pane: will monitor logs
```

### Step 3: Run Server

In left pane:
```bash
cd ~/web-demo
python3 app.py 2>&1 | tee server.log
```

In right pane (Ctrl-a arrow-right):
```bash
cd ~/web-demo
tail -f server.log
```

You should see Flask startup messages in both panes.

### Step 4: Test Persistence

1. Detach from tmux:
   ```
   Ctrl-a d
   ```

2. Disconnect from server:
   ```bash
   exit
   ```

3. Wait 30 seconds

4. Reconnect:
   ```bash
   ssh droplet
   ```

5. Reattach to tmux:
   ```bash
   tmux attach -t webserver
   ```

Server should still be running!

**Deliverable**: Take screenshot showing tmux session with web server running.

Save as: `tmux_screenshot.png`

**Question 5**: In `answers.md`, explain what happened when you disconnected and why the server kept running.

---

## Exercise 9: Access Server from Your Laptop (15 minutes)

### Step 1: Set Up Port Forwarding

On your laptop (new terminal), connect with port forwarding:

```bash
ssh -L 5000:localhost:5000 droplet
```

Or add to your SSH config:

```ssh
Host droplet
    HostName YOUR_DROPLET_IP
    User yourname
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
    LocalForward 5000 localhost:5000
```

Then just:
```bash
ssh droplet
```

### Step 2: Access in Browser

Open your browser and go to:
```
http://localhost:5000
```

You should see your web page with server information!

**Deliverable**: Take screenshot of browser showing your web page.

Save as: `browser_screenshot.png`

**Question 6**: In `answers.md`, explain how port forwarding works and why you can access a server running on your droplet through `localhost` on your laptop.

### Step 3: Verify It's Really Running on Droplet

In the browser, note:
- The hostname (should be your droplet name)
- The server time (should match droplet timezone)

Refresh the page a few times - time should update.

In your droplet's tmux session (right pane with logs), you should see:
```
127.0.0.1 - - [date] "GET / HTTP/1.1" 200 -
```

Each browser refresh creates a new log entry.

---

## Exercise 10: Stress Test Persistence (10 minutes)

### Goal: Prove the server survives multiple disconnections

### Step 1: Initial State

Server is running in tmux. You're connected via SSH with port forwarding. Browser shows the page.

### Step 2: First Disconnect

1. In browser: Refresh page (works)
2. Close SSH connection: `exit`
3. Wait 1 minute
4. Browser: Try to refresh (should fail - no port forwarding)

### Step 3: Reconnect

1. SSH back in with port forwarding:
   ```bash
   ssh -L 5000:localhost:5000 droplet
   ```

2. Check tmux:
   ```bash
   tmux ls
   ```
   
   Should show: `webserver: 2 windows...`

3. Reattach:
   ```bash
   tmux attach -t webserver
   ```

4. Browser: Refresh page (works again!)

### Step 4: Verify Uptime

In the tmux session (left pane with server), server should show continuous uptime - no restart messages.

Check log file for gaps:
```bash
cat ~/web-demo/server.log
```

Should show timestamps proving server never stopped.

**Question 7**: In `answers.md`, explain how this differs from running the server directly in SSH without tmux.

---

## Exercise 11: Cleanup (10 minutes)

**IMPORTANT**: Delete your droplet to save credits!

### Step 1: Stop Server

In tmux session:
```bash
# Left pane: Ctrl-C to stop server
# Exit tmux
Ctrl-a d
```

Kill tmux session:
```bash
tmux kill-session -t webserver
```

### Step 2: Delete Droplet

1. Go to https://cloud.digitalocean.com/
2. Click on your droplet
3. Click "Destroy" (top right)
4. Type droplet name to confirm
5. Click "Destroy"

**Deliverable**: Take screenshot of DigitalOcean dashboard showing droplet is deleted.

Save as: `deleted_screenshot.png`

### Step 3: Verify Deletion

Try to SSH:
```bash
ssh droplet
```

Should fail with connection refused or timeout.

**Question 8**: In `answers.md`, answer:
- How much credit did this homework use?
- How much credit do you have remaining?
- How long could you run this $4/month droplet with your remaining credits?

---

## Exercise 12 (Bonus): SSH Config Enhancements (+10%)

Add these to your SSH config and document what each does:

```ssh
Host droplet-secure
    HostName YOUR_DROPLET_IP
    User yourname
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
    ServerAliveCountMax 3
    ControlMaster auto
    ControlPath ~/.ssh/control-%r@%h:%p
    ControlPersist 10m
    Compression yes
    ForwardAgent yes
```

Create `ssh_config_explanation.md` explaining each option and when you'd use it.

---

## Exercise 13 (Bonus): Automated Setup Script (+10%)

Create a script that automates the server setup:

`setup_server.sh`:
```bash
#!/usr/bin/env bash
set -euo pipefail

# Install packages
sudo apt update
sudo apt install -y tmux python3-pip

# Install Flask
pip3 install flask --break-system-packages

# Create web directory
mkdir -p ~/web-demo
cd ~/web-demo

# Create app (use heredoc)
cat > app.py << 'EOF'
# (your Flask app code)
EOF

# Create tmux startup script
cat > start_server.sh << 'EOF'
#!/bin/bash
tmux new -d -s webserver
tmux split-window -h -t webserver
tmux send-keys -t webserver:0.0 'cd ~/web-demo && python3 app.py 2>&1 | tee server.log' C-m
tmux send-keys -t webserver:0.1 'cd ~/web-demo && tail -f server.log' C-m
tmux attach -t webserver
EOF

chmod +x start_server.sh

echo "Setup complete! Run: ./start_server.sh"
```

Upload to your droplet and test:
```bash
scp setup_server.sh droplet:~/
ssh droplet
./setup_server.sh
```

Document in `automation_notes.md`.

---

## Exercise 14 (Bonus): systemd Service (+15%)

Make your web server start automatically on boot using systemd.

Create `/etc/systemd/system/webapp.service`:
```ini
[Unit]
Description=Flask Web App
After=network.target

[Service]
User=yourname
WorkingDirectory=/home/yourname/web-demo
ExecStart=/usr/bin/python3 /home/yourname/web-demo/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable webapp
sudo systemctl start webapp
sudo systemctl status webapp
```

Test:
```bash
sudo reboot
# Wait, reconnect
curl localhost:5000
```

Document the process in `systemd_notes.md`.

---

## Submission Checklist

Your `week3-[netid]/` directory must contain:

### Required (100%)
- [ ] `answers.md` - All 8 questions answered
- [ ] `tmux_screenshot.png` - Shows tmux with server running
- [ ] `browser_screenshot.png` - Shows webpage in browser
- [ ] `deleted_screenshot.png` - Shows droplet deleted

### Bonus (up to +35% extra credit)
- [ ] `ssh_config_explanation.md` (+10%)
- [ ] `setup_server.sh` + `automation_notes.md` (+10%)
- [ ] `systemd_notes.md` (+15%)
- [ ] `notes.md` (what broke, how you fixed it)

---

## Grading

**Required exercises (100%):**
- Exercise 1-3: Setup (20%)
- Exercise 4-5: SSH configuration (20%)
- Exercise 6-8: tmux and web server (30%)
- Exercise 9-10: Port forwarding and persistence (20%)
- Exercise 11: Cleanup (10%)

**Grading is completion-based**: If you attempted each exercise honestly and followed instructions, full credit for that exercise.

---

## Common Issues and Solutions

### "Permission denied (publickey)"

Your SSH key isn't being used.

Solution:
```bash
ssh -i ~/.ssh/id_ed25519 root@YOUR_IP
```

Or check SSH config has correct `IdentityFile` path.

### "Connection refused" on port 5000

Flask isn't binding to all interfaces.

Check app.py has:
```python
app.run(host='0.0.0.0', port=5000)
```

Not:
```python
app.run()  # Wrong - only binds to localhost
```

### "Cannot connect after disconnect"

Port forwarding is per-SSH-session.

Solution: Reconnect with `-L` flag:
```bash
ssh -L 5000:localhost:5000 droplet
```

### Flask not found

Install:
```bash
python3 -m pip install flask --user
```

### Droplet creation fails

You may need to verify your account (credit card or PayPal).

Alternative: Use free trial, apply student credits later.

### Can't delete droplet

Navigate to droplet page → "Destroy" at very top right.

If still stuck, contact DigitalOcean support.

---

## Questions Template

Copy this into your `answers.md`:

```markdown
# Week 3 Homework Answers

## Student Info
- Name: 
- NetID:
- Droplet IP: 

## Exercise Answers

### Question 1
What is an SSH key and why is it better than passwords?

**Answer:** 

### Question 2
What is a "droplet" and how does it differ from your laptop?

**Answer:**

### Question 3
- Hostname: 
- RAM: 
- Available disk space: 

### Question 4
What does the SSH config file do and why is it useful?

**Answer:**

### Question 5
What happened when you disconnected and why did the server keep running?

**Answer:**

### Question 6
How does port forwarding work and why can you access a server running on your droplet through `localhost` on your laptop?

**Answer:**

### Question 7
How does running the server in tmux differ from running it directly in SSH without tmux?

**Answer:**

### Question 8
- Credit used: $
- Remaining credit: $
- How long could you run a $4/month droplet: 

## Notes
(Optional - any problems you encountered and how you solved them)
```

---

## Tips for Success

**Take screenshots as you go** - Don't wait until the end

**Read error messages carefully** - They usually tell you what's wrong

**Test each step before moving on** - Don't skip ahead if something doesn't work

**Ask for help early** - Don't waste hours stuck on one issue

**Save your SSH config** - You'll use these patterns all semester

**Delete your droplet!** - Seriously, don't waste $200 of free credits

---

## What You've Learned

By completing this homework, you've:

- Created and configured a cloud server from scratch
- Set up SSH keys and config for easy access
- Used tmux to create persistent sessions
- Run a web service that survives disconnections
- Used port forwarding to access remote services
- Understood the basics of "the cloud"
- Practiced good security (SSH keys, no root, non-standard user)
- Learned responsible resource management (cleanup)

These skills transfer to:
- AWS EC2
- Google Cloud
- Azure
- Any VPS provider
- Any Linux server

You now understand how servers actually work. Everything in "the cloud" is just this, at scale.

---

**Submit by**: Next Monday via git submodule

**Questions?** Office hours or class forum

**Remember**: Delete your droplet!
