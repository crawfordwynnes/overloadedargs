---
layout: post
title:  "The Magic Of Git Hooks"
date:   2022-12-30 12:00:00 +0000
categories: jekyll update
---

I wanted to write a post about git-hooks, because I think they are underused. They are very flexible. You can use them to aid your team's productivity and can use them in many ways - even if your team unfortuantely adhears to 'if you break the build you fix the build'.

In each Git repository you will find a hidden folder at .git, which is created every time you 'git init'. Inside of this folder you will also find the /hooks folder. So for example if you want to run a script before you commit then you can use a pre-commit hook. You create a file called pre-commit and make it into a bash script by adding #!/bin/sh at the top of the file (also setting executable bit). Then for instance you can do all sorts of things such as force the test suite to be run locally and pass, you could make sure your code base passes linter specifications or other style guides, you can run static scanners, or you can just make sure that the comitter has checked which branch they are commiting to. I think this is helpful because if you've ever worked with a collabaritive master branch (now main) then once you push to a remote it can be hard to rectifiy your changes, and the general pattern is to make sure you avoid having to force push to fix the problem.

I've never really used git-hooks extensively but that's why I wanted to write about them, they seem like a really cool light way of improving productiviy in a team environment or solo environment. I am also interested if it forces teams to follow the same git flow model because in 2019 there was a new pre-commit-merge hook that may stop the user from merging if it's not a rebase. There's probably also a way to ensure one commit, the squashing pattern.

Also a really vague thing that I just want to add about Git is that there are sometimes really weird things that can happen with Git that you wouldn't expect. I came across a problem once - a really long time ago - where the repository couldn't get recent changes because there was some kind of binary blob in one commit. So the repository could only be cloned and then pulled with cache (can save network requests). I think git hooks would have solved this problem. 

The other thing about Git repositories is that if they are absolutely huge then there may be problems cloning the repository onto your machine because of memory contsraints.
To get the code base, with commit history, on to your machine you may have to shallow clone and then pull in steps potentially modifying memory requirements and proceed from there.

There is a common problem that you can get with previous sensitive information being committed to a repository, to fix these problems you have to get into the area of rewriting git history, which is something you never want to do. I've had a lot of success with 
[BFG-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) for removing sensitive data. To do this simply remove the entire files with BFG cleaner and if they had code in that you need you can add them again.

Make sure you back up your repo!