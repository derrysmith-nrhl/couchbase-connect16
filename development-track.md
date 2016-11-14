# Development Track

## Tue, Nov 8

### Bootiful Couchbase: microservices with Spring - Pivotal

Spring Bootiful

__Repository Query methods__
save
T findOne(ID id)
bool exists(ID id)
IEnumerable<T> findAll
delete(ID id)
deleteAll()

findAll(Sort sort)
findAll(Pageable pageable)

querying
N1QL - SQL like query language for JSON
secondary index supports global indexes across the cluster
multidimensional scaling - spearate index and query services

bucket.query(N1qlQuery.paramterized(

))

secondary index querying with N1qlQuery
List<User> fundByLastnameAndAgeBetween(string lastname, int minAge, int maxAge);

or

@query("#{#n1ql.selectEntity} WHERE lastname=\"#{#lastname}\" AND " +
"age BETWEEN #{#minage} AND )


__View Index Querying__
function (doc, meta) {
	emit([doc.zip, doc.inspectionScore], doc.id);
}

http://localhost:8092/restuarants/_design/dev_view/_view/view?startkey=["",80]

SDK
bucket.query(ViewQuery
	.from("views", "view")
	.startKey("[\"94110\",80]"))

__Spatial View Index Querying__
similar to views with just Map function
index on GeoJson objects and other JSON properties
open and closed...

SDK


__production rady microservice__
cross cutting concerns:
reliability
discoverability
observability (monitoring/health)
configurability
securability
auditability




### LINQing to data: Easing the transition from SQL

> centeredge software

local client/server sql app (integrate with cloud-based products)

persists shopping carts and cached sql query results

using couchbase server 4.5

*** query and index services (different servers)


data modeling
-------------
only need "type" property on root document

[DocumentTypeFilter()] - automatically applies a WHERE type = 'x' predicate to generated n1ql syntax
(type becomes readonly and ensures saved correctly)

need to join on document keys

UNNEST - similiar to inner join to nested document array


IsMissing - is attribute (property) missing
IsNotMissing
IsValue
IsNotValue - is null or is missing

UseKeys - doesn't hit index, just grabs documents by their keys

USE INDEX - gives index hint (normally should allow linq/cb to use what it thinks best)

Async - query.ExecuteAsync()
can take lambda to run execution method on return

When to index???
Indexing:
1. use predicate to only index documents of a certain type
create index `` on `` (`sourceairport`) where `type` = 'airport'
2. index the same attribute accros multiple document types by including type first
create index `def_type_id` on `bucket` (`type`, `id`) 
??3. a good practice is to create a fallback in case other indexes aren't used
create index `def_type` on `travel-sample` (`type`)

Date Times:
need to add STR_TO_MILLIS function on index if indexing on datetime properties


Change Tracking
---------------
creates proxy... overrides all virtual properties, marks as dirty if changed
need to add [Key] attribute,
properties need to be virtual
use IList instead of List

db.BeginChangeTracking()
	- turns on change tracking and creates proxies
db.SubmitChanges() or db.EndChangeTracking()


after SubmitChanges()

db.MutationState, pass into query, will ensure that query is up-to-datetime

Github:
couchbaselabs/Linq2Couchbase
brantburnett/Couchbase.Linq.Example





Migrating from Mongoose/MongoDB with Ottoman.JSON
ODM - object document mapper
	- Spring data
	- Mongoose
	- Mandango
	- Mongoid
	- Hibernate Object Grid

JSON - flexible data modeling
simplified integration with UI in web and mobile apps

a lot of boilerplate for interactive apps
- model the data, including realationships
- add validation logic
- create, build indexes to support access patterns

a way to add more schema to the model based on the language object primitives
- add references
- handle embedded documents

a method of storing and retrieiving objects that match the model
- save()

add another layer of validation to the model

Ottomanjs
- define a JavaScript Object Model to become your data model
var ottoman = require('ottoman');
ottoman.bucket = cluster.openBucket('default');

var Furniture = ottoman.model('Furniture', {
	name: 'string'
});
- frees you from boilerplate of finding data backing model
Furniture.findByName('table', function (err, tables) {
	if (err) return console.error(err);
	console.log(tables);
});
- support multiple indexing strategies and creation of indexes
var Furniture = ottoman.model('Furniture', { name: 'string' }, {
	index: {
		findByName: {
			by: 'name',
			type: 'n1ql' // 'refdoc' or 'view'
		}
	}
});
- has all JSON datatypes and makes references and default values declarative
var Manufacturer = otttoman.model('Manufacturer', {
	name: 'string',
	city: 'string',
	incorporatedDate: { type: 'Date', default: Date.now }
});

var Furniture = ottoman.model('Furniture', {
	name: 'string',
	manufacturer: { ref: 'Manufacturer' }
});
- allows for complete customization and method declaration on your object model
Furniture.prototype.dance = function() {
	console.log('I do not dance');
};





### Migrating from relational: data modeling and access - centeredge

Database => bucket
Table => bucket

data types: number, string, bool are what's supported in couchbase
Talend - connector for Couchbase
tCouchbaseInput
tCouchbaseOutput


DDL
- only create/drop index exists in n1ql
- all others should be removed
- temp tables

views
- view indexes
- functional indexes

statement blocks
- triggers/procedures handled by app, not cb

sequences
- use UUID()
- special documents with counter and accessed atomically
- specify delta

joins
- lookup
- index
- use document keys

transactions
- no need
- doc modification is atomically
- consistency specified at REST level or SDK
	- scan_consistency=[not_bounded|at_plus|request_plus|statement_plus]

monitoring
- system:completed_requests
- system:active_requests
- EXPLAIN &lt;statement&gt;


modeling
_entities_: main objects that are targets of the apps operates on

root attributes v. embedded attributes
- root has type as first attribute
- embedded as "type" attribute in document


for dates, using epoch data type is easiest to index and sort by

JSON-Schema (use for validating documents if need be)


keys - 250 chars
try using real world examples
- phone number, username, ssn
surrogate keys
- SHA-1
- UUID()
- sequential numbers (implemented using Counter feature)
real world examples are best so you don't need to look up id, and can execute UseKeys


trade offs
- document size
- atomicity
- complexity
- speed

embed v. references
- embed when want retrieve with one Get
- refer when document is too large


only use one service for all updates, this is where schema enforcement happens
schema changes supported using custom json converters during deserialization

watch out for too large documents
serialization/deserialization performance penalties
document contention as too many actions attempt to modify the document simultaneously

watch out for data spread across too many related documents

include document keys up front, so you can use n1ql to JOIN or NEST operations


store document key inside document?
- already in meta
- querying is easier since don't need META() call


view indexes good for pre-aggregating numbers, reports, graphs, etc...
GSI best for generic queries, collecting a set of documents
views don't scale as cleanly (live on data nodes)



## Wed, Nov 9


### Utilizing arrays

Functions:
- ARRAY_CONCAT
- ARRAY_COUNT
- ARRAY_INSERT
- ARRAY_REVERSE
- ARRAY_MIN
- ARRAY_AVG




### Going beyond SELECT, top 10 ways to use n1ql

DML support
- MERGE
- PREPARE/EXECUTE
- INFER: discover schema of documents
- EXPLAIN

INSERT
- insert values directly
- insert with select statement 
- returning clause returns values of attributes

DELETE
- with where clause

UPDATE
- USE KYES clause
- SET clause (change value of attribute)
- UNSET clause (remove attribute from document)
- UPDATE-FOR (iterates over a nested array)

MERGE
- update, insert, or delete in one bucked based on match with data in another
- source bucket and target bucket, needs a join condition on common attribute

PREPARE - EXECUTE
- avoids the statement being prepared repeatedly (so query is cached)

INFER
- discover schema of documents in bucket
- based on sampling
- pass sample size to randomly sample that number of documents in the keyspace or bucket
	- default value is 1000
- pass num sample values to specify sample values to return
	- default value is 5
- similarity_metric is degree of similarity between two document schemas (percentage match)
	- default value is 0.6
- dictionary_threshold

EXPLAIN
- shows query plan




### API
services
- data (binary, :11210)
- views json, :8092
- n1ql json, :8093
- full json, text search :8094
- analytics json, :8095

rfc
semi-formal process to advance sdks in api and behavior
one shpherd
github.com/couchbaselabs/sdk-rfcs


prioritizing uniformity
- program flow is the same regardless of language
	- get cluster
	- get bucket

prioritizing language specifics


key/value
- fastest way to access & mutate documents
- access by doc id
- subdoc manipulation
- efficient binary protocol

views
- distributed map/reduce
- good for aggregations
- spatial support

n1ql
- flexible, but need to get index right
- global index instead of map/reduce
- similar to sql

full text search
- flexible, more concepts to learn
- inverted index
- still in developer preview

analytics
- analytical storage engine
- similar to n1ql for querying
- performs great on huge datasets and aggregations
- developer preview

streaming/changes feed
- based on DCP protocol
- very fast and efficient
- low level
	- look at kafka connection (message queuing)

tricks
- transparently cache expensive n1ql/fts/view results with a ttl
- "tarzan"
	- go from doc-to-doc via references and use bulk fetches for higher throughput




### Containerizing Couchbase with Swarm & Kubernetes

> qa/testing, using docker to build test framework

orchestration
- deploy apps with multiple containers in pods

Swarm
- containers are encapsulated in services
