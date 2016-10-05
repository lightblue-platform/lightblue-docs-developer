# Key Decisions
Over the design and development of lightblue some key design decisions have been made.  This chapter collects those into one place for postarity with the intent of providing context for why some things were done the way there were.

They are not in any particular order.

## How will we represent date in JSON?
APIs accept dates of the [format](http://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html):  YYYYMMDDTHH:mm:ss.SSS+-ZZZZ
(Note T is a literal string)

All dates will be stored internally as UTC, with no TZ in the representation.
All dates will be returned in UTC, with TZ appended.

## How will we support BigDecimal and BigInteger?
If a field is defined as biginteger, or bigdecimal in metadata, store the value as string, and don't allow < > operators during search.

If a field is defined as bigdecimal(x,y) or biginteger(x) in metadata, store the value with x digits, and y decimals, padded with 0s, and support < > during search.

## How will events for create, update, and delete be sent to the Enterprise Service Bus?
Lets abstract out the mechanics of auditing. Instead of providing a flag for each field to say if it is audited or not, lets do something like this:

For each field, define a hook. For instance, user.firstname would have audit-hook and publish-hook associated with it. We can define a set of hook implementations that take the old and new versions of the document, and the action performed on it. The audit-hook would be of a hook type that dumps the old version of the data into an audit collection. publish-hook might be something that dumps the new version of the data into a separate collection, or call a REST API with the document, or publish the doc to a JMS topic, etc.

## Can we use gson to do json / java conversions?
Short answer: no.

More details:
* Performance of Gson is bad compared to other options.
* Handling of generics is not good, so if model contains generics use something else

Recommend only using Gson for quick and simple hacks and only in testing.  Recommend not using in releasable code.

## Are we going to use puppet to configure services?
After some fiddling in [Ansible](http://www.ansible.com/home) and considering other options it has been decided we will continue using puppet.  Our goal is to include puppet configurations as part of lightblue, but that it will not be required for configuration.

## How will we do aggregation of lightblue data and other data sources?
This question was asked before we got down the path of multiple controller implementations.  The answer is aggregation functionality that will be added in [technical deliverable 3](../proposed_roadmap/README.html#td3-aggregation) and the existence of other controller implementations.  [Technical deliverable 5](../proposed_roadmap/README.html#td5-rdbms-support) is to implement a generic RDBMS controller.

## How will metadata be structured?
*    Metadata is split into two sections:  entity info and schema.
*    Entity info is all the non-version specific info like entity name, indexes, hooks, and datastore information.
*    Schema is all the version specific info.
*    Entity info may contain a single default version.

## How will metadata be versioned?
* Versions are tracked at the entity level only (entity schema).
* Versions are strings following the [versioning standard in the user guide](http://docs.lightblue.io/standards/versioning.html).
* Each version of an entity's metadata is active, deprecated, or disabled.
* An active or deprecated version of the requested entity must be specified by every client request.
* Each entity may keep a default version.
* Default version is not managed automatically and can be intentionally excluded.
* Attempts to deprecate or disable the default version of an entity will fail.

## Do clients have to supply a version?
With respect to the data service:
* Every client's request may specify the version of an entity they want to work with.
* If a client requests a version of an entity that is disabled the request fails.
* If a client does not specify a version and the entity has a default version defined, the request will be processed with that default version.
* If a client does not specify a version and the entity does not have a default version defined, the request will fail.

## How are we going to tune indexes for queries?
Metadata will contain a section for non-versioned information (entity info).  This section can be updated independently of the versioned data (schema).  Therefore, indexes will be managed via updates to metadata entity info and not via direct database updates.  It can be updated by anybody with sufficient authorization.  When an index is created or updated on metadata via the metadata service the service will also apply the index in the correct data collection.
