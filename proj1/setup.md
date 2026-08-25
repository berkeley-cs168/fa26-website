---
layout: page
title: "Project 1: Setup"
nav_order : 2
parent: "Project 1: Traceroute"
---

# Project 1: Setup

## Operating System Installation

This project has been tested to work on Linux and Mac.

If you're on Windows, you need to install Windows Subsystem for Linux (WSL). WSL is a tool which allows you to run a Linux environment directly on Windows. To install WSL,
1. Open PowerShell as Adminstrator, then run `wsl --install`. This should install the Ubuntu distro. When prompted, restart your computer.
2. Open the Ubuntu terminal from the Start Menu. It should prompt you to choose a username and password. Choose whatever you like and remember it.

If the installation was successful, you should see `username@hostname:~$` in your terminal. If you are using VSCode, you can click on the "><" arrows in the bottom left corner and select "Connect to WSL" to open a Linux environment. Other code editors might also have options to connect to WSL.

### Note on File Location (Windows Users)

After downloading the starter code (see below), do not run Linux commands on files located in the Windows filesystem (those under `/mnt/c`). This can be frustratingly slow because you are running Linux commands on the Windows disc. Instead,
1. Create a project directory in your WSL home using `mkdir -p ~/projects`.
2. Copy the extracted starter code into `~/projects` using `cp -r /mnt/c/Users/<username>/Downloads/cs168-{{ site.semester_slug }}-proj1-traceroute ~/projects/`. You can also drag the folder into the Explorer sidebar tab if you are using VSCode.
3. Navigate to the  folder in your terminal using `cd ~/projects/cs168-{{ site.semester_slug }}-proj1-traceroute` and work from there.

## Python Installation

This project has been tested to work on Python 3.11. (It probably works on newer versions of Python as well, but use at your own risk.)

If you run `python3 --version` or `python --version` in your terminal and you see output of the form `Python 3.11.*`, you're all set.


## Starter Code

[Download a copy of the starter code here.](/assets/projects/proj1/cs168-su26-proj1-traceroute.zip)

In your terminal, use `cd` to navigate to the `cs168-sp26-proj1-traceroute` directory. All of the Python commands should be run from this directory.

To check that your setup works, in your terminal, run:

```bash
sudo python3 traceroute.py cmu.edu
```

If you see something like this, everything should be set up correctly:

```
traceroute to cmu.edu (128.2.42.10)
 1: *
 2: *
 3: *
(some lines omitted...)
26: *
27: *
28: *
29: *
30: *
```

You should only edit `traceroute.py`. There are comments clearly indicating the places where you should fill in code.

Guidelines:
- Don't modify any other files.
- Don't add any new files.
- Don't add any imports.
- Don't edit any code outside the sections indicated by the comments.
- Don't add any hard-coded global variables. 
- Adding helper methods is fine (and encouraged).
- In general, if we haven't told you to use something, you probably don't need it. Our goal is not to trick you!

