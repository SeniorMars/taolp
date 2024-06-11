# More Tools & Other Recommendations

## Customization

### Set up SSH Keys
SSH keys provide a convenient and secure way to log into remote machines without typing your password. Here’s how to set up your SSH keys, which act as a form of verification for your machine:

```bash
$ ssh-keygen
```

```admonish
These instructions may not be applicable to Windows users unless WSL is installed.
```

~~~admonish info
During key generation, you'll have the option to enter a passphrase. Choose a passphrase different from your Rice password for security. Use `ssh-agent` to avoid re-entering this passphrase:
```shell
$ ssh-keygen -p  # Set or change your passphrase
```
~~~
~~~admonish info
If an SSH key already exists, you will see a message like this:
```shell
$ /Users/username/.ssh/id_rsa already exists
```
Security experts recommend using different keys for different purposes. If you replace an old key, remember to update it on any servers that used it.
~~~

To manage your private keys (and not have to enter your passphrase every time you SSH), first, start `ssh-agent` in the background:
```bash
$ eval "$(ssh-agent -s)"
```

Then, add your private key (you will be prompted for your passphrase):
```bash
$ ssh-add
```

```admonish info
You can also add a timeout to `ssh-add` using `$ ssh-add -t 3600` (for a timeout of 3600 seconds) to be extra secure.

For most machines, `ssh-agent` should start automatically, so when you start a completely new session (e.g., after rebooting your computer), all you should need to do is run `ssh-add` again, but if `ssh-agent` has not started, you will need to start it in the background again as well.
```

To log in to our machines (e.g., risotto) without entering your password every time, you will need to copy your public key to the remote machine. You can do this with the following command, where username is replaced by your Rice net id:

```shell
$ ssh-copy-id username@ssh.clear.rice.edu
```

If `ssh-copy-id` is unavailable, manually copy the key:
```shell
$ cat ~/.ssh/id_rsa.pub | ssh username@ssh.clear.rice.edu "mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

You know can type in your password once per startup session.

### Set up SSH Config File - Bypassing DUO Authentication
Writing an SSH config file can streamline SSH connections and avoid having to repeatedly add flags when connecting to machines. If you do not have a config file you first need to create one in `~/.ssh/config`.

To enable shorter ssh names, e.g. accessing clear by typing `ssh clear` vs `ssh username@ssh.clear.rice.edu` you need to add additional lines per host to your ssh config (change everything inside the brackets)

```ssh
Host clear
    User [your_netid]
    HostName ssh.clear.rice.edu
    PasswordAuthentication no
    PreferredAuthentications publickey
```

```admonish info
Setting `PasswordAuthentication no` with `PreferredAuthentications publickey` helps bypass DUO authentication.
```

With this setup, you can connect to CLEAR by typing:
```bash
$ ssh clear
```

~~~admonish info
If you would like to use X11 for GUI displays on
the remote server (e.g., using Atom editor), you will
need to install X11 on your local machine (XQuartz for
macOS and Cygwin for Windows 10). Add the following
line under your host definition:
    ```
    ForwardX11 yes
    ```
~~~

## Choose Your Shell
Before you log into our machines, make sure you set your default shell preferences. The default choice is csh, but you can choose between: bash, sh, tcsh, csh, zsh.

We recommend zsh, but you are welcome to choose whatever you feel most comfortable with. To change the default shell for any remote machine, you log into [account config](https://old.apply.rice.edu/) (you need to be on the RICE VPN to access this page). Log in with your NetID, then under “Account Maintenance” -> “Shell Management,” you can choose your desired shell environment.

## Bash Profiles
Editing shell profiles can save you time and effort by configuring aliases or short cuts for various commands. In addition, it allows for changing the colors of different file and directory types, and adding other convenient functionality.

Since we primarily use bash, we have included some configurations we have found useful that we usually set a priori.

These should be set in your `~/.bashrc` file (you may have to create it if it is missing).

Aliases allow you to set shortcuts for frequently used commands. These are some good ones to configure.
```bash
alias rm='rm -i'  # Ask before deleting
alias mv='mv -i'
alias ls='ls -G'
alias ll='ls -lthG'  # Detailed listing
alias l.='ls -G -d .*'  # Show hidden files
alias mkdir='mkdir -pv'  # Make parent directories as needed
alias wget='wget -c'  # Continue stopped downloads
```
Other helpful functions:

```bash

