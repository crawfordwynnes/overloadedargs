---
layout: post
title:  "Git vs Https Protocols"
date:   2019-04-14 14:30:08 +0100
categories: jekyll update
---

## Git Protocol for pushing where git protocol is blocked

When using the git protocol as opposed to the ssh protocol facilitated by a daemon, does not use any authentication.

If for some reason you cannot connect over the git protocol and have tried setting rules on your router for port forwarding, e.g. port 9418 for git:// then you should be able to run

git config --global url."https://github.com/". instead of  'git@github.com:'

to force git to use https instead.

However it may be better to use the ssh protocol.