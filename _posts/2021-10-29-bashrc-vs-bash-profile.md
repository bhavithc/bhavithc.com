---
layout: post
title: ".bashrc Vs .bash_profile"
date: 2021-10-29 20:02 +0530
---

# What is the difference between .bashrc and .bash_profile

Sometime many of us get confused whether to place our alias, export’s and init things in **.bashrc** or **.bash_profile. **

The answer to this doubt is very simple. Whenever you want to execute the commands automatically when user logins, place them in **.bash_profile**. Similarly whenever you want to execute the commands automatically when terminal is opened or /bin/bash is executed place then in **.bashrc**

In simple words,

- When user logs in to the Linux using username and password either locally or from remote via ssh the **.bash_profile** gets invoked
- When user open’s a new terminal or executes /bin/bash the **.bashrc** gets invoked

### Further information

Basically **.bashrc** and **.bash_profile** are the two hidden files which are present under home directory in Unix like operating system(s). These are the files which is used to execute shell commands during login or when terminal is opened. 
**RC** in **bashrc** stands for run command.

*Tips:*

If you want to execute same commands both in **.bash_profile** and **.bashrc**. Place them only in **.bashrc** and source **.bashrc** inside **.bash_profile**

Example:

**~/.bash_profile**

```bash
source ~/.bashrc
```

**~/.bashrc**

```bash
alias l=”ls -ltr”
alias v=”vim”
alias gp="git pull"
```

In above example both when user logs in and terminal is invoked .bashrc is invoked

***Note:***
1.     Each Unix like operating system have their own conventions, some OS use **.profile ** instead of **.bash_profile**
2.     If your shell is bash then you have **.bashrc**, similarly if you using **zsh** then rc file will be **.zshrc**. Even vim and other applications like task uses same method to give user the flexibility to do some initialization stuff. Vim uses **.vimrc** and task(Taskwarrior) uses **.taskrc** to run initial commands