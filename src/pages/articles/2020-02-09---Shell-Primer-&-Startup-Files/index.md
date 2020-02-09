---
title: Shell Primer & Startup Files
date: "2020-02-09T16:51:00.000Z"
layout: post
draft: false
path: "/posts/shell-primer-&-stratup-files/"
category: "Gatsby"
tags:
  - "SHELL"
  - "Productivity"
description: "This is a Shell primer focused on Mac and Zsh"
---

## Shell Primer & Startup Files

What shell I use
```
ZSH
```
Why I use it 
- `zsh` + [ohmyz.sh](https://ohmyz.sh/) = access to cool [plugins](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins)
- Does more than what your out of the box shell can do

What is a shell
- A program that takes commands from the keyboard and gives them to the operating system to perform

Being a Mac user, what shell does it come with?
- Bourne SHell (sh)
- Bourne Again SHell
- Z shell (zsh)

`bash` vs. `zsh` vs `sh`
- `sh` is default of some Unix versions 
- `bash` is an improvement of `sh` 
- `zsh` is an improvement of `bash`

What is Unix
- **UNIX**  is an operating system and proprietary. (_not free_)
- **Linux**  is a unix-like operating system  **_kernel_**  and open source. (_free_)

What is a Linux Distro
- It is an operating system that uses Linux as the kernel
	- i.e. Ubuntu, CentOS

How to check what shell is in use
```bash
echo $SHELL
```

Switching your default shell (Mac)

```bash
chsh -s /bin/zsh  
chsh -s /bin/bash

# you need to open a new terminal window to see results
``` 
Startup files are initialized when you open a shell session. [more info](http://zsh.sourceforge.net/Intro/intro_3.html) 
```bash
$HOME/.zshenv
$HOME/.zprofile
$HOME/.zshrc
$HOME/.zlogin
$HOME/.zlogout
```

`.zshenv` 
- is _sourced_ on all invocations of the shell
- It should contain commands to set the command search path, plus other important environment variables.

**source** 
- is a shell built-in command which is used to read and execute the content of a file

`.zshrc` 
- is sourced in interactive shells. It should contain commands to set up aliases, functions, options, key bindings, etc.

`.zlogin` 
- is sourced in login shells. It should contain commands that should be executed only in login shells.

`.zprofile`
- alternative to `.zlogin`

`.zlogout` 
- is sourced when login shells exit

_login shell_
- important thing to remember is it sources `.zprofile`

check if shell is a `login shell`
```bash
echo $0
-zsh # "-" is the first character. Therefore, this is a login shell.
```
_non-login shell_
- a.k.a. interactive shell
- sources `.zshrc` but not `.zprofile`

My personal preference
- I only use `.zshenv` and `.zshrc`

Example `.zshenv` content

- env vars

```bash
# AWS
export AWS_CONFIG_PATH="$HOME/.aws"

# WORK DIRS
export WORK_DIR_="$HOME/Desktop"
export PERSONAL_DIR="$WORK_DIR_/__PERSONAL"
```

- path

```bash
# php stuff
export PATH="$HOME/.composer/vendor/bin:$PATH"

# I think this is drupal stuff
export PATH="/Applications/DevDesktop/tools:$PATH"

# Scripts to make life easier
export MY_SCRIPTS="$HOME/scripts"
export PATH="$MY_SCRIPTS:$PATH"
```

`$PATH`
- tells the shell which directories to search for executable files

Example `.zshrc` content
```bash
alias vup="vagrant up"
alias vssh="vagrant ssh"
alias vreload="vagrant reload"
alias vdesup="vagrant destroy && vagrant up"
```