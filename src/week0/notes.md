# Week 0 Notes
Week 0 is the first week of the course, where I introduce the course to the class. Watching the video is a great way to skip straight to the content and expectations of the class.


## Intro
As computer scientists, we know that computers are great at aiding in repetitive tasks. However, far too often, we forget that this applies just as much to our use of the computer as it does to the computations we want our programs to perform. We have a vast range of tools available at our fingertips that enable us to be more productive and solve more complex problems when working on any computer-related problem. Yet, many of us utilize only a small fraction of those tools; we only know enough magical incantations by rote to get by, and blindly copy-paste commands from the internet when we get stuck.

This class is an attempt to address this and close a gap between theory and practice. I intend to teach how to make most of the tools you know, show new tools to add to your toolbox, and instill in you some excitement for exploring (and perhaps building) more tools to be a true lazy programmer.

## Demo

```admonish
The basics of bash will be covered in the next lesson. For now, we need to get used to the terminal and the basic commands.
```

## Basic Unix Commands
There are many bash commands, but this short guide is meant to cover the essentials, either as an introduction for those unfamiliar with Unix-based terminals or as a refresher for those more experienced.

### Basic Keystrokes and Characters


|     keystroke / character | function                                                                                                        |
|--------------------------:|-----------------------------------------------------------------------------------------------------------------|
| `uparrow` and `downarrow` | Scroll through recently used commands.                                                                          |
|                     `tab` | Autocomplete commands or file names / directories.                                                              |
|                `ctrl`-`c` | Kill the current process.                                                                                       |
|                       `.` | Hidden file prefix or current directory.                                                                        |
|                       `~` | Home directory.                                                                                                 |
|                     `../` | Move up one directory.                                                                                          |
|                      ` \| `  | Pipe for chaining multiple commands.                                                                            |
|                       `>` | Redirect output to a file.                                                                                      |
|                       `<` | Redirect input from a file.                                                                                     |
|                       `*` | Wild card character (e.g., use it find all files in the current directory that are Python scripts `$ ls *.py`). |
|                       `$` | Indicate a variable in bash (e.g., $HOME).                                                                      |
|               `ctrl`-`z` | Suspend the current foreground process.                                                                         |
|               `ctrl`-`d` | Exit the current shell (sends EOF).                                                                             |
|                `ctrl`-`r` | Search through command history interactively.                                                                   |
|                      `&&` | Logical AND (e.g., `cmd1 && cmd2` runs `cmd2` only if `cmd1` succeeds).                                         |
|                      `\|\|` | Logical OR (e.g., `cmd1 \|\| cmd2` runs `cmd2` only if `cmd1` fails).                                             |
|                       `!` | Negate a condition or access the last command that starts with a particular string.                              |
|                `echo`-` ` | Display a line of text/string that is passed as an argument.                                                    |
|                      `; ` | Command separator (e.g., `cmd1; cmd2` runs `cmd2` after `cmd1` regardless of the outcome of `cmd1`).            |

### Foundations

Navigating the terminal requires familiarity with basic commands for directory manipulation, file interaction, and process monitoring. To delve deeper into any command, consult the Unix manuals by entering `man command_name`.

#### File and Navigation Fundamentals
- **`cd`**: Change directories (folders).
- **`mkdir`**: Create a new directory.
- **`ls`**: List all files in a given directory.
- **`cp`**: Copy a file from one location to another.
- **`scp`**: Copy files between two machines over SSH, similar to `cp`.
- **`rsync`**: A more sophisticated method for copying files either locally or over a network, combining the functionalities of `cp` and `scp`.
- **`mv`**: Move a file from one location to another.
- **`rm`**: Delete a file. Use `-r` to remove directories.
- **`pwd`**: Display the current working directory's path.
- **`chmod`**: Change file permissions.
- **`chgrp`**: Change the group associated with a file or directory.

#### Probing File Contents
- **`cat`**: Display the entire contents of a file to the command line, useful for small files or as part of a command chain.
- **`wc`**: Count the words in a file, or use `-l` to count lines.
- **`head`** and **`tail`**: Display the beginning or end of a file, respectively.
- **`less`**: Efficiently view large files by loading only a portion at a time. Use arrow keys to navigate, `G` to jump to the end, `g` to return to the beginning, `/` to search within the file, and `q` to exit.

