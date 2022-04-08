---
layout: post
title:  "Rails Whenever, Capistrano & Cron"
date:   2019-10-18 14:30:08 +0100
categories: jekyll update
---
Cron comes from the 1970s but is still used in Rails apps to schedule jobs on VPS.

There are two ways in Rails to set the crontab from your config/schedule file which has a handy DSL for scheduled tasks.

After your project has been wheneverized then you can run e.g.

bundle exec whenever --update-crontab --set 'environment=staging' staging

on the server which sets the crontab.

You can check this with crontab -e.

If you deploy with Capistrano you won't need to do this and you can require 'whenever/capistrano'
which will add a task to update the crontab on deploy.

 You can list the crontab with crontab -l

 and clear the crontab with crontab -r