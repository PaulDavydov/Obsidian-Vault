## Design a URL shortener
### Problem requirements
1. Generate unqiue shortURL per longURL/paste --> How can we ensure these are unique?
2. Per generated URL allow the original poster to see the number of clicks --> how can we ensure these are accurate
### Performance considerations
1. Median URL has 10k clicks, most popular URL in millions of clicks
2. At a time may have to support up to 1 trillion short URLS
3. For certain pastes, text sizes can be in the gigabytes, average size in kilobytes. 1 trillion X 1kB = 1pB
### Link Generator
- Idea: we probably want to has (longURL, userID, create Timestamp) -> h("pornhub.com", 69, 01/06/2021) = `tinyurl.com/32fz1ca8`
- Number of characters? -> possibilities = 36^n. if we use 0-9,a-z characters
	- If n=8 we have around 2 trillion combinations
- hash collisions? -> if 32fz1ca8 is taken try 32fz1ca9 and so on
### Assigning URLs - Replication
- we want to try to maximize our write throughput where possible!
	- Can we use multi-leader/leaderless repications? NO!
	![[Drawing 2024-12-09 20.09.08.excalidraw]]
	- LAST WRITE WINS can be a strategy. except it might introduce a shortURL that is NOT the right shortURL for a client
	- Telling people a few minutes later is too late!!
	- Rules out dynamo databases: Cassandra, Scylla, Riak, etc...
		- We want Single leader replication
- Sometimes we can speed up our writes via the use of a write back cache!
	- Issue is we will run into the same issue as before
	- Partitioning is important if we have a lot of data, it can help us speed up our reads and writes by reducing load on every node!
	- Can partition by range of shortURL, they're already hashes so should be relatively even
		- Allows probing to another has to stay local to one DB
		- Keep track of consistent hashing ring on cooridnation service, minimize reshuffling on cluster size change

| tinyURL  | actualURL      | userId | createTime | expireTime | clicks |
| -------- | -------------- | ------ | ---------- | ---------- | ------ |
| ac12upb6 | "onlyfans.com" | 22     | 11/13/28   | 11/20/28   | 500    |
| d4k5a721 | "google.com"   | 23     | 11/14/28   | 11/27/28   | 1000   |
|          |                |        |            |            |        |

### Predicate Locks
- Predicate Query: 
	- `SELECT * FROM URLS where SHORTURL = "dk45a721"`
	- If two users are trying to create a shortURL at the same time and it is being added to our database, the first user to add the row, will be given an OK from the server, while the one that was submitted later would be given an error and told to try again
	- Index on shortURL makes predicate query much faster
	- using a store procedure can reduce network calls in event of has collision
### Materializing Conflicts
- Can prepopulate every possible row in our database so they exist to lock on!
- 2 trillion * 1 byte/char * 8 char = 16TB, not very much
### Engine implementation
- we do not need range queries so a hash index would be super fast
- but we are storing 1pb of data so probable too expensive for in memory DB
- LSM Tree + SSTable
	- worse for reads by better for writes, we assume that more people will be reading
	- Stores data in memory, occasionally flushing the data to disk
- B-Tree 
	- Better for reads, worse for writes, probably better for us
	- Self balancing tree data structure. maintains sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time
### Database choice
- What are our database demands:
	1. Single Leader replication
	2. Paritioned
	3. B tree based
- Seems easy to just use a relations DB, we are not really making any distributed joins anways, MySQL
	- Could make the argument for MongoDB
![[Drawing 2024-12-09 20.39.41.excalidraw]]

### Maximizing Read Speeds
- replicas + multiple partitions to ensure adequate ability to handle load
	- possible to get stale reads from replica, could check leader on null result?
	- need to be careful with this, could accidentally spam leader
### Hot links
- Some links are "HOT", get much more traffic than others
	- A caching layer can help mitigate a lot of the load!
		- Can be scaled independently of DB!
		- Partitioning the cache by shortURL should lead to fewer cache misses
### Populating the Cache
- Note: we can not "push" links to the cache in advance, we do not know what will be popular! so how should we populate it?
	- write back, can have write conflicts, bad idea
	- write through, slows down write speeds
	- write around, causes initial cache miss. best option for us
- LRU eviction (Least Recenlty Used)
### Analytics - Naive solution
- we keep a clicks counter per row, could we just increment it?
- We would have to grab a lock/use atomic increment operation per row
	- for super popular links, this is too slow!
### Analytics - Stream Processing
- Idea: we can place individual data somewhere that doesn't require grabbing locks, and then aggregate them later!
- Options:
	- database -> relatively slow
	- in memory message broker -> super fast, not durable
	- log based message broker -> basically writing to write ahead log, durable
- We can dump our click info to kafka!
### Analytics - Click Consumer
- options:
	- HDFS + Spark
		- batch job to aggregate clicks, may be too infrequent
	- Flink
		- processes each event individually. may send too many writes to the database depending on implementation
	- Spark Streaming
		- processes events in configurable mini-batch size
- Stream consumer frameworks enable us to ensure exactly once processing of events via checkpoint/queue offsets!
### Analytics - Exactly Once?
- Events are only processed exactly once internally
- Options:
	- two phase commit (super slow)
	- idenpotency key, scales poorly if many publishes for same row
### Analytics - One publisher per row
- By partitioning our Kafka queues and Spark Streaming consumers by shortURL, we can ensure that only one consumer is publishing clicks for a shortId at a time
- Benefits:
	- Fewer idenpotence keys to store
	- no needs to grab locks on publish steps
### Deleting Expired Links
- Can run a relatively inexpensive batch job every x hours to check for expired links
	- Only has to grab lock of row currently being read
	- `if(currentTime > row.expiredTime) row.delete()/row.clear()`
### Pastebin - HUGE Pastes
- For pastes that are multiple GB, we cannot store them in our DB
	- Could store in an object store or HDFS
	- Object store likely preferable, cheaper, no batch jobs to run, dont need data locality
	- CDNs will greatly improve latency, if the massive files are infrequent enough a writethrough model could make sense
- Writing in series allows us to avoid 2 phase commit
Client - > CDN -> S3 - > DB

![[Pasted image 20241209212001.png]]
use apache zookeeper as a centralized service for maintaining configuration information, naming, providing distributed synchro, and providing gorup services