#### Process Basics
- **`ps`**: List currently running processes.
- **`kill`**: Terminate a process.
- **`htop`**: Launch a sophisticated process manager, akin to a text-based version of Task Manager on Windows or Activity Monitor on Mac.

#### Other Useful Commands
- **`du -h`**: Check disk usage with output in human-readable format.
- **`wget`**: Download files from the internet using a URL.
- **`curl`**: Fetch or send data using URL syntax, offering more options than `wget`. For differences, refer to [curl vs wget](https://daniel.haxx.se/docs/curl-vs-wget.html).

#### Rust replacements
Here are several well-known Rust projects that serve as modern alternatives or enhancements to classic Unix commands:

### Rust Projects as Unix Command Alternatives
- **Exa (`ls`)**: Exa is a modern replacement for `ls`, the traditional Unix command to list directory contents. It features better defaults and is rich in features like git integration and color-coded output.  [Github Link](github.com/ogham/exa)
- **Ripgrep (`grep`)**: Ripgrep is a line-oriented search tool that recursively searches your current directory for a regex pattern. It is much faster than traditional `grep` due to its efficient use of memory and CPU, and its ability to ignore binary files and hidden directories by default.  [Github Link](github.com/BurntSushi/ripgrep)
- Bat (`cat`): Bat is a clone of `cat` with syntax highlighting, line numbers, and git integration. It also integrates with other tools like `ripgrep` to provide a seamless viewing experience.  [Github Link](github.com/sharkdp/bat)
- Fd (`find`): Fd is a simple, fast, and user-friendly alternative to `find`. It helps you find files in the file system quickly and efficiently.  [Github Link](github.com/sharkdp/fd)
- Dust (`du`): Dust is a more intuitive version of `du`. It shows disk usage and free space in a more comprehensible way by automatically sorting folders by size and visualizing the output in a tree format.  [Github Link](github.com/bootandy/dust)
- Zoxide (`cd`): Zoxide is a smarter `cd` command, learning your habits and allowing you to jump to frequently and recently used directories with fewer keystrokes.  [Github Link](github.com/ajeetdsouza/zoxide)
- Procs (`ps`): Procs is a modern replacement for `ps`. It provides detailed and colored output to make the information more comprehensible.  [Github Link](github.com/dalance/procs)
- Skim (`fzf`/`ctrl-r`): Skim is a fuzzy finder similar to `fzf` but written in Rust. It can be used to quickly find files, directories, or even history commands.  [Github Link](github.com/lotabout/skim)

These projects not only replace traditional Unix commands but also enhance them with modern features and better performance, thanks to Rust's efficiency and safety features.

### Tips
- If you wish to use vim keybinds, you can by setting readline to `set -o vi` in your `.bashrc`. This allows you to use vim-like editing commands directly in the command line.
- If you wish to "pause" a command, you can use `<C-z>` to set the current process to the background. Then when you are ready to unpause, you can use `fg` to continue your progress.
- To search through your command history quickly, use `<C-r>` for a reverse search. Start typing part of the command you're looking for, and press `<C-r>` repeatedly to cycle through past commands that match.
- Customize your prompt to show helpful information like the current directory, git branch, or other useful status by modifying the `PS1` variable in your `.bashrc`.
- Use `alias` to shorten commands that you use frequently. For example, you can add `alias ll='ls -alF'` to your `.bashrc` to create a shortcut for a detailed listing format.
- When scripting, use `set -e` to make your script exit on the first error. This can help prevent errors from cascading and make debugging easier.
- To preserve your session's state between logins, consider using a terminal multiplexer like `tmux` or `screen`. This is especially useful for remote sessions over SSH.
- If you find yourself needing the same command frequently but with slight variations, use `history | grep <search-term>` to quickly find previously used commands without needing to retype them completely.
- For more efficient navigation, use `pushd` and `popd` to work with directories as a stack. This allows you to "push" directories onto your stack as you navigate and "pop" them off to return to previous locations.
