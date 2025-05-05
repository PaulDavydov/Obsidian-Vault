- Topic: a particular stream of data
	- logs, purchases, tweets
- Like a table in a database (without all the constraints)
- identify a topic by its name
- any kind of message format
- the sequence of the messages is called a data stream
- you cannot query topics, instead, use kafka producers to send data and kafka consumers to read the data
### parition and offsets
- topics are split in partitions (could split to 100)
	- Message within each partition are ordered
- Kafka topics are immutable, once data is written to a partition it cannot be changed
- Once the data is written to a partition, it cannot be changed
- Data is kept only for a limited time
	- default is 1 week, but is configurable
- offset only have a meaning for a specific partition
- offset are not resued even if previous messages have been deleted
- Order is guaranteed only within a partition (not across partitions)
- Data is assigned randomly to a partition unless a key is provided
- you can have as many partitions per topic as you want
- Partitions rebalance automatically in Kafka,
	- Happens whenever a consumer joins or leaves a group
	- Also happens when a partition is added into a topic
- Eager Rebalance
	- Default algo
	- When new consumer joins the group, all consumers stop, and give up their ownership/membership of the partitions
	- Consumers rejoin the group, and all partitions are assigned to the consumers
		- This will be random
	- Consumers don't necessarily "get back" the same partitions as they used to
- Cooperative Rebalance (Incremental Rebalance)
	- Reassigning a small subset of the partitions from one consumer to another
	- Other consumers that don't have reassigned partitions can still process uninterrupted
	- Can go through several iterations to find a "stable" assignment (hence "incremental")
	- Avoids "stop-the-world" events where all consumers stop processing data
	- Kafka Consumer, exists a property called `partition.assignment.strategy` 
		- RangeAssignor: assign partitions on a per-topic basis (can lead to imbalance)
		- RoundRobin: assign partitions across all topics in round-robin fashion, optimal balance
		- StickyAssignor: balanced like RoundRobin, then minimises partition movements when consumer join/leave the group in order to minimize movements
		- CooperativeStickyAssignor: rebalance strategy is identical to StickyAssignor but supports cooperative rebalances and therefore consumers can keep on consuming from the topic
		- The default assignor is RangeAssignor, CooperativeStickyAssignor which will use the RangeAssignor by default, but allows upgrading to the CooperativeStickyAssignor with just a single rolling bounce that removes the RangeAssignor from the list
### Producers
- producers write data to topics (which are made of partitions)
- producers know to which partition to write to (and which kafka broker has it)
- in case of kafka broker failures, producers will automatically recover
- Producer can choose to send a key with the message (can be string, number, binary, etc)
- if key = null, data is sent round robin (part 0, 1, 2, 3, etc)
- if Key != null, then all messages for that key will always go to the same partition (hashing)
- a key are tpyically sent if you need message ordering for a specific field
Typical kafka message anatomy

| Key - binary (can be null) | Value - binary (can be null) |
|                    Compression type (none, gzip, lz4)              |
|                 Headers (optionsal) |key - value|key - value|  |
|                   Partition + Offset                                              |
|                    Timestamp (system or user set)                    |

- Kafka message serializer
- Kafka only accepets bytes as inputs from producers and send bytes out as an input to consumers
- Message serialization means transforming objects/data into bytes
- they are used on the value and the key
- Common serializer
	- String (incl JSON)
	- Int, Float
	- Avro
	- Protobuf
- a kafka paritioner is a code logic, that determines what partition a record goes to
- Key hashing is the process of determining the mappipng of a key to a partition
- default kafka, murmur2 is the default algo

### Consumers
- Consumers read data from the topic (identified by name) - pull model
- Consumers automatically know which broker to read from
- in case of broker failure, consumers know how to recover
- data is read in order from low to high offset within each partition
- Deserialize indicates how to transform bytes into objects/data
- they are used on the value and the key of the message
- The serialization and deserialization type must not change during a topic lifestyle (create a new topic isntead)
### Consumer groups
- all the consumers in an application read data as a consumer group
- each consumer within a group reads from exclusive paritions
- If you have more consumers than partitions, some consumers will be inactive
- Multiple consumer groups on one topic
- kafka allows to have multiple consumers groups on the same topic
- to create distinct consumer groups, use the consumer group group.id
- Kafka stores the offsets at which a consumer gorup has been reading
- the offsets committed are in kafka topic name __consumer__offsets
- when a consumer in a group has processed data received fro kafka, it should be periodically committing the offsets
- if a consumer dies, it will be able to read back from where it left off thanks to the committed consumer offsets
- by default java consumers will auto commit offsets (at least once)
- there are 3 delivery semantics if you choose to commit manually
- at least once
	- offsets are committed after the message is  processed
	- if the processing goes wron the message will be read again
	- the can result in duplicate processing of messages, make sure your processing is idempotent
