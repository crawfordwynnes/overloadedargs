---
layout: post
title:  "Rails Preproduction Checks"
date:   2019-05-11 18:04:08 +0100
categories: jekyll update
---

## Rails Preproduction Checks

### Server

Check patch levels for whichever OS or software you are using. Are you storing your data in line with Data Protection Directive or GDPR? e.g. Heroku in EU zone? What level of load-balancing do you have? Do you have external APIs that need to go through a proxy? Which web and app servers will you use? What will you use for monitoring and security, how long must you retain server logs?


### Version Control


Which version control system? 

Do you have practices for development, do you have a branch policy? Do you use tagging?

Do you rebase commits onto master, do you use a staging branch, can you deploy feature branches?

Are there external scripts you can use to help you, e.g. Githooks to configuration management.


### Ruby Version


Check that you are using the latest patch version of specific Ruby you want.

Can you tune the Garbage Collector? How are you specifying ruby version between development and production environment. RVM/RBenv? 

Rails Version – Make sure you have most up to date patch version of Rails. Also make sure JSON gem and others are up to date so they don't have security problems. Are you using middleware? Are you using self-signed SSL certs in development?


### Ruby Code


Static analysis tools to measure complexity? https://www.ruby-toolbox.com/categories/code_metrics.

Can you use CodeClimate online!

Check best practices for Rails: https://github.com/flyerhzm/rails_best_practices 


### Code Smells/Design Smells


Are you using too many classes or not enough controllers? Fat Models/Skinny Controllers? Can you use concerns? Any repetition of code, do you use DRY? Does code live in a place far away from where it's used? Is code shared across apps in a really obfuscated way. Is meta programming understandable? Has the programmer written code for which there are already methods in the Ruby API? Are Service Objects and Gem's used appropriately?

### Database

Scan your database and make sure you have indexes where needed. Will it scale really quickly? Can you denormalize tables before you would need to? What's monitoring the database? Does your database represent your data? e.g. use NoSQL or graph database? Are you getting reliable backups? Can you supply filtered database snapshots e.g. for staging?

Who can log into your database boxes? Do they need to go through another machine?


### Authentication


Are you using salts for your passwords? Are you using a good hashing algorithm for passwords? Are you implementing a secure version of OAuth standards? How are you generating security tokens? Are you logging everything? To what level? Where are you logging, consider external logging with rsyslogd. Don't log passwords!


### Front End Code


Is there logic in your views, are you using helpers properly, could you separate logic into a presenter pattern of some sort? 

Do you know all of the assets and JavaScript your adding into your page. Are you sure there are no nefarious Javascripts? What other technology are you using on the front end? Flash/Silverlight/WebGL? 

Are your assets up to date e.g. jQuery? 

Can you use Shape Security real time polymorphism to defend botnets?


### Test Code


Use things like Metric Fu to measure complexity and code coverage. Churn is an important metric. 

Do you have Acceptance/Integration tests? Should you use Capybara/Cucumber? What test data are you using? Are you stubbing out requests to external APIs? Webmock/VCR. How are you testing edge cases? Timecop is an awesome Gem.


### Cross Browser Compatibility


Does your interface work on all resolutions? Which browsers will you support? Do you have a responsive design?


### Mobile Compatibility


What type of mobile devices/standards will you support? Are you using redirects to a mobile web site or a notice for a native app?


### Deployment Practices


Who can deploy? When can they deploy? Will it run the tests before deploy? Will you use Continuous Integration/Continuous Deployment? Where are you storing your API keys? Will it need to load-balance between the latest version and old versions for no down-time? Are applications split into services, how are they discovered? How is job scheduling handled? Do you use system scheduling and cronjobs?


### Performance Monitoring


Use New Relic for performance monitoring. Consider using off the shelf tools to do load testing. Can you write your own load testing? What will you use Selenium? HTTPerf? 

Use Exception notifiers e.g. Airbreak, Sentry.


### PCI Compliance


If your handling payment information make sure you look at PCI compliance, HTTPS for API requests to external vendors even when loading the form? Are there any spurious text files storing credit card information?


### Internationalisation


Do you need internationalisation? How are you managing your translations and config?


### Testing


Do you have a staging/qa/uat environment? Who is testing using what frameworks? Can you use Browserstack for mobile device testing?