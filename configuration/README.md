# Configuration
Configuration files are expected to be in the classpath. For EAP6
implementations, this means they are installed as an EAP6 module, and
jboss-deployment-structure.xml is included to refer to that module.

There are four configuration units:
 * **Datasources**: This configuration defines the datasources accessible
   to Lightblue. It is managed by
   com.redhat.lightblue.config.common.DataSourcesConfiguration
   class. The datasources are initialized first, and used by both
   metadata and CRUD configurations. It is stored in "datasources.json".
 * **Metadata**: The structure of this configuration is determined by the
   actual metadata implementation. It is initialized and managed by
   com.redhat.lightblue.config.metadata.MetadataManager class. It is
   stored in "lightblue-metadata.json".
 * **CRUD**: This configuration lists all the CRUD implementations known
   to Lightblue. It provides the datastore type that referenced from
   entity metadata, and the factory class to create instances of
   the CRUD implementation. The list of all datasources are passed
   to the factory. It is stored in "lightblue-crud.json".
 * c**onfig.properties**: This is the hystrix configuration.

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
	"mongo": {
		"type": "com.redhat.lightblue.mongo.config.MongoConfiguration",
		"metadataDataStoreParser": "com.redhat.lightblue.metadata.mongo.MongoDataStoreParser",
		"ssl": false,
		"noCertValidation": false,
		"database": "mongo",
		"credentials": {
			"mechanism": "MONGODB_CR_MECHANISM",
			"userName": "lightblue",
			"password": "lbpassword",
			"source": "admin"
		},
		"servers": [
    		{
    			"host": "localhost",
    			"port": "27017"
    		}
		],
		"driverOptions": {
		    "writeConcern": "majority",
		    "readPreference": "primaryPreferred",
		    "maxQueryTimeMS": 75000,
		    "maxResultSetSize": 15000
		}
	}
}
```

  * `type`: Java class that represents the configuration
  * `metadataDataStoreParser`: If this datasource will be used to store
    metadata, this should be initialized to the Java class
    implementing the metadata DataStoreParser interface.
  * `ssl`: Whether to use ssl for database communications
  * `noCertValidation`: If true, ssl certificate validation will be disabled. Do not
    use this in production environments.
  * `database`: The database in MongoDB to connect to
  * `credentials`: This can be an array of credentials, or a single credential object
     * `mechanism`: Possible values are GSSAPI_MECHANISM, MONGODB_CR_MECHANISM, MONGODB_X509_MECHANISM, PLAIN_MECHANSM
     * `userName`: The user name
     * `password`: The password
     * `source`: The name of the database where the user info is defined
  * `server`: The host and port of the server to connect to
  * `servers`: Array of host and port of the servers to connect to
  * `driverOptions`: Options for the mongo driver.
    * `writeConcern`: Default write concern used for insert, update, save, and delete.  Can override with execution option per request.
    * `readPreferences`: Default read preference used for find. Can override with execution option per request.
    * `maxQueryTimeMS`: Default maximum time (in milliseconds) a find runs on the mongo server.  Can override with execution option per request.
    * `maxResultSetSize`: maximum number of documents to return per request

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
    "collection": "metadata",
    "cachePeekIntervalMsec": 10000,
    "cacheTTLMsec": 60000
}
```

  * dataSource : Name of the datasource in datasources configuration
    where metadata is stored.
  * collection: MongoDB collection used to store metadata
  * cachePeekIntervalMsec: The interval with which the metadata cache will
    peek into the database to see if anything has changed. The cache will
    remain valid if no changes were detected in the metadata database.
    Defaults to 10000 (10 seconds).
  * cacheTTLMsec: The interval with which the metadata cache will refresh
    unconditionally. Defaults to  600000 (10 minutes)

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
        "backend" : <datasourceName>,
        "controllerFactory" : <Class name>,
        "extensions": { 
            "extensionType" : { configuration},
            ...
        },
        "options": {
            <backend specific options>
        }
      },
      ...
   ]
}
```
  * validateRequests: If true, requests are validated using their JSON schema. Defaults to false.

  * backend : Name of the backend for which this
    controller is to be used. This is the backend used in
    metadata DataStore interface.
  * controllerFactory : Name of the class implementing
    com.redhat.lightblue.config.crud.ControllerFactory interface.
    This is used to create instances of CRUD controllers.

### MongoDB CRUD

```
{
   "controllers" : [
      {
        "backend" : "mongo",
        "controllerFactory" : "com.redhat.lightblue.mongo.config.MongoCRUDFactory"
      }
   ]
}
```

  * backend : Name of the datastore type for which this
    controller is to be used, "mongo" in this example.
  * controllerFactory: the factory implementation for Mongo

#### MongoDB Options

Currently MongoDB CRUD backend supports these options:

   * updateBatchSize (integer): Default value is 64. This controls the batch size of insert/update operations.
   * concurrentModification: Configuration options for concurrent modification detection:
     * detection (boolean): Default value is 'true'. When this is set, non-atomic upsert/update
     operations will use optimistic locking to detect if the documents the current thread is operating on have been
     modified by another thread during the update operation.
     * failureRetryCount (int): Default value is 3. When a concurrent modification is detected, the operation will be
     retried this many times before giving up with an error.
     * reevaluateQueryForRetry (boolean): Default value is 'true'. When true, if a concurrent modification is
     detected on a document, the document is re-validated to see if it still matches the query. If it does,
     update is retried, otherwise the document is left untouched. If set to 'false', updates on documents that are modified
     by another thread will be retried unconditionally.
     

#### MongoDB extensions

##### Locking

This extension, when configured, enables locking APIs using mongoDB backend. The configuration is as follows:

```
"extensions": {
   "locking": [
       {
         "domain": "domain1",
         "datasource": "source1",
         "collection": "lockCollection"
       },
       {
         "domain": "domain2",
         "datasource": "source1",
         "collection" : "lockCollection2"
       },
       ...
    ]
 }
```

Each element in "locking" array configures a locking domain. The
locking domain is the "domain" argument used in the locking api, which
logically separates locks used for different purposes. "datasource"
gives the mongoDB datasource defined in datasources.json, and
"collection" gives the collection in "datasource" that keeps the
locking information.

         
