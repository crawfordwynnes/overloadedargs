---
layout: post
title:  "Useful Commands For Navigating The Shell & Psql Sessions"
date:   2019-10-18 14:30:08 +0100
categories: jekyll update
---
ZShell

I use ZShell and today I found cd ... which goes up two directories. I am guessing it's an alias method defined in a shell config file somewhere.

I also need to remember Ctrl-e to jump to the end of the line, Ctrl-a to the beginning.

Also I found forward interactive search with ctrl-s which is the opposite of backward interactive search ctrl-r which I use quite a lot.

Databases

If you are a Mac user you may be able to use Function-left and Function-right to jump to the end of lines when you are in a Psql session. 

Also you can use commands like Psql -U postgres DATABASE_NAME < DATABASE backup to import a Psql dump.

Just a random note about when using Psql to connect to a database with a password you need to set the h option with localhost to get a password prompt. The -p option won't work.

Using multiple terminals

To use more than one terminal at the same time, programs like ITerm2 have the option to open tabs. These can be switched using ctrl-w-left and ctrl-w-right.

There is also tmux for using separate sessions within one terminal.

Switching windows in Vim

In Vim you can quite easily separate the editor into different sections using horizontal and vertical split using :vsp and :sp. These allow you to compare files more easily. You can switch the focus of these using CMD-SHIFT left and right.