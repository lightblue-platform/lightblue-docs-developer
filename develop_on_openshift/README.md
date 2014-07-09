# Develop on OpenShift

This guide expects that you have an [OpenShift](https://www.openshift.com/) account and have [installed rhc](https://www.openshift.com/developers/rhc-client-tools-install).

This document assumes you use "lightblue" as your appliation name and that you want to create it in your default domain.

## Create Application
You can create an application via the OpenShift web app or CLI and add the MongoDB cart.  Given that versions of components change over time we'll leave this up to the user.  If you have difficulties please reach out to us for help!

Requirements:
* any size gear will work
* application type: JBoss EAP6
* add-on cart: MongoDB

## Initial Deployment

Check out your gear locally with the ```rhc``` command.
```
rhc git-clone -a lightblue
```

Setup environment variable to configure lightblue to talk to the mongo instance:
```
eval `rhc ssh lightblue env | egrep -e ^OPENSHIFT_MONGODB_DB_HOST -e ^OPENSHIFT_MONGODB_DB_PORT -e ^OPENSHIFT_MONGODB_DB_USERNAME -e ^OPENSHIFT_MONGODB_DB_PASSWORD | sed 's/^/export /g'`
rhc env-set MAVEN_ARGS="clean package -U -Popenshift -DskipTests -Dmongodb.host=${OPENSHIFT_MONGODB_DB_HOST} -Dmongodb.port=${OPENSHIFT_MONGODB_DB_PORT} -Dmongodb.user=${OPENSHIFT_MONGODB_DB_USERNAME} -Dmongodb.pass=${OPENSHIFT_MONGODB_DB_PASSWORD}" --app lightblue
unset OPENSHIFT_MONGODB_DB_HOST
unset OPENSHIFT_MONGODB_DB_PORT
unset OPENSHIFT_MONGODB_DB_USERNAME
unset OPENSHIFT_MONGODB_DB_PASSWORD
rhc env-show MAVEN_ARGS --app lightblue
```

And finally push the application:
```
git remote add github https://github.com/lightblue-platform/openshift-lightblue-all.git
git fetch github
git reset --hard github/master
git push origin master -f
```

## Deploy Your Changes
...