# Prevent accidental overwriting
set -o noclobber

# Enhance history features
export HISTCONTROL=ignoredups
export HISTFILESIZE=
export HISTSIZE=
export HISTTIMEFORMAT="[%F %T] "
export HISTFILE=~/.bash_history_unlimited
shopt -s histappend
shopt -s autocd
shopt -s checkwinsize
shopt -s globstar  # Match files with **
PROMPT_COMMAND="history -a; $PROMPT_COMMAND"

# Color settings for man pages
export LESS_TERMCAP_mb=$'\e[1;32m'
export LESS_TERMCAP_md=$'\e[1;32m'
export LESS_TERMCAP_me=$'\e[0m'
export LESS_TERMCAP_se=$'\e[0m'
export LESS_TERMCAP_so=$'\e[01;33m'
export LESS_TERMCAP_ue=$'\e[0m'
export LESS_TERMCAP_us=$'\e[1;4;31m'

# Enable terminal colors
export TERM=xterm-256color
```

To view your command history with timestamps, use `history`. Access the history file directly at `$HISTFILE` for a detailed log.

Certainly! Below is a section on the Fish shell, detailing its features and benefits for users considering an alternative to Bash.

## Choose Your Shell: Why Fish Might Be Your Next Best Friend

When setting up your development environment, selecting the right shell can significantly enhance your workflow efficiency. While Bash is the default shell on many systems due to its long-standing presence, **Fish (the Friendly Interactive SHell)** offers several compelling features that might make it your shell of choice.

### What is Fish?

Fish is a smart and user-friendly command line shell for macOS, Linux, and the rest of the family. Unlike traditional shells, which can be daunting for newcomers, Fish is designed with usability in mind, offering powerful features to both new and seasoned users.

### Features That Make Fish Stand Out

- **Autosuggestions**: Fish suggests commands as you type based on history and completions, just like a web browser. Watch as Fish suggests commands in real time, allowing you to repeat previous commands faster or use similar commands without remembering every detail.

- **Enhanced Tab Completions**: Fish offers advanced completion capabilities. It completes commands, options, and even file paths, reducing the amount of typing and minimizing the guesswork.

- **Rich, Out-of-the-box Configuration**: Unlike Bash, Fish works impressively right out of the box. It includes a web-based configuration interface for adjusting prompt colors and functions without the need to edit text files.

- **Extensible and Easy Scripting**: Fish's scripting syntax is simple and clean. For example, there are no `$` signs needed for variable expansion. Conditions and loops are easier to write and read, making script maintenance less cumbersome.

- **Web-based Configuration**: Fish includes a web interface that allows you to configure it from your browser—something unique that no other shell offers. This makes tweaking Fish's behavior and appearance easier for users who prefer graphical interfaces over text files.

### Switching to Fish

Fish can be installed on most systems with a simple package manager command. For example, on macOS, you can use Homebrew:

```bash
$ brew install fish
```

And on Ubuntu systems:

```bash
$ sudo apt install fish
```

To make Fish your default shell, you can use the `chsh` command:

```bash
$ chsh -s /usr/local/bin/fish  # The path to Fish might differ based on your installation
```

### Integrating Fish into Your Workflow

Transitioning to Fish can be straightforward due to its intelligent design. It reads and executes commands from `~/.config/fish/config.fish`, similar to Bash’s `.bashrc`. You can start by adding custom functions or aliases into this file.

Fish is particularly useful for developers who value a rich, straightforward, and interactive command line experience. Its syntax and features promote efficiency and ease of use, potentially making your command line tasks faster and more enjoyable.

For those interested in exploring the power of Fish or wanting a break from the more traditional Bash shell, Fish offers a refreshing and powerful alternative.
