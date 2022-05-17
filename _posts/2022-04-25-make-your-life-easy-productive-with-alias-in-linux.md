---
layout: post
title: Make your life easy & productive with alias in Linux
date: 2022-04-25 13:39 +0530
tags: [bash, shellscript, linux, alias]
categories: [shell-script]
---

When I worked first time with Linux (Ubuntu), I use to remember lot of commands required for my project. In order to make myself productive I started creating either a text file called `help.txt` or create a notes in OneNote to save all those commands. So when ever I need a command I use to copy the command from my `help.txt` file and use `Ctrl + Shift + v` to paste on my terminal and go on.

One day I had been to Linux training where I observes that, to list the file the trainer was executing ls command, but the output of `ls` was `ls -la`. I was totally confused and thought that he might be using some third party ls  (As a new bee in Linux world, I assumed that trainer might have done `sudo apt-get install third-party-ls` or some thing similar).

First and foremost his personal PC was also a Linux (Ubuntu distro). The most curious thing which I found was he installed some software in front of me using please install `some-software`. I had gone mad and without holding my curiosity I asked trainer that how come your system has please instead of `sudo`. He replied that he is alias `sudo` to please. Then I nodded my head like as if I know alias and kept quite. Then I searched on internet and found the power of alias and from that day onwards I become a fan of alias.

Keeping all the story aside let see what is alias.** In simple alias is a custom name that we can give to any command/commands.**

Example

With out alias 

```bash
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ ls
'Mastering Embedded Linux Programming - Second Edition.pdf'   a.out   lambda.cpp   learning_cmake.pdf
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$
```

With alias

```bash
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ alias ls="ls -la"
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ ls
total 6584
drwxrwxrwx 1 bhavith bhavith    4096 Mar  1 22:56  .
drwxrwxrwx 1 bhavith bhavith    4096 Mar  2 00:09  ..
-rwxrwxrwx 1 bhavith bhavith 6184516 Dec 10 21:05 'Mastering Embedded Linux Programming - Second Edition.pdf'
-rwxrwxrwx 1 bhavith bhavith   20976 Mar  1 22:56  a.out
-rwxrwxrwx 1 bhavith bhavith    3257 Mar  1 22:57  lambda.cpp
-rwxrwxrwx 1 bhavith bhavith  525865 Dec 10 19:22  learning_cmake.pdf
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$
```
#### How to make alias for multiple commands at once

To alias multiple commands use function of bash script 

Example

```
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ echo "function setupMyWorkspace()
> {
>     echo "Setting up the workspace"
>     mkdir myworkspace
>     cd myworkspace
>     touch readme.md
>     echo "Workspace created"
> }" > myalias.sh
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ source myalias.sh
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning$ setupMyWorkspace
Setting up the workspace
Workspace created
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning/myworkspace$
```
#### Explanation:

- Create a function `setupMyWorkspace` and save it in a file called `myalias.sh`
- Source `myalias.sh` to make this function visible (expose) to running terminal 
- Execute your new command setupMyWorkspace you are done

### What will happen to my commands if terminal closed ?

If you close your terminal then all the alias created will be removed. Again you have to create new alias for one line commands and source **myalias.sh** to use `setupMyWorkspace` command.

But don’t worry there is way to make your commands available once terminal is opened.  The way is paste all your commands to the end of **~/.bashrc** file so that every time you open the new terminal all your alias present for you. beautiful right ? 

Also you can paste your commands in **~/.bash_profile** But I don’t recommend to do so. To understand the difference between ~/.bashrc and ~/.bashprofile refer .bashrc Vs .bash_profile blog 

***Tips:***
- If you are using **bash** save your alias in  **~/.bashrc** and if you are using **zsh** (Z shell) save in **~/.zshrc**
- If **~/.bashrc** (commonly called as rc files) is not present no worry nothing will happen, just create one
- To know which shell are you using execute echo ${SHELL} then you know which shell is currently you are using example

```bash
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning/myworkspace$ echo ${SHELL}
/bin/bash
bhavith@DESKTOP-M98L7FO:/mnt/e/Learning/myworkspace$
```

### Enough is enough please provide your alias 🙂 

Always try to understand or use your senior’s .bashrc or .vimrc (For vim). I know many of them disagree to provide one, as if there is a rocket science in it. Don’t worry I have pasted some snippet for reference to understand how really we can use alias to make our life easy and productive

Below is my simple alias snippet 

```bash
alias ev="vim ~/.vimrc"     #Edit .vimrc
alias eb="vim ~/.bashrc"    #Edit .bashrc 
alias rc="source ~/.bashrc" #Source .bashrc

# Git aliases 
alias gdns="git diff --ignore-submodules=dirty"
alias gco="git checkout"
alias gs="git status"
alias gd="git diff"
alias edit_cscope_vim="vim ~/.vim/plugin/cscope_maps.vim"
alias tr="tree -L "


# Multiple command aliases using functions

#Extract iso image
function extract_img()
{
    source_file=$1
    dest_file=$(echo $(basename $1) | cut -f 1 -d '.')
    dest_gz=$dest_file".gz"
    out=$dest_file"_out"
    
    echo "Extracing $1 please wait ..."
    cp $source_file $dest_gz
    gunzip $dest_gz
    rm -rf $out;mkdir $out
    cd $out
    echo "Uncompressing $dest_file ..."
    cpio -i <../$dest_file
    echo "Find the uncompressed image in $out"
    cd ../
    rm -f $dest_file
}

# Prepare CSCOPE (It also prepare for CTAGS)
function pcs()
{
    echo "Cscope setup started ..."
    find $(pwd) -name "*.c" -o -name "*.h" > $(pwd)/cscope.files
    cd $(pwd)
    cscope -b
    #ctags too
    ctags -R
    CSCOPE_DB=$(pwd)/cscope.out;export CSCOPE_DB
    echo "Cscope setup is finished !"
}

# Clean ctags and cscope
function ccs()
{
    echo "Cleaning cscope ..."
    echo "Executing rm cscope.files cscope.out"
    rm cscope.files cscope.out
    echo "Cscope cleaned !"
}
```
