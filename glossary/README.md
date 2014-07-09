# Glossary

## General
* simple entity - a single persistence unit.. "data that can be written atomically"
* aggregate entity - a collection of simple entities associated through some relationships
* data service - a software service for manipulating a simple entities
* orchestration service - synonymous with "aggregation service"
* aggregation service - a software service for manipulating aggregate entities
* business service - a software service for doing anything outside of data or aggregation service scope.  Typically anything that has business logic requirements.

## Technical
* document - structured text representing data for an entity.  Unless specified, assume this is referring to a [JSON](http://en.wikipedia.org/wiki/JSON) document.
* hook - operations that provide a way for CRUD operations to have side effects after a document is created, modified, or deleted. Similar to triggers in a relational database
* projection - projection refers to either a document that defines what fields to keep when returning a result set or the result set from such an operation (applying a projection)

## Migration
* movable data - data that can be moved from existing datastores to another location
* unmovable data - data that can not be moved from existing datastores
