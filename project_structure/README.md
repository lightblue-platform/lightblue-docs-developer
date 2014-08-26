# Project Structure

There are many repositories that make up lightblue.  Here's the high level overview of what is in each.

## lightblue-core
Core lightblue fucntionality.  In scope are the following in the puzzle:
* lightblue CRUD Layer
* abstract code for the metadata and controller plugins

https://github.com/lightblue-platform/lightblue-core

## lightblue-mongo
The MongoDB metadata and CRUD controller implementation.

https://github.com/lightblue-platform/lightblue-mongo

## lightblue-rdbms
The RDBMS CRUD controller implementation.

https://github.com/lightblue-platform/lightblue-rdbms

## lightblue-rest
The lightblue REST API and implementation.

https://github.com/lightblue-platform/lightblue-rest

## lightblue-audit-hook
The generic lightblue audit-hook implementation.

https://github.com/lightblue-platform/lightblue-audit-hook

## lightblue-applications
There are two management applications that enable easier use of metadata and data services.  This repository is for that code.

https://github.com/lightblue-platform/lightblue-applications

![Lightblue Applications](https://raw.githubusercontent.com/lightblue-platform/lightblue/master/docs/overview.png)

## openshift-lightblue-all
All rest apps and web apps bundled into a single WAR for deployment on OpenShift.  See [lightblue in action](../develop_on_openshift/README.md).

https://github.com/lightblue-platform/openshift-lightblue-all

## lightblue-tests
All functional (integration) tests and load tests for lightblue.  These tests can be executed for every deployment to verify that lightblue is working well.

https://github.com/lightblue-platform/lightblue-tests

## lightblue-docs-overview
[Lightblue overview documentation](http://lightblue.io/).

## lightblue-docs-user
[Lightblue user guide](http://docs.lightblue.io/).

## lightblue-docs-developer
[Lightblue developer manual](http://dev.docs.lightblue.io/).

## pyresttest
A python based rest testing framework forked from [svanoot/pyresttest](https://github.com/svanoort/pyresttest).  Functionality is added as needed and all changes are sent up-stream.

https://github.com/lightblue-platform/pyresttest

## shakespears-monkey
A library written on java for generating JSON documents that meet a documented structure.  For example, to create test data you might use shakespears-moneky to generate a few million records.

https://github.com/lightblue-platform/shakespeares-monkey
