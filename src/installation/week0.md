# Week 0 Installation Guide

Welcome to Week 0! In this guide, you'll learn how to log into CLEAR and submit your first assignment. We've included specific instructions tailored to different operating systems to ensure everyone can get started smoothly.

## Getting Started with Remote Servers

Everyone has their preferred setup for working on remote machines. While you're encouraged to use what works best for you, we recommend following these guidelines if you're new to this environment. Be sure to review the [basic Unix commands notes](../week0/notes.md) to familiarize yourself with the necessary commands. This tutorial is intended for MacOS and/or Linux Users, but instructions for Windows are included.


### What is CLEAR?
"CLEAR is a robust and dynamic Linux cluster with exciting features available to Rice students and faculty. The cluster is designed to offer a Linux environment available for teaching and courseware needs." 
Specifically, CLEAR is the cluster we will be using for this class, as we'll all be using Fedora Linux as our environment.


### How to Login into CLEAR

To access CLEAR, you need an SSH client (I'll explain how to install one in the next section). Once you have an SSH client, you can log into CLEAR using the following details:

1. **Host Name**: Use `ssh.clear.rice.edu`.
2. **Login Credentials**: Enter your Rice NetID and password.
3. **Direct Access**: Your home directory on CLEAR is linked to your desktop home from `storage.rice.edu`.

```admonish
If you don't have a home drive when using Clear, please create a ticket in [Rice IT](https://help.rice.edu) with the subject "need clear home directory" to set one up for you.
```

## Installing an SSH Client

### Windows Users

If you are on Windows, it is recommended to install the [Windows Subsystem for Linux with Ubuntu](https://docs.microsoft.com/en-us/windows/wsl/install) (WSL). Windows Powershell is now compatible with OpenSSH on Windows 10, however, for optimal server use for our labs, a *UNIX environment is fully preferred. The Windows Subsystem for Linux brings a full Linux experience to Windows 10 with WSL 2 bringing a full Linux kernel to windows. If your computer/laptop does not support virtualization, then a SSH program like [putty](https://www.puttygen.com/download-putty) may be ideal.

#### Windows Subsystem for Linux

To install WSL, run the following command in PowerShell as an administrator:

```powershell
wsl --install
```

For a detailed guide, watch this [installation video](https://www.youtube.com/watch?v=htBOnNsrDBA).

### Linux Users

For Linux users, particularly those using Ubuntu, ensure you have the OpenSSH client installed:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt install openssh-client
```

### macOS Users

Mac users typically have an SSH client pre-installed. If not, or for a more enhanced experience, consider installing iTerm2. Additionally, enabling remote login and installing Xcode can be beneficial:

```bash
sudo systemsetup -setremotelogin on
sudo systemsetup -getremotelogin
xcode-select --install
```

```admonish
We recommend installing Xcode from the App Store and running `xcode-select --install` in the terminal to install Xcode command line tools.
```

## Logging into CLEAR

To connect to CLEAR, use the following command:

```bash
ssh [your Rice NetID]@ssh.clear.rice.edu
```

Example for Windows: https://imgur.com/a/CyQ2vU4

Doing this successfully will log you into CLEAR. It is important to note that you may need to type your password twice (once for the ssh client and once DUO authentication).

## Further Customization and Setup

View the [extra notes](../installation/extra.md) for more information on how to customize your environment. Specifically, how to log into CLEAR faster and bypass DUO.

# Introduction to Git

Git is a version control system that allows you to track changes to your files. Git is a free and open source software distributed under the GPL.

You will be learning about git in this class through the assignments. You may be interested in the guide on [First Time Git Setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) if you need a refresher on using git in general.

# Git and GitHub - Submodule Setup

Think of a submodule as a GitHub repo inside another GitHub repo (i know, woah). Here’s how you can set it up effectively:


### 1. Create and clone your repo where all your work will be done:

```shell
$ git clone git@github.com:charlie/work-test.git
cloning into 'work-test'...
remote: counting objects: 3, done.
remote: total 3 (delta 0), reused 0 (delta 0), pack-reused 0
unpacking objects: 100% (3/3), done.
checking connectivity... done.
```

### 2. Clone the main class repo:

```shell
 $ git clone git@github.com:mks65/euler.git
cloning into 'euler'...
remote: counting objects: 14, done.
remote: compressing objects: 100% (9/9), done.
remote: total 14 (delta 5), reused 9 (delta 3), pack-reused 0
unpacking objects: 100% (14/14), done.
checking connectivity... done.
```

### 3. Navigate to the appropriate directory in the class repo and add your repo as a submodule:


```bash
cd class-repo
git submodule add -b "branch" "url to your repository" "required submodule directory name"
```

Here is an example:

```shell
$ cd euler/
socrates: euler charlie$ cd 04/
socrates: euler/04 charlie$ git submodule add -b main git@github.com:charlie/work-test.git cruz-charlie
cloning into '04/cruz-charlie'...
remote: counting objects: 3, done.
remote: total 3 (delta 0), reused 0 (delta 0), pack-reused 0
unpacking objects: 100% (3/3), done.
checking connectivity... done.
```

### 4. Commit and push your changes to the main class repo:

```bash
git commit -am "Added submodule for lastname-firstname"
git push
```
Here is an example:

```shell
$ git pull
already up-to-date.
socrates:~/desktop/git_demo/euler/04 charlie$ git commit -a -m "added charlie submodule"
    [master f25eeda] added charlie submodule
2 files changed, 4 insertions(+)
    create mode 100644 .gitmodules
    create mode 160000 randomizer/6/cruz-charlie
    socrates:~/desktop/git_demo/euler/04 charlie$ git push
    counting objects: 5, done.
    delta compression using up to 4 threads.
    compressing objects: 100% (5/5), done.
    writing objects: 100% (5/5), 626 bytes | 0 bytes/s, done.
total 5 (delta 0), reused 0 (delta 0)
    to https://github.com/mks65/euler.git
    11dc0c6..f25eeda  master -> master
```

### 5. Remove the main repo clone if no longer needed:

```bash
$ cd ../../
$ rm -rf euler/
```

Ensure to follow these steps to maintain a clean and organized repository structure for your coursework.
