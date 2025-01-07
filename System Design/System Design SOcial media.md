
For a follower/following db we should be using Cassandra,, leaderless replications, so any replica can be hit 
use userId as the partition key, helps with sorting

- Posts DB
	- Use cassandra
	- use the timestamp as the sort key
	- cassandra allows for writes to be fast and all posts will go there first
- For popular users,
	- We know in advance that these people will be the most popular
	- we can cache data for these users
	- we use flink as our caching layer.
		- handles real time data streams with efficiency
		- Redis provides data storage and retrieval
		- we want the data streaming more tahtn we awant the storage
		- flink will take the popular post from a popular user, and put the posts into the popular posts caches
			- shard cahces by user Id
			- 


