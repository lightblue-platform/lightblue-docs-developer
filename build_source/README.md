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
