# Configuration
Configuration files are expected to be in the classpath. For EAP6
implementations, this means they are installed as an EAP6 module, and
jboss-deployment-structure.xml is included to refer to that module.

There are four configuration units:
 * Datasources: This configuration defines the datasources accessible
   to Lightblue. It is managed by
   com.redhat.lightblue.config.common.DataSourcesConfiguration
   class. The datasources are initialized first, and used by both
   metadata and CRUD configurations. It is stored in "datasources.json".
 * Metadata: The structure of this configuration is determined by the
   actual metadata implementation. It is initialized and managed by
   com.redhat.lightblue.config.metadata.MetadataManager class. It is
   stored in "lightblue-metadata.json".
 * CRUD: This configuration lists all the CRUD implementations known
   to Lightblue. It provides the datastore type that referenced from
   entity metadata, and the factory class to create instances of
   the CRUD implementation. The list of all datasources are passed
   to the factory. It is stored in "lightblue-crud.json".
 * config.properties: This is the hystrix configuration.

## Datasources

Datasources configuration is maintained by
com.redhat.lightblue.config.common.DataSourcesConfiguration
class. When initialized, it reads "datasources.json" from the
classpath and initializes all datasources. The configuration
file has the following format:

```
{
   <datasourceName> : {
    "type" : <Class name>,
    <datasource definition>
  },
  <other datasources>
}
```

Every datasource contains a 'type' field giving the class name for the
Java class implementing
com.redhat.lightblue.config.common.DataSourceConfiguration
interface. This class must have a default constructor. Once a
datasource is read from the configuration, the datasource class is
instantiated, and the JSON object corresponding to the datasource is
passed to the datasource instance to initialize itself:

```
  Class clazz = Class.forName(type);
  DataSourceConfiguration ds = (DataSourceConfiguration) clazz.newInstance();
  ds.initializeFromJson(dsNode);
```

### MongoDB Datasources

A MongoDB datasource definition looks like this:

```
{
   "mongo" : {
     "type" : "com.redhat.lightblue.mongo.config.MongoConfiguration",
     "metadataDataStoreParser" : "com.redhat.lightblue.metadata.mongo.MongoDataStoreParser",
     "ssl" : false,
     "noCertValidation" : false,
     "database" : "mongo",
     "credentials" : {
       "mechanism": "MONGODB_CR_MECHANISM",
       "userName" : "lightblue",
       "password": "lbpassword",
       "source" : "admin"
     },
     "server" :
        {
           "host" : "localhost",
           "port" : "27017"
        }

   }
}
```

  * type : Java class that represents the configuration
  * metadataDataStoreParser : If this datasource will be used to store
    metadata, this should be initialized to the Java class
    implementing the metadata DataStoreParser interface.
  * ssl : Whether to use ssl for database communications
  * noCertValidation: If true, ssl certificate validation will be disabled. Do not
    use this in production environments.
  * database : The database in MongoDB to connect to
  * credentials : This can be an array of credentials, or a single credential object
     * mechanism: Possible values are GSSAPI_MECHANISM, MONGODB_CR_MECHANISM, MONGODB_X509_MECHANISM, PLAIN_MECHANSM
     * userName : The user name
     * password: The password
     * source: The name of the database where the user info is defined
  * server : The host and port of the server to connect to
  * servers : Array of host and port of the servers to connect to

MongoDB makes a distinction between initializing with a "server" and
with a "servers" list. If "server" is given, the host running the
Lightblue application must have access to that database host, even
though there are other replicas. If "servers" list is given (and even
if it contains only one server), the host running the Lightblue
application must have access to all the replicas, even if they are not
listed in the "servers" list.

## Metadata

Metadata configuration is managed by the
com.redhat.lightblue.config.metadata.MetadataManager class. It reads
"lightblue-metadata.json" file from the classpath. The configuration
file has the following format:

```
{
  "validateRequests" : true or false,
  "type" : <configuration class name>,
  "hookConfigurationParsers": [<hook configuration parser class names>],
  <configuration>
}
```

The "validateRequests" field determines whether calls receiving JSON
documents will validate the submitted JSON documents with respect to
their schema. These are metadata, schema, and entity info
documents. By default, requests are not validated.

The "type" field gives the Java class name implementing
`com.redhat.lightblue.config.metadata.MetadataConfiguration`. Once the
metadata configuration is read, an instance of this class is created,
and the configuration JSON object is given to it to initialize itself.

The "hookConfigurationParsers" is an optional list of Java class names implementing `com.redhat.lightblue.metadata.parser.HookConfigurationParser`.  One example implementing class is `com.redhat.lightblue.hook.audit.AuditHookConfigurationParser` in project [lightblue-audit-hook](https://github.com/lightblue-platform/lightblue-audit-hook).

### MongoDB Metadata

For MongoDB metadata implementation, configuration looks like this:

```
{
    "type" : "com.redhat.lightblue.mongo.config.MongoMetadataConfiguration",
    "dataSource" : "mongo",
    "collection": "metadata"
}
```

  * dataSource : Name of the datasource in datasources configuration
    where metadata is stored.
  * collection: MongoDB collection used to store metadata

### Example Metadata Configuration
Example with MongoDB metadata config and audit hook parser:

```
{
    "type" : "com.redhat.lightblue.mongo.config.MongoMetadataConfiguration",
    "hookConfigurationParsers": [
        "com.redhat.lightblue.hook.audit.AuditHookConfigurationParser"
    ],
    "dataSource" : "metadata",
    "collection": "metadata"
}
```

## CRUD

CRUD configuration is managed by the
com.redhat.lightblue.config.crud.CrudManager class. It reads the
"lightblue-crud.json" file from classpath. The file has the following
format:

```
{
   "validateRequests" : true or false,
   "controllers" : [
      {
        "datastoreType" : <datasourceName>,
        "controllerFactory" : <Class name>
      },
      ...
   ]
}
```
  * validateRequests: If true, requests are validated using their JSON schema. Defaults to false.

  * datastoreType : Name of the datastore type for which this
    controller is to be used. This is the data store type used in
    metadata DataStore interface.
  * controllerFactory : Name of the class implementing
    com.redhat.lightblue.config.crud.ControllerFactory interface.
    This is used to create instances of CRUD controllers.

### MongoDB CRUD

```
{
   "controllers" : [
      {
        "datastoreType" : "mongo",
        "controllerFactory" : "com.redhat.lightblue.mongo.config.MongoCRUDFactory"
      }
   ]
}
```

  * datastoreType : Name of the datastore type for which this
    controller is to be used, "mongo" in this example.
  * controllerFactory: the factory implementation for Mongo
