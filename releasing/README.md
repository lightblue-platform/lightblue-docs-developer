# Releasing
lightblue-platform releases are built with Maven and released to the [Open Source Software Repository Hosting (OSSRH)](http://central.sonatype.org/pages/ossrh-guide.html) available from Sonatype.  In order to be able to perform a release, you must be authorized to deploy to the lightblue, which can be be requested by opening an issue in the  [Sonatype JIRA Instance](https://issues.sonatype.org/). For example see [OSSRH-17406](https://issues.sonatype.org/browse/OSSRH-17406).

## Assumptions
1. You have received access from Sonatype to deploy to the lightblue-platform repositories.
2. You have write access to lightblue-platform repositories on github (needed for tagging).
3. You have a gpg key pair (see [working with pgp signatures](http://central.sonatype.org/pages/working-with-pgp-signatures.html)) to sign the release artifacts. Note this does not need to be a sonatype trusted key, any gpg key will do. The **public key has to be published to a public key repository**, otherwise the signing phase will fail.
3. You have the source checked out and you can build it locally.

## Maven-gpg-plugin setup

1. Add sonatype servers to settings.xml:
```xml
   <servers>
   <server>
     <id>ossrh</id>
     <username>login</username>
     <password>password</password>
   </server>
   <server>
     <id>sonatype-nexus-snapshots</id>
     <username>login</username>
     <password>password</password>
   </server>
   <server>
     <id>sonatype-nexus-staging</id>
     <username>login</username>
     <password>password</password>
   </server>
  </servers>
```
Where login/password are credentials to your sonatype account. For more information consult [sonatype documentation](http://central.sonatype.org/pages/consumers.html#apache-maven).

2. If you have more than one gpg key pair, you need to configure which one you want to use. In you settings.xml:
```xml
  <properties>
    <gpg.keyname>keyid</gpg.keyname>
  </properties>
```

## Release
Note: It is safe to interrupt release.sh script's execution with ctrl+c.

1. Cd to the repo you want to release
2. Make sure you are on the master branch (script will complain if you don't)
```
git checkout master
```
3. Run ./etc/release.sh <release version> <new snapshot version>
```
./etc/release.sh 1.2.0 1.3.0-SNAPSHOT
```
4. NOTES: You may be be asked to specify release and development versions for various lightblue artifacts. This might mean the sonatype promotion to the central maven repository is not complete. Note that some of them (lightblue-applications and lightblue-client) have different versionsing than remaining artifacts. This is a manual, error prone process. If you make a mistake, Ctrl + c and start again.
5. You will be asked to specify the password for your private gpg key.
6. You will be asked to specify the password for your github account.

### Release Order
Items must be built in the following order of phases, but items with the same phase number can be built simultaneously.

| Phase | Project|Note
| ----- | -------|----
| 1 | core|
| 2 | mongo|
| 3 | audit-hook|
| 4 | esb-hook|
| 5 | ldap? | Not officially released yet, but it is used as a test dependency for rest. So a new artifact may not need to be cut.
| 6 | rest|
| 7 | client|
| 8 | applications|
| 9 | migrator | manual
| 10 | bom |

## Troubleshooting
If you encounter errors at any point during the release of a lightblue-platform project, try re-running the release script, and see if that corrects the issue. The Maven release plugin is stateful, so it should be able to resume where it left off without retrying the steps that completed successfully.
