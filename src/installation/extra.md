# More Tools & Other Recommendations

# Customization

## Set up SSH Keys
SSH keys are convenient, because they allow you to log into remote machines without typing your password. To set up, you will first need to generate an SSH key that acts as a way of verifying your machine.

To create ssh keys on your local machine, open up a terminal:

!!! note
    These instructions may not be applicable to Windows users unless you install WSL.
    
    
```
$ ssh-keygen
```

!!! tip
    You will be prompted with the option to enter a passphrase for your keys. Please enter a passphrase, but use something different from your Rice password. You will be able to add your SSH key to `ssh-agent` so you do not have to enter your passphrase every time. If you already have generated an SSH key, but did not use a passphrase originally, you can set a passphrase with `$ ssh-keygen -p`. This same command will also allow you to reset the passphrase (after prompting you for the old one).

!!! note
    If you already have an SSH key, you can skip the key generation step (you will receive a warning like the one below if you already have an ssh key):
    ```
      $ /Users/username/.ssh/id_rsa already exists
    ```
    It is recommend by various security experts to have different keys for different uses.
    If you overwrite your old key, you will have to update any servers that used the previous one to authenticate with your new one.

To manage your private keys (and not have to enter your passphrase every time you SSH), first, start `ssh-agent` in the background:
```
$ eval "$(ssh-agent -s)"
```

Then, add your private key (you will be prompted for your passphrase):
```
$ ssh-add
```

You can also add a timeout to `ssh-add` using `$ ssh-add -t 3600` (for a timeout of 3600 seconds) to be extra secure.

For most machines, `ssh-agent` should start automatically, so when you start a completely new session (e.g., after rebooting your computer), all you should need to do is run `ssh-add` again, but if `ssh-agent` has not started, you will need to start it in the background again as well.

To log in to our machines (e.g., risotto) without entering your password every time, you will need to copy your public key to the remote machine. You can do this with the following command, where username is replaced by your Rice net id:
```
$ ssh-copy-id username@ssh.clear.rice.edu
```

If successful, you will be presented with instructions of how you can now log in:
```
Now try logging into the machine, with:   "ssh 'username@ssh.clear.rice.edu'"
and check to make sure that only the key(s) you wanted were added.
```

If `ssh-copy-id` is not installed on your machine (e.g., you have a Windows machine), you can use the following command to copy your public key to the remote server:
```
$ cat ~/.ssh/id_rsa.pub | ssh username@ssh.clear.rice.edu "mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

You know can type in your password once per startup session.


## Set up SSH Config File - Bypassing DUO authentication
Writing an SSH config file can help further streamline SSH connections and avoid having to repeatedly add flags when connecting to machines. If you do not have a config file you first need to create one in `~/.ssh/config`.

To enable shorter ssh names, e.g. accessing clear by typing `ssh clear` vs `ssh username@ssh.clear.rice.edu` you need to add additional lines per host to your ssh config (change everything inside the brackets)

```
Host clear
	User [your_netid]
	HostName ssh.clear.rice.edu
	PasswordAuthentication no
	Preferredauthentications publickey
```

!!! note
    `passwordAuthentication no` with `Preferredauthentications publickey` allows us to bypass duo authentication.
    
    
With this base you should now be able to ssh into risotto without specifying a username:
```
$ ssh clear
```

!!! note
    If you would like to use X11 for GUI displays on the remote server (an example use case is the simple atom editor installed on the servers), you will need to install X11 on your local machine (XQuartz for macs and Cygwin for Windows 10), you can add the following line under your host definition:
    ```
    ForwardX11 yes
    ```

## Choose Your Shell
Before you log into our machines, make sure you set your default shell preferences. The default choice is csh, but you can choose between: bash, sh, tcsh, csh, zsh.

We recommend bash, but you are welcome to choose whatever you feel most comfortable with. To change the default shell for any remote machine, you log into [account config](https://old.apply.rice.edu/) (you need to be on VPN to access this page). Log in with your NetID, then under “Account Maintenance” -> “Shell Management,” you can choose your desired shell environment.

## Bash Profiles
Editing shell profiles can save you time and effort by configuring aliases or short cuts for various commands, for changing the colors of different file and directory types, and adding other convenient functionality.

Since we primarily use bash, we have included some configurations we have found useful that we usually set a priori.

These should be set in your `~/.bashrc` file (you may have to create it if it is missing).

Aliases allow you to set shortcuts for frequently used commands. These are some good ones to configure.

```
alias rm='rm -i' # flag that asks permission to delete, can override with -f
alias mv='mv -i'
alias ls='ls -G'
alias ll='ls -lthG' # time human readable (with table)
alias l.='ls -G -d .*' # shows hidden
alias mkdir='mkdir -pv' # auto makes parent directories
alias wget='wget -c' # default behavior to continue downloading if stopped
```
Other helpful functions:
```
# preventing accidental overwriting using >
set -o noclobber

# allows for unlimited bash history size
export HISTCONTROL=ignoredups
export HISTFILESIZE=
export HISTSIZE=
export HISTTIMEFORMAT="[%F %T] "
export HISTFILE=~/.bash_history_unlimited
shopt -s histappend
shopt -s autocd
shopt -s checkwinsize #check window size
shopt -s globstar # match files with **
PROMPT_COMMAND="history -a; $PROMPT_COMMAND"


# Color for Man pages
export LESS_TERMCAP_mb=$'\e[1;32m'
export LESS_TERMCAP_md=$'\e[1;32m'
export LESS_TERMCAP_me=$'\e[0m'
export LESS_TERMCAP_se=$'\e[0m'
export LESS_TERMCAP_so=$'\e[01;33m'
export LESS_TERMCAP_ue=$'\e[0m'
export LESS_TERMCAP_us=$'\e[1;4;31m'


# enable colors
export TERM=xterm-256color
```
To look at history with timestamps, you can just use `history` command (and of course you can use pipes, grep as with other bash commands, e.g., `history | less`). If you try accessing the file directly at `$HISTFILE`, the formatting may look a bit nonsensical (with each command preceded by a comment).