- at most once
	- offsets are committed as soon as messages are received
	- if the processing goes wron, some messages will be lost
- exactly once
- for kafka -> kafka workfloes: use the Transactional API
- for kafka -> external system workflows, use an idempotent consumer

### Kafka Brokers
- Kafka cluster is composed of multiple brokers (servers)
- each broker is identified with its ID (integer)
- each broker contains certain topic paritions
- after connecting to any broker (called a bootstrap broker), you will be connected to the entire cluster
- a good number to get started is 3 brokers, but some big clusters have over 100 brokers
- each kafka broker is called a bootstrap server
- means only need to connect to one broker, and the kafka clietns will know how to be connected to the entire cluster 
- each broker knows about all brokers, topics, and partitoins

### Topic replication factor
- topics should have a replication factor > 1 (usually between 2 and 3)
- this way if a broker is down, another broker can server the data
- at any time only ONE broker can be a leader for a given partition
- producers can only send data to the broker that is leader of a partition
	- The other brokers will replicate the data
	- therefore each parition has one leader and multiple ISR (in-sync replica)
- kafka producers can only write to the leader broker for a partition
- kafka consumers  by default will read from the leader broker for a partition
- Kafka Consumers Replica fetching, available at 2.4
	- configures consumers to read from the closest replica
	- this may help improve latency and also decrease netwrok costs if using the cloud
		- Same data center, less cost
### Producer Acknowledgement
- producers can choose to receive acknowledgement of data writes
- acks = 0: producer wont wait for acknowledgement (possible data loss)
- acks = 1: producer will wait for leader acknowledgement (limited data loss)
- acks = all : leader + replica acknowledgement (no data loss)
- For topic replication factor of 3, topic data durability can withstand 2 brokers loss
- as a rule, for a replication factor of N, you can permanently lose up to N-1 brokers and still recover your data

### Zookeeper
- Zookeeper manages brokers (keeps a list of them)
- Zookeeper helps in performing leader election for partitions
- Zookeeper sends notifications to kafka in case of changes (e.g. new topic, broker dies, broker comes up, delete topics, etc...)
- Kafka 2.x cant work without zookeeper
- kafka 3.x can work without zookeeper (KIP-500) - using kafka raft instead
- kafka 4.x will not have zookeeper
- zookeeper by design operates with an odd number of severs (1,3,5,7,...)
- zookeeper has a leader (writes) the rest of the servers are followers (reads)
- zookeeper does NOT store consumer offsets with Kafka > v0.10
- Zookeeper passes in the meta data to the kafka broker
- Should i use Zookeeper
	- Kafka brokers?
		- Yes until 4.x
	- With kafka clients?
		- over time kafka clients and cli have been migrated to leverage the brokers as a connection endpoint instead of zookeeper
		- Zookeeper is less secure than kafka, therefore zookeeper ports should be only open to accept traffic from kafka, not clients

### KRaft
- 2020 kafka started to work to remove zookeeper dependencies
- zookeeper shows scaling issues when kafka clusters have > 100000 partitions
- Removing zookeeper
	- scale to millions of partitions, easier to maintain and set up
	- improve stability, makes it easier to monitor, support, and administer
	- single security model for the whole system
	- single process to start with kafka
	- faster controller shutdown and recover time
- Kafka 3.x now implements the raft protocol to replace zookeeper

### Sticky Paritioner
- default, kafka sends messages to topics using a round robin algo.
	- No realy order, can be very random
- When sending messages in batch, uses sticky partitioner algo
	- Sends all the messages in the batch, to the same partition
	- or if all messages are sent real fast, for performance improvements, will send to the same partitioner
