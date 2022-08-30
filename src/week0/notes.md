# Week 0 Notes
Week 0 is the first week of the course, where I introduce the course to the class. Watching the video is a great way to skip straight to the content and expectations of the class.


## Intro
As computer scientists, we know that computers are great at aiding in repetitive tasks. However, far too often, we forget that this applies just as much to our use of the computer as it does to the computations we want our programs to perform. We have a vast range of tools available at our fingertips that enable us to be more productive and solve more complex problems when working on any computer-related problem. Yet many of us utilize only a small fraction of those tools; we only know enough magical incantations by rote to get by, and blindly copy-paste commands from the internet when we get stuck.

This class is an attempt to address this and close a gap between theory and practice.

I want to teach you how to make the most of the tools you know, show you new tools to add to your toolbox, and hopefully instill in you some excitement for exploring (and perhaps building) more tools on your own to be a true lazy programmer.

## Demo


!!! note

    The basics of bash will be covered in the next lesson.

## Basic Unix Commands
There are many bash commands, but this short guide is meant to cover the essentials, either as an introduction for those unfamilar with Unix-based terminals or as a refresher for those more experienced.

### Basic Keystrokes and Characters

|     keystroke / character | function                                                                                                       |
|--------------------------:|----------------------------------------------------------------------------------------------------------------|
| `uparrow` and `downarrow` | scroll through recently used commands                                                                          |
|                     `tab` | autocomplete commands or file names / directories                                                              |
|                `ctrl`-`c` | kill current process                                                                                           |
|                       `.` | hidden file prefix or current directory                                                                        |
|                       `~` | home directory                                                                                                 |
|                     `../` | up one directory                                                                                               |
|                      \|   | pipe for chaining multiple commands                                                                            |
|                       `>` | output redirect                                                                                                |
|                       `*` | wild card character (e.g., use it find all files in the current directory that are python scripts `$ ls *.py`) |
|                       `$` | indicate a variable in bash (e.g., $HOME)                                                                      |

### Foundations
To navigate around the terminal you will need to know some basic commands for directory manipulation, file interaction, and process monitoring. To find out more information about any command, take advantage of the manuals in Unix by entering `man command_name`.


- file and navigation fundamentals:

    - `cd`: change directories (folders)
    - `mkdir`: create a directory
    - `ls`: list all files in a given directory
    - `cp`: copy a file from source to target
    - `scp`: same as `cp` except between two machines over ssh
    - `rsync`: more sophisticated, optimized (but also slightly more complex) way to copy files either locally or over a network (functionality of both `cp` and `scp`)
    - `mv`: move a file from source to destination
    - `rm`: delete a file, add `-r` to delete directories
    - `pwd`: print the current working directory (and its path from the root)
    - `chmod`: change file permissions
    - `chgrp`: change the group associated with a file or directory

- probing file contents:

    - `cat`: print the entire contents of a file to the command line. This command is best used to view small files (only a few lines long) or as part of a longer, more complex command.
    - `wc`: counts up the words in a file, or lines when using `-l`
    - `head` and `tail`: prints the start or end of a file to the command line
    - `less`: efficent way to view large files as it loads in only a small portion of a file at a time. Once open use arrow keys to scroll, `G` to navigate to the end of a file, `g` to the beginning, `/` to search, and `q` to exit.

- process basics:

    - `ps`: list currently running processes
    - `kill`: terminate a process
    - `htop`: opens a more sophisticated process manager - analogous to a text-based version of Windows task manager or Mac's Activity Monitor.

- other useful commands:

  - `du -h`: check disk usage (the `-h` flag displays usage in human readable format)
  - `wget`: download a file from a given url
  - `curl`: very similar to `wget` with slightly more options for interacting with remote servers; see [curl vs wget](https://daniel.haxx.se/docs/curl-vs-wget.html) for more.


### Tips
- If you wish to use vim keybinds, you can by setting readline to `set -o vi` in your .bashrc.
- If you wish to "pause" a command, you can use `<C-z>` to set the current process to the background. Then when you are ready to unpause, you can use `fg` to continue your progress.
