---
layout: post
title:  "Better way of navigating directories in Linux"
categories: tutorials linux
athor: mihna123
last_modified_at: 2025-05-30 16:21:30 +0100
---

Have you ever thought: "Maan I wish I could navigate trough my directories 
faster?"

Maybe yes maybe no, but I sure did. I am always trying to maximize my desktop 
setup efficiency. This is the reason that I am using **i3 window manager** 
coupled with **zshell** and **neovim**. This settup has been a life saver 
honestly and very fun to use. 

# The small problem

I noticed that every time I wanted to open up a project directory it would be
the same old sameold: Type in `cd` then try to remember where the directory 
I'm looking for is. Maybe Documents? Maybe Projects? Who knows? We will have to 
try them all. Eventualy you find it and its all good, you wasted a couple of 
seconds and who cares?

This is what the classic experience looked like:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/fzf-01.gif" | prepend: site.url}}" width="800" alt="Navigating directories in linux using cd"/>
</p>

I did it like this for years until I stumbled upon this interesting tool called
**the fuzzy finder** or **fzf**. This tool is just so powerfull and fast. What 
it essentialy does is given some input of lines it filters out the one line 
that matches the given query the most. Could this be used to traverse the 
directory tree faster? The answer is yes.

# How to set it up

So firstly install the tool if you already don't have it on our system. If 
using apt it is gonna be `sudo apt install fzf`. 

Secondly, if you try and just type in `fzf` in your terminal, you will notice
that it tries to find files and not directories. The solution is that you pass
to it only directories using the `find` command.

The full command is `find . -type d -print` to print out all directories going 
from current directory.

# Putting it all together

Full command you should be using to go to desired directory is 
`cd $(find . -type d -print | fzf)`. Since it is a long command i sugest making
an alias to it. Something like `fd` (find directory) works for me.

To make an alias for **zshell** edit the `~/.zshrc` file. For **bash** it is 
`~/.bashrc`.  
Add this line at the end of the file:
`alias fd="cd \$(find . -type d -print | fzf)"`. The forward slash before the 
`$` sign is very important. If you type it in withought the forward slash, the 
command is going to execute just once every time you open up your shell.

This is the end result:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/fzf-02.gif" | prepend: site.url}}" width="800" alt="Navigating directories in linux using fzf"/>
</p>
