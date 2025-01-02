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



## Reddit/Twitter/Facebook/Insta
### Objectives:
1. Users can quickly see who they are following and who follows them
2. We can quickly load all posts for a given user
3. Low latency news feed from posts that a user follows
4. Posts can have configurable privacy types, as do follows
5. user can comment on posts, and comments can be infinitely nested
### Capacity Estimates
1. 100 characters a post, around 100 bytes for text, maybe around 100 for other metadata
2. 1 billion pots per day -> 1 billion 200 bytes * 365 days/year = 73 TB/year
3. On average users have 100 followers some "verified" users have millions
4. Comments also have 100 characters, so around 200 bytes with metadata
5. Most comments on one post = 1 million, 1million x 200 bytes = 200 MB
### Fetching Followers/following
- getFollowers(userId)/getFollowing(userId) -> we want the queries to return quickly
	- if we chose one, the other will become super slow
- instead of having one table, have two, one for followers and one for following
- There will be a lot of writes to theses tables, so at least the user-following table needs to be able to ingest them quicklky! 
	- CASSANDRA BEST DB
		- No SQL DB, can write to any replica, LSM tree fast for ingestion
	- Do not need to worry about write conflicts, every write is valid!
- Partitioning
	- userId -> partition key, allows us to ensure no cross partition query to get following
	- follower/followingId -> sort key, quick scanning if we ever need to delete a row
### News Feed (Naive)
- client
	- hit user following table
		- hit post db sharded by userId
			- aggregate all data on one server
- SLOW
### New Feed (Optimal)
- how do we avoid reading from multiple different places?
	- Would somehow need to index tweets by what new feed they belong to
	- But a tweet can be in around 100 different news feeds
	- 1 billion tweets/day x 200 bytes/tweet x 100 copies = 20tb of tweets per day
	- 20 TB/ 256gb beefy hosts as caches = 80 in memory caches
	- this is doable
### Posts DB
-  DB writes need to be fast since all posts go there first!
- Utilizing some database with lots of partitioning + leaderless replication + LSM tree could help us here! (Cassandra AGAIN)
TYPICAL SCHEME

| userId (partitioning key) | timestamp (sort key) | twee body   |
| ------------------------- | -------------------- | ----------- |
| 69                        | 01/06/2020           | ........... |
- Partitioning/sorting this way also ensures that loading all tweets for a given user is fast!
### Popular Users
- Some users will have millions of followers, our prev step wont work
	- Too many following to stream to flink
	- too many caches to update per post
	- can we use a hybrid approach?
### Caching popular posts
- we can introduce a caching layer for posts of popular users to speed up fetching them!
- Since we know in advance that these posts will be popular,  we can use a "push" based approach to preload the data!
### Security levels on Posts
- lets say a user can specify whether a post is for "all" followers or "close friend" followers.
	- Put the info in the followers table

USER-FOLLOWERS table:


| User | follower | security level |
| ---- | -------- | -------------- |
| 1    | 2        | all            |
| 1    | 3        | close friend   |
- changes to post security/follower security level will flow through our posting pipeline  and eventually flow to the news feed caches
	- UNFORTUNATELY expensive and asynchronous
### Nested Comments
- we want to be able to optimize our setup to quickly read nested commments
- Shard by postId
-  Multileader may not here, let's use MySQL
- BFS or DFS up to you
### Graph DB
- Allows us to have a generalized query approach for traversing comments
- Could use a native graph DB like Neo4J
	- Users pointers on disk
### Alternatives to Graph DB
- graph DBs are still going to have somewhat high latency, jumpinbg around on disk is very slow so we shoudl try to avoid it when we can!
- can we do something smart with traditional DBs to make a better index?
- Build DFS index

![[Pasted image 20241222202538.png]]


### B Trees
- B trees are a type of Database index
	- Kept entirely on disk
- Every Node of the B tree has a set size
- Split nodes as we traverse to help keep the tree balanced
- have a write ahead log to keep everything up to date on your db
- Summary:
	- Self balancing tree on disk
	- No limits to dataset size
	- support for range queries


## Robinhood
- Problem requirement:
	- Users should be able to buy and sell financial products via an exchange and see their existing positions
	- Users should be able to see the realtime values of their assests on one screen, as well as total portfolio value
	- We want to minimize the number of servers that we have listening to the exchange (this is expensive)
	- Estimating around 100 million users, 100 different instruments in each portfolio
- Initial thoughts:
	- most robinhood clients are connecting via mobile device, so we want to limit the number of active connections to reduce how much load we put on them
		- Lets try to keep it to one active connection per client!
		- If a client is connected from multiple devices, theyll want the same data on each device, should all connect to the same server
		- use consistent hashing on userId to determine server to connect to
	- We cant have these user servers directly listen to the exchange for prices
		- we pay extra for this, likely too expensive
### Placing Orders
- Sending our request to a gateway, and it will get handled for us.
	- When placing an order, need to put into pending since it will take awhile to fulfill
		- Need to be able to cancel order
- For cancelling orders, we have a race condition
	- Need confirmation from the exchange for cancellation, or else the user can click cancel, we can tell them it was cancelled, and the order could be filled in the mean time.

![[Pasted image 20250101223908.png]]

### Exchange Layer
- We want to minimize the number of subs to exchange
	- They publish data over multicast channels,, typically using some sharding over ticker
	- we do not want one excahnge facing server per user interested in an  asset
	- ideally just one or two "publishing" servers per asset (can be run in active-active or active-pssive configuration, can both listen to zookeeper)
		- AA: publish updates twice but no downtime
		- AP: only publish but slight down time on failure
### Pricing Layer
- Decide a price for each asset based on incoming data form multiple exchange layer servers for a given ticker
- Deliver prices to the user layer
- Shard it on the has of the tickers!
user server 1 & user server 2 ----> pricing server <---------     exchange server A or B <------- excahnge A or B

### Pricing Layer Load
- Our pricing server can get overloaded by popular assets
- By exchange publishers if there are a lot of trades/open orders
	- in the last trade pricing case, our publishers could throttle how often they publish and only send some of the messages (or an averaged price)
	- in the weighted average case, each publisher could build up state of bids and asks and occasionally send snapshot to pricing cache to aggregate
- By user servers if many users want pricing, too many active connections
### Pricing Forwarding Servers
- for popular assets, forward data from pricing servers, to intermediary cache servesr
user server ------> cache 1 <--------- pricing gme server
- can use round robin load balancing to decide which cache connect to !

![[Pasted image 20250101230300.png]]



