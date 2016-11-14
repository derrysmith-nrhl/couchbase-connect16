# Notes

## Questions
- Best way to design for versioning/history
- Best way to determine deltas from "same" documents
- Id generation (guid, integer?)
- Partitioning buckets by type
	- need to persist a "type" field
	- couchbase has extension methods that can add the type predicate to queries so dev does not have to include in every query
	- should we partition buckets by object type or by functionality?
		- since we have to add predicate anyway and since documents can evolve over time, is it necessary to have a bucket per CLR type?
- Scan Consistency
	- add/update document, indexes get updated
	- when running queries, by default 
		- NotBounded (fastest), query will not wait for documents to be indexed (might miss some documents)
		- RequestPlus, waits for index to finish (slower)
		- AtPlus, fast + accuracy, but more work (code) (only waits on specific documents)
- Check and Set (CAS)
	- new Document<T> { Id = "", Content = {}, Cas = ... }
	- dealing w/ concurrency (compares Cas values)
	- if Cas is omitted, insert/update is "last document wins"
- Error Handling
	- Result objects include Exception, Success, Message
