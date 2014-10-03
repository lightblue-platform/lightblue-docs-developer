# Contribution Process
Not to sound process heavy, but through experience and best practicies we have landed on some flow to how changes happen to lightblue related projectcs.  This document intends to capture that to make it clear how thinks should work.

NOTE: this is NOT set in stone!  This is expected to change over time as better ways are developed and agreed on.  If you disagree with this document it's a great topic for discussion on the [developer forum](http://dev.forum.lightblue.io)!

## Tools
There is one board setup on [waffle.io](http://waffle.io) to bring all issues into a single kanban board:
* [documentation & development board](https://waffle.io/lightblue-platform/lightblue)

The flow of issues through each board is the same.

## General Flow
Issues not ready for any action can be discussed on the [developer forum](http://dev.forum.lightblue.io), [user forum](http://forum.lightblue.io/), or opened directly as issues in the appropriate github repository.  Once an issue is opened the general flow is:

1. Issue goes to "Backlog" column.
2. Someone assigns a milestone [optional].
3. [Tasks](https://github.com/blog/1375%0A-task-lists-in-gfm-issues-pulls-comments) are added in a comment.
4. Issue is moved to "Ready" column.
5. Someone decides to work on the issue and:
    1. Assigns the issue to themself.
    2. Move issue to "In Progress" column.
    3. Develop change in a fork.
    4. Issue pull request when work is complete.  Please include reference to the original issue.
6. PR goes to the "In Progress" column for collaborators.
    * Leave the original issue in the "In Progress" column until PR is merged.
    * Not put in Ready for Review simply to allow continued work against the PR.
    * When done, move PR to "Ready for Review".
7. PR goes to the "Ready for Review" column for non-collaborators.
    * Rational is that a PR from external is issued only when the code is done.
8. Someone other than the creator of the PR moves the PR to the "In Review" column.
9. If review is good (no issues, clean merge, travis-ci build successful):
    1. The PR is merged.
    2. Remove the "in review" tag from the PR. [optional]
    3. Remove the "in progress" tag from the original issue. [optional]
    4. If the original issue was not linked to the PR and therefore not auto-closed, the original issue is closed.
10. If there is a question or issue with the PR it is moved back to the "In Progress" column.
    * Note open question on how to handle this for non-collaborators, since they cannot move cards around on waffle.io.


For more details of the tags and columns in [waffle](https://waffle.io/lightblue-platform/lightblue), take a look (or even comment if you have any question) on [this nabble thread](http://dev.forum.lightblue.io/How-to-improve-the-communication-solve-some-issues-the-best-way-to-the-asked-questions-to-issue-requd-td155.html#a163).
