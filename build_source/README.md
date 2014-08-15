# Build from Source

## Dependencies
* rpm-build
* git
* maven (3+)
* java (1.7)

```
sudo yum install rpm-build git maven java-1.7.0-openjdk-devel
```

## Get the Source
```
git clone https://github.com/lightblue-platform/lightblue.git
cd lightblue
```

## Setup Environment
There is a possibility of a [man-in-the-middle attack](https://github.com/lightblue-platform/lightblue/issues/106) when getting artifacts from Maven Central with default settings.  The root directory of each maven project has a settings.xml file that will override the default.  It is recommended that this setup is used.  There are a few options for using the override:
* Copy the settings.xml to your ~/.m2 directory.
* Specify the file with each build using option `-s ./settings.xml`.
* [Configure this yourself](http://central.sonatype.org/pages/consumers.html#apache-maven).
* Do nothing, use defaults (**NOT RECOMMENDED!**).

This documentation assumes the first option, copying settings.xml to ~/.m2 directory, and does not make mention of this issue again.


## Build Options
You can build lightblue without any particular options set.  It will create all the java artifacts but not package them for deployment (unless you want to just grab the WAR).
```
mvn clean install
```

There are two profiles that are optional at this time:
* rpm - builds all the RPMs for REST services
* wildfly - change RPM packaging from jbossas (default) to wildfly

## Build for Wildfly
Builds lightblue RPMs for deployment on wildfly.  The only difference is where the artifacts get deployed on the filesystem.  By default lightblue builds for deployment on JBoss EAP6.

```
mvn clean install -P wildfly -P rpm
```
