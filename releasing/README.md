# Releasing
lightblue-platform releases are built with Maven and released to the [Open Source Software Repository Hosting (OSSRH)](http://central.sonatype.org/pages/ossrh-guide.html) available from Sonatype.  In order to be able to perform a release, you must be authorized to deploy to the lightblue, which can be be requested by opening an issue in the  [Sonatype JIRA Instance](https://issues.sonatype.org/).

## Assumptions
1. You have received access from Sonatype to deploy to the lightblue-platform repositories
2. You have the source checked out
3. You can build the source locally

## Setup

1. GPG Setup - http://central.sonatype.org/pages/working-with-pgp-signatures.html
2. Maven /usr/share/maven/conf/settings.xml Setup
  1. Add "server" for each of these to "servers" section.  Each must have fields "id", "username", and "password".
    1. id = ossrh
    2. id = sonatype-nexus-snapshots
    3. id = sonatype-nexus-staging
3. Maven ~/.m2/settings.xml Setup
  1. http://central.sonatype.org/pages/consumers.html#apache-maven
4. mvn on PATH

## Release
1. cd to the repo you want to release
2. Make sure you are on the master branch (script will complain if you don't)
```
git checkout master
```
3. Run ./etc/release.sh <release version> <new snapshot version>
```
./etc/release.sh 1.2.0 1.3.0-SNAPSHOT
```

## Troubleshooting
If you encounter errors at any point during the release of a lightblue-platform project, try re-running the release script, and see if that corrects the issue.  The Maven release plugin is stateful, so it should be able to resume where it left off without retrying the steps that completed successfully.
