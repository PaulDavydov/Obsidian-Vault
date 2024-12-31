### Review: Indexes
- A database index is used for the purpose of speeding up reads conditioned on the value of a specific key! Be careful to not overuse indexes, as they slow down db writes:
	- LSM trees + SSTables
	- B-Trees
### LSM TREES and SS Tables
- LSM Tree writes first to a balanced binary search tree in memory
- Tree flushed to a sorted table on disk, when it gets too big
	- SS Table is effectively a sorted list of keys and their values
	- When wanting to read a key we first look for it on the mem table, LSM tree
		- if not there we got to the SS table in order
- Can binary search SStables for the value of a key
- If there are too many SSTables they can be merged together (old values of keys will be discarded)
- PROS: Fast writes to memory!
- CONS: may have to search many SSTabels for value of key
### B Trees
- a binary search tree using pointers on disk
- writes iterate through the binary tree, and either overwrite the existing key value or create a new page on disk and modify the parent pointer to the new page
- PROS: faster reads, know exactly where key is located!
- CONS: Slow writes to disk instead of memory
### Replication
- is the process of having multiple copies of data in order to make sure that if a database goes down, the data is not lost
- Types:
	- Single leader replication: all writes go to on database, reads come from any database
		- ensures that there are no data conflicts, all writes go to one node
	- Multi leader replication: writes can go to a small subset of leader databases, reads can come from any database
	- Leaderless replication: writes go to all databases, reads come from all databases
		- Applies to multi leader as well, useful for increasing write throughput beyond just one db node.
### SQL DB
- Relational/Normalized data- changes to one table may require changes to others
	- adding an author and their books to different tables on different nodes
	- may require a two phase commit
- Have transactional (ACID guarantees)
	- excessively slow fi you do not need them (due to two phase locking)
- Typically use B-trees
	- better for reads than writes in theory!
- Conclusion: Use SQL when correctness is of more importance than speed
	- banking apps, job scheduling
### Mongo DB
- Document data model (NoSQL)
	- Data is written in large nested documents, better data locality, but denormalized
- B-Trees and Transactions supported
- Conclusion: Rarely makes sense to use in a systems design interview since nothing is "special" about it, but good if you want SQL like guarantees on data with more flexibility via the document model
### Apache Cassandra
- Wide column data store (NoSQL), has a shard key and a sort key
	- Similar to excel spread sheet
	- Allows for flexible schemas, ease of partitioning
- Multileader/leaderless replication (configurable)
	- super fast writes, albiet uses write wins for conflict resolution
	- may clobber existing writes if they were not the winner of LWW (LAST WRITE WINS)
- Index based off of LSM tree and SSTables
	- Fast writes, slower reads
- Conclusion: Great for applications with high write volume, consistency is not as important, all writes and reads go to the same shard (no transactions)
	- See chat application for a good example of when to use
### Riak
- key value store (NoSQL), has a shard key and a sort key
	- Allows for flexible schemas, ease of partitioning
- Multileader/leaderless replication (configurable)
	- Super fast writes, supports CRDTs (conflict free replicated data types)
	- Allows for implementing things like counters and sets in a conflict free way, custom code to handle conflicts
- Index based off LSM tree and SSTables
	- fast writes
- Conclusion: Great for apps with high write volume, consistency is not as important, all writes and reads go to the same shard (no transactions)
	- See chat app for a good example of when to use
### Apache HBase
- Wide Column data store (NoSQL), has a shard key and a sort key
	- allows for flexible schemas, ease of partitioning
- Single lead replication
	- Built on hadoop, ensures data consistency and durability
	- Slower than leaderless replication
- index based off of LSM tree and SSTables
	- Fast writes
- Column oriented storage
	- Column compression and increased data locality within columns of data
- Conclusion: Great for applications that need fast column reads
	- Multiple thumbnails of a youtube video, sensor readings
### Memcached and Redis
- Key value stores implemented in memory (redis is a bit more feature rich)
	- uses a hashmap under the hood
- Conclusion: useful for data that needs to be written and retrieved extremely quickly or used frequently. memory is expensive so good for small datasets
	- Good for caches, certain essential app features (see geo spatial index for lyft)
Neo4j
- graph database!
	- as opposed to just using a SQL database under the hood with relations to represent nodes and edges, actually has pointers from one address on disk to another for quicker lookups
	- the former is bad because reads become slower proportional to the size of the index (O log n to binary search), but using direct pointers is O(1)
- Conclusion: only useful for data naturally represented in graph formats
	- map data, friends on social media
### TimescaleDb/Apache Druid
- Time series DB
	- Use LSM trees for fast ingestion, but break table into many small indexes by both ingestion source and timestamp
	- Allows for placing the whole index in CPU cache for better performance, quick deletes of whole index when no longer releveant (as opposed to typical tombstone method)
- Very niche but serve their purpose very well
	- Great for sensor data, metrics, logs, where you want to read by the ingestor and range of timestamp
New SQL
- voltdb
	- sql but completely in memory, single threaded execution for no locking
	- expensive and only allows for small datasets
- spanner
	- SQL, uses GPS clocks in data center to avoid locking by using timestamps to determine order of writes
	- very expensive
- Datawarehouses
	- SQL format, usually after going through large batches of data, cleaned and formatted