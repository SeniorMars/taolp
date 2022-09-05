# Week 0 Installation
In this guide, I'll teach you how to log into CLEAR and commit your first assignment.


## Getting Started with Remote Servers
Everyone has their own preferences for setting up environments and workflows on remote machines. These are the practices that we have found that significantly speed things up and make things easier, but you are of course welcome to use what works best for you. If you have limited experience, we recommend you follow these guidelines to get started. Make sure to view the notes for [basic unix commands](../week0/notes.md). This tutorial is intended for MacOS and/or Linux Users, but instructions for Windows are included.

## What is CLEAR?
"CLEAR is a robust and dynamic Linux cluster with exciting features available to Rice students and faculty. The cluster is designed to offer a Linux environment available for teaching and courseware needs." 
Specifically, CLEAR is the cluster we will be using for this class, as we'll all be using Fedora Linux as our environment.

## How to login into CLEAR
To access the CLEAR cluster interactively, you must have an SSH client. Simply use ssh.clear.rice.edu as the host name and log in with your Rice NetID and password. Your home directory will be the same as your desktop home provided from storage.rice.edu.

If you don't have a home drive when using Clear, please create a ticket in https://help.rice.edu Be sure to put "need clear home directory" in the subject and one will be setup for you.

# Installing an SSH client 

## Windows -- Windows Subsystem For Linux and Putty
If you are on Windows, it is recommended to install the [Windows Subsystem for Linux with Ubuntu](https://docs.microsoft.com/en-us/windows/wsl/install) (WSL). Windows Powershell is now compatible with OpenSSH on Windows 10, however, for optimal server use for our labs, a *UNIX environment is fully preferred. The Windows Subsystem for Linux brings a full Linux experience to Windows 10 with WSL 2 bringing a full Linux kernel to windows. If your computer/laptop does not support virtualization, then a SSH program like [putty](https://www.puttygen.com/download-putty) may be ideal.

### Windows Subsystem for Linux
Likely, you'll just need to run this one powershell instance with admin privileges:

```powershell
wsl --install
```

If lost, you may find my old video on installing WSL useful: https://www.youtube.com/watch?v=htBOnNsrDBA.


## Linux -- Ubuntu
If you are on linux, you can install an ssh-client with the following command:

```bash
$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt install openssh-client
```

## MacOS
If you are on a Mac, you already have an ssh client. However, in the case you don't, you should install iTerm2. Alternatively, the following instructions may be useful.

```bash
$ sudo systemsetup -setremotelogin on
$ sudo systemsetup -getremotelogin
```

It's also recommended to install Xcode.
    - Install Xcode from the App Store
    - Run the following in a terminal in install xcode command line tools: `$ xcode-select --install`


# Logging into CLEAR
Once you have an ssh client, you can log into CLEAR with the following command:

```bash
$ ssh [your Rice NetID]@[ssh.clear.rice.edu]
```
Example for Windows: https://imgur.com/a/CyQ2vU4

Doing this successfully will log you into CLEAR. It is important to note that you may need to type your password twice (once for the ssh client and once DUO authentication).

Once again, note that if you don't have a home drive when using Clear, please create a ticket in https://help.rice.edu Be sure to put "need clear home directory" in the subject and one will be setup for you.

You should now be logged into CLEAR.

## Further Customization
View the [extra notes](../installation/extra.md) for more information on how to customize your environment. Specifically, how to log into CLEAR faster and bypass DUO.



# Git
Git is a version control system that allows you to track changes to your files. Git is a free and open source software distributed under the GPL.
You will be learning about git in this class through the assignments.

If you are on a Unix system (please install WSL if you are on Windows), please follow this guide: https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup



# Git and github - submodule setup
if you need a refresher on using git in general, see “the basics” section below.

think of a submodule as a github repo inside another github repo (i know, woah).

- each class will have a repo for assignments.

- each student/group will create a totally separate repo for their work on their own gh account.
    - this should not live in the main work repo.

- each student/group will clone the main work repo, and then link their own repo as a submodule.

- each student/group will get rid of their local copy of the main repo.

- each student/group will work on their assignment in the repo created in step 1.

- the name of your submodule should be `lastname-firstname`

ok, but what about more instructions with examples you say?

1. create & clone your own repo
do all your work in this repo.

        $ git clone git@github.com:charlie/work-test.git
        cloning into 'work-test'...
        remote: counting objects: 3, done.
        remote: total 3 (delta 0), reused 0 (delta 0), pack-reused 0
        unpacking objects: 100% (3/3), done.
        checking connectivity... done.

2. clone the main repo

    
        $ git clone git@github.com:mks65/euler.git
        cloning into 'euler'...
        remote: counting objects: 14, done.
        remote: compressing objects: 100% (9/9), done.
        remote: total 14 (delta 5), reused 9 (delta 3), pack-reused 0
        unpacking objects: 100% (14/14), done.
        checking connectivity... done.


3. change into the correct directory and add your repo as a submodule

    submodule adding is done by: `git submodule add -b <branch> <url to your repository> <required submodule directory name>`


        $ cd euler/
        socrates: euler charlie$ cd 04/
        socrates: euler/04 charlie$ git submodule add -b main git@github.com:charlie/work-test.git cruz-charlie
        cloning into '04/cruz-charlie'...
        remote: counting objects: 3, done.
        remote: total 3 (delta 0), reused 0 (delta 0), pack-reused 0
        unpacking objects: 100% (3/3), done.
        checking connectivity... done.

4. commit and push changes

    
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
        

you do not have to pull first, but it is a good idea in case anyone has pushed before you have a chance to.

5. remove the main repo and go about your business

        $ cd ../../
        $ rm -rf euler/
