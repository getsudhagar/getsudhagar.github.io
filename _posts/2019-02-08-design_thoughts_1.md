---
layout: post-right-sidebar
title:  "Design Thoughts #1"
author: sal
categories: [Home,blog]
image: assets/images/design_thoughts1.jpeg
---
### Do code only for application’s business logic.

It means application should be coded for only whatever intention for application is created. There should not be any other logic or code around for any reason. That is, don't code for any other reasons like below

+ Purpose of investigation

+ Reporting for statistics

+ Reporting for auditing

+ Reporting for errors

There may be minimal coding in the worst case if we need some insight details of an application.

### What’s wrong if we write logic or code for other than business logic?

Writing logic for reporting and other than business logic is waste of time as there is another to get things done without spending more time on this.
We have to keep touch the business logic code whenever there are changes in requirement. If so, we have to modify the reporting logic code as well. This is never ending the waste of time for coding and testing effort.
Reporting logic will screw up whenever there are changes in application’s business logic.
If we don't code then how do we get it?

All kind of reporting should be generated based on how an application’s business logic is executed.

How does reporting tool know what application has executed?

Its all about logging. The way application is logging, whatever information application is logging, the reporting tool should be application pull that information for reporting.

### What is the good practice for application’s logging?

Many of us aware of what are all the different logging levels but many of us now aware of what info should go to what level and what are all the places logging required.

We should always keep in mind below thoughts on logging irrespective if what application or whatever language we use.

> “Whenever a request is executed on an application, if we see the log files we should know where does request flow over in the application’s code from beginning to end.”

> “In other words, an application log file should tell the story of application’s request execution details any point of time.”

### Reporting is okay but what about error reporting?

Again we should not write logic for any error handling. If we use proper error or exception handling framework, that will take care of all error reporting logic inside that framework.

Finally, a developer should code only for application’s business logic nothing more than that. Nothing more that.
