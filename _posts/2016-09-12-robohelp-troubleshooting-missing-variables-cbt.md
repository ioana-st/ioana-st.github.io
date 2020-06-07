---
layout: single
title: "RoboHelp: Troubleshooting Missing Variables and Conditional Tags"
date: 2016-09-11 13:01:48 +0300
classes: wide
categories: robohelp
---
We often have problems with RoboHelp elements such as variables, conditional build tags, baggage files, etc that go missing. This post gives a few pointers of things to investigate if that happens.

Our environment is RoboHelp 2015 with SVN as source control (using TortoiseSVN), but the basic idea applies to all versions of RoboHelp (Classic). Actually, this applies to pretty much anything that goes missing in RoboHelp, since almost everything is stored in an `.apj` file.

## Issue
* Tags/variables that were already defined do not appear in the **Conditional Build Tags** or **User Defined Variables** pod.
* Variable errors are generated in the output (_Variable is undefined_).
* Baggage files no longer appear in **Project Files**.

## Solution
This is usually either a SVN problem or a RH refresh problem.

First, check which application is at fault:
1. Open the project source folder in Windows Explorer. 
2. Open the `rhbuildtag.apj`, `rhvariable.apj`, `rhbag.apj` file etc, as applicable, in  Notepad (or any other text editor). 
3. Check if your missing tags are in the file.

If the tags are in the file, it's a RoboHelp problem (RoboHelp is not "seeing" the most recent version of the file).

**Note**: You don't need to close RoboHelp while trying the following fixes.

To force a refresh:
1. In Notepad, type anything in the file, then delete the change (for example, type and then delete a space). You need to do this to "fake" a change, so that the text editor allows you to re-save the file. (Might not apply to all text editors.)
2. Save the file.
3. Check RoboHelp - your tags should start appearing in real-time.

If the tags are not in the file, it's an SVN problem. To fix it:

1. Right-click the file and select **TortoiseSVN &gt; Show Log**.
2. From the log window, open the most recent version of the file.
3. Check whether the missing tags are displayed there.
    * If the file in the repository has the tags, it means your copy was not updated correctly. Either try to update the file again from SVN, or delete it and update the entire folder. (Note: only do the latter if you are sure you don't have any un-commited changes in the file, like new build tags.)
  	* If the file in the repository does not have the tags, it means that the person who added the tags did not commit the file correctly or SVN had a glitch. Either have that person commit the file again (assuming her/his local version displays the tags) or simply recreate the tags yourself using the RoboHelp GUI.