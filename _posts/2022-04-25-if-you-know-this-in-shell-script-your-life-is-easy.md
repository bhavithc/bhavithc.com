---
layout: post
title: If you know this in shell script your life is easy
date: 2022-04-25 13:40 +0530
tags: [bash, shellscript]
categories: [shell-script]
---

There is one thing which we often get confuse in shell script, especially for those who are new to shell scripting

The difference between `$var`, `${var}` and `$(var)`

```bash
echo $var

echo ${var}

echo $(var)
```
I try to keep the concept as simple as that so that it should be helpful for new bees

Even before start, one thing we need to understand is in shell script to echo does not necessarily required quotes to print the message i.e. either double quote "" or single quote ''

Example:

```bash
my_shell $ echo "Hello world"

Hello world

my_shell $ echo 'Hello world'

Hello world

my_shell $ echo Hello world

Hello world
```
In above example the output remain same for all the types of echo. Please try yourself w.r.t adding double quote in single quote and vice versa

**$var** - When we echo $var the value saved in var is printed

```bash
my_shell $ var="World"

my_shell $ echo Hello $var

Hello World
```
**${var}** - There is no difference between `$var` and `${var}` except the way it is used. i.e. if there is a value which need to be print just after variable var then variable var should be enclosed with curly brace i.e. `${var}`

```bash
my_shell $ var="Linkedin "

my_shell $ echo "$varis very famous"

 very famous
```
In the above example `$varis` in echo is considered as one variable. Now in order to tackle this we need to use `${var}`

```bash
my_shell $ var="Linkedin "

my_shell $ echo "$varis very famous"

 very famous

my_shell $ echo "${var}is very famous"

Linkedin is very famous

```
In the above example you can see now $var is differentiated between the word is just by enclosing in a brace

**$(var):** This is called command substitution and there is no way it is linked or related to `$var` or `${var}`

This is used when we want to execute shell command and save the output to variable

Example:

```bash
my_shell $ var=$(echo "Hello world")

my_shell $ echo $var

Hello world

my_shell $ echo $(pwd)

/home/bhavith/Bhavith

```
In above example observer how var saved the output of `echo` and `pwd` output

Another example:

```bash
my_shell $ echo "List of files in $(pwd) is
>
> ---------------------
> $(ls)
> ---------------------"

List of files in /home/bhavith/Bhavith is

---------------------
c++
Development
Documents
doWhile
Junk
opensource
Youtube
---------------------
```
In the above example observe how `pwd` and `ls` is executed and echoed to console