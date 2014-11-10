# Contribution Process
Not to sound process heavy, but through experience and best practicies we have landed on some flow to how changes happen to lightblue related projectcs.  This document intends to capture that to make it clear how thinks should work.

NOTE: this is NOT set in stone!  This is expected to change over time as better ways are developed and agreed on.  If you disagree with this document it's a great topic for discussion on the [developer forum](http://dev.forum.lightblue.io)!

## Tools
There is one board setup on [waffle.io](http://waffle.io) to bring all issues into a single kanban board:
* [documentation & development board](https://waffle.io/lightblue-platform/lightblue)

The flow of issues through each board is the same.

## General Flow
Issues not ready for any action can be discussed on the [developer forum](http://dev.forum.lightblue.io), [user forum](http://forum.lightblue.io/), or opened directly as issues in the appropriate github repository.  Once an issue is opened the general flow is outlined below.

### Prepare Issue for Work
These steps are done by contributors in general.

1. Issue goes to "Backlog" column on waffle.io.
2. Someone assigns a milestone. [optional]
3. [Tasks](https://github.com/blog/1375%0A-task-lists-in-gfm-issues-pulls-comments) are added in a comment. [optional]
4. Issue is moved to "Ready" column.

### Fix the Issue
Someone decides to work on the issue and:

1. Assigns the issue to themself.
2. Move issue to "In Progress" column.
3. Creates a fork of the master branch in their own user account on github.
4. Develop change in the fork.
5. Issue a [pull request](https://help.github.com/articles/using-pull-requests/) (PR) when work is complete.
    * The PR should reference the original issue(s) being fixed.

Where the pull request lands in the waffle.io columns dependends on who put in the request.
* Collaborator PR are put in the "Ready For Review" column.
* Non-collaborator PR are put in the "Ready" column.

### Merging Fixes

1. Someone other than the creator of the pull request reviews the pull request.
    * Blocking issues are noted in comments of the pull request.
    * Non-blocking issues are opened as new issues in the repo.
2. If review is good (no issues, clean merge, travis-ci build successful):
    1. The pull request is merged.
    2. If the original issue was not linked to the PR and therefore not auto-closed, the original issue should be closed manually.
3. If there are blocking issues, the original author fixes the issue and makes comments about resolution on the pull request


For more details of the tags and columns in [waffle](https://waffle.io/lightblue-platform/lightblue), take a look (or even comment if you have any question) on [this nabble thread](http://dev.forum.lightblue.io/How-to-improve-the-communication-solve-some-issues-the-best-way-to-the-asked-questions-to-issue-requd-td155.html#a163).
