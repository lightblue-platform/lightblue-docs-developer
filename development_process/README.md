# Contribution Process
Not to sound process heavy, but through experience and best practicies we have landed on some flow to how changes happen to lightblue related projectcs.  This document intends to capture that to make it clear how thinks should work.

NOTE: this is NOT set in stone!  This is expected to change over time as better ways are developed and agreed on.  If you disagree with this document it's a great topic for discussion on the [developer forum](http://lightblue-dev.1011138.n3.nabble.com/)!

## Tools
There are two boards setup on [waffle.io](http://waffle.io) to bring all issues into a single kanban board:
* [development board](https://waffle.io/lightblue-platform/lightblue)
* [documentation board](https://waffle.io/lightblue-platform/lightblue-docs-overview)

The flow of issues through each board is the same.

## General Flow
Issues not ready for any action can be discussed on the [developer forum](http://lightblue-dev.1011138.n3.nabble.com/), [user forum](http://lightblue-users.1011140.n3.nabble.com/), or opened directly as issues in the appropriate github repository.  Once an issue is opened the general flow is:

1. Issue goes to "Backlog" column.
2. Someone assigns a milestone [optional].
3. [Tasks](https://github.com/blog/1375%0A-task-lists-in-gfm-issues-pulls-comments) are added in a comment.
4. Issue is moved to "Ready" column.
5. Someone decides to work on the issue and:
    1. Assigns the issue to themself.
    2. Move issue to "In Progress" column.
    3. Develop change in a fork.
    4. Issue pull request when work is complete.  Please include reference to the original issue.
6. If a non-collaborator issued the PR it will be put into the "Ready" column.
7. If a collaborator issued the PR it will be put in the "Ready for Review" column.
    * Note, please leave the original issue in the "In Progress" column until PR is merged.
8. Someone other than the creator of the PR moves the PR to the "In Review" column.
9. If review is good (no issues, clean merge, travis-ci build successful):
    1. The PR is merged.
    2. Remove the "in review" tag from the PR.
    3. Remove the "in progress" tag from the original issue.
    4. If the original issue was not linked to the PR and therefore not auto-closed, the original issue is closed.
10. If there is a question or issue with the PR it is moved back to the "In Progress" column.
