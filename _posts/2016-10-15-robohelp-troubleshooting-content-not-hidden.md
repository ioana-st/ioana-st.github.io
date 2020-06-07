---
layout: single
title: "RoboHelp: Troubleshooting Content Not Being Hidden in the Output"
date: 2016-10-15 12:01:48 +0300
classes: wide
categories: robohelp
---
Conditional tags can be tricky to troubleshoot in RoboHelp if you are not super familiar with how they work, and sometimes you may wonder why some content you thought was hidden is suddenly showing up in your online help.

Before you start troubleshooting this, it's important to remember that three steps are required to hide content in RoboHelp: define a conditional tag, apply it to the content, and exclude the tag from the output when you generate.

## Issue
You have a conditional build tag applied to some text or a topic, but the text/topic is visible in the generated output.

## Solution
First, check if all the CBT applied to the text/topics actually appear in the **Conditional Build Tags** pod.
* If all tags are displayed, check the conditional expression in the SSL.
  * If the expression is also correct, you either found a new bug, or you are doing something wrong :)
  * If the expression is not correct, fix it and regenerate the output.
* If tags are missing, follow the steps in [Troubleshooting Missing Variables and Conditional Tags](2016-09-12-robohelp-troubleshooting-missing-variables-cbt)

Second (if you are having problems with entire topics), make sure that you applied the conditional tag _in the **Project Manager**_. If you only apply the tag in the TOC, the topic will still be generated and will show up in searches.
