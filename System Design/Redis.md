- Redis is a versatile tech, caching, logs, replace ment for Kafka
- Redis is a single threaded, in memory, DSA server
	- Single threaded simplifies things, no multicore worries, first thing that comes into Redis is the first thing that runs
	- In memory, basically in your RAM. Lightning fast and run in sub milli sec time for gets, sets and other function calls
	- Data structure server - redis values do not need to be streams, numbers, strings, blobs, they can be sorted sets, geospatial data.
		- Redis allows you to use them in a distributed fashion allowing for very fluid integration
- Core of redis is a dictionary, keys to values
	- Commands allow us to mess with the data
- Keys matter since this is how redis handles multi nodes in its server
- Slot, hash of a key, single master or main will own that slot
- every node of the cluster communicates via a gossip protocol
- Most common usage of Redis is to use it as a cahce
	- For example, if you have Database that does a large number of queries, to speed things up, we can shard out the Redis cache and store some of the data within that cache. So if the client/service needs to pull in some data, they can quickly grab from the cache to speed things up
- One of the most important things to know about redis is the hot key issue and expiration policy.
	- For key we can just append the data to the key, allowing for our data to be stored without issue
	- retention policy, set a expiration time, something like 60 sec
		- We can also say we want to rid a Least Recently Used item in the cache or a different type of algo
- Another use for Redis is to use it as a Rate Limiter
	- Need to keep a expensive service free from execessive requests
	- Use redis atomic increment and we can increment a keys value. Set it to a limit such as 5 and have a timer set that will expire after ? time
	- After the timer has ran out, we can expire the key and allow access to the expensive service again
- Redis offers stream, similar to Kafka
	- Ordered lists of items, each item given an id that contain their own data/values
	- we create the stream and then create a consumer group that points into the stream. The consumer will track where we are in the stream. the workers for the group will take in avaialble items owned by the consumer group
	- worker group will usually heartbeat to the consumer group, letting it know that it might be still working on the item
- Another usage for redis is a leaderboard
	- Sorted Sets, implented using skip lists and hastables
	- Adding a value is OLogN while searching is OLog M + N
- Redis has a great geospatial index
- Redis Pub Sub
	- Server needs to address one another in a reasonable fashion
	- Servers can connect to redis and announce a publication they are making, on that topic other servers can subscribe to pull that data
	- 