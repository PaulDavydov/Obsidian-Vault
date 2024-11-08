
## Chapter 1 Scale from Zero to Millions of users a
#### Single Server Setup
- Chapter focuses on redundancy and scaling from a single web server to a massive stateless cluster
- To start off with a single webserver:
	1. Users access websites thru domain names, such as `api.mysite.com`. The Domain Name System (DNS) is a paid service provided by 3rd parties and not hosted bvy our servers
	2. Internet Protocol (IP) address returned to the browser or mobile app
	3. Once the IP address is obtained, Hypertext Transfer Protocol (HTTP) requests are sent directly to the web server
	4. The web server returns the HTML pages or JSON response for rendering
- Traffic sources to our web server come from web applications or mobile applications
	- Web applications: uses a combination of server-side langauges (JAVA or PYTHON) to handle business logic, storage, etc. Client-side languages (HTML and JS) for presentation
	- Mobile applications: HTTP protocol is the communication protocol between the mobile app and the web server. JS Query Notation (JSON) is commonly used API response format to transfer data due to its simplicity
- As user base grows, need more servers. One for user traffic and the other for database operations
#### Database
- Need to decide on one of two databases for storage:
	- Relational DB: Relation Database Management System (RDBMS). MySQL, PostgreSQL, Oracle DB. Represent and store data in tables and rows. Can perform join operations using SQL across different database tables
	- Non-Relational DB: NoSQL databases. CouchDB, Neo4j, Cassandra, HBase, Amazon DynamoDN. The DB are grouped into four categories: key-value stores, graph stores, column stores, and document stores. Join operations are generally not supported in non-relational databases.
- Typically Relational DB should be used, they work well and there is plenty of fixes/documentation for it
- Use non-relational DB if:
	- Applications requires super-low latency
	- data is unstructured, or you do not have any relational data
	- only need to serialize and deserialize data (JSON, XML, YAML, etc)
	- Need to store a massive amount of data
#### Types of Scaling
- Vertical scaling: refers to as scale up.
	- this typically involves adding more resources to the servers
	- adding more CPU, RAM, etc
- Horizontal means to scale out
	- typically means adding more servers into the pool of resources
- With low traffic, vertical scaling is the simplest option
	- Has some down sides:
		1. Vertical scaling has a hard limit, impossible to add unlimited CPU and memory to a single server
		2. Vertical scaling does not have a failover or redundancy. When server goes down, the website/app goes down with it completely.
- Horizontal Scaling is more desirable for large scale applications
	- More servers can be added to meet demand of user flow
	- If one server goes down, another can take on the workload to not disrupt operations
#### Load Balancer
- Helps evenly distribute incoming traffic among web servers that are defined load-balanced set
- Instead of connecting directly to the web servers, the user grabs the public IP of he load balancer
	- Abstracts the web servers from the users
	- Private IPS are created for servers to communicate with each other, including the load balancer
		- Private IP only allow for communcation in the same network
- With the addition of a load balancer and a second web server, the failover issues is resolved
	- If server 1 goes offline, all traffic routed to server 2
	- This will also add another server to the mix if server 1 goes offline
- Load balancer also allows to gracefully add additional web servers to handle excessive traffic. It will start to automatically sending requests to them
#### Database replication
- Database replication can be used in many database management systems, usually with a master/slave relationship between the original (master) and the copies (slave)
- Master DB supports write operations, while the copies/slaves read the data from the master DB and provide read operations to the web servers
	- Master DB: insert, delete or update
	- Most systems require a higher ratio of reads to writes, so more slave DBs
- Advantages:
	1. Better performance: write and updates happen in master nodes, read operations happen in slave nodes. Improves performance since it allows to queries to be processed in parallel
	2. Reliability: If one of your DBs is destroyed, data is still perserved since the data is replicated across multiple locations
	3. High availability: since it is all replicated, your website remains in operation even if a database is offline since the data is stored in multiple database servers
- What happens if one of the databases goes offline:
	- If a slave database goes offline, read operations will be transferred to the master DB or another slave DB. A new slave DB server will be created to replace the old one
	- If the master DB goes offline, a slave DB is pormoted to be a master DB. Another slave db will be created to replace the promoted one. In prod systems, promoting a new master is more complicated as the data in a slave database might not be up to date. Missing data needs to be updated by running data recovery scripts.
- What does adding database replication look like in our system
	- A user gets the IP address of the load balancer from the DNS
	- user connects to the load balancer
	- HTTP request is routed to an available server
	- web server reads user data from a slave database
	- web server routes any data-modifying operations to the master db
#### Cache
- A cache is a temporary storage area that stores the result of expensive responses or frequently accessed data in memory so that subsequent requests are served more quickly.
- Database calls are costly and can heavily impact performance, cache helps to mitigate these problems
- A cache tier is a temporary data store layer, much faster than the database
- The way this works:
	- After receiving a request, web server checks the cache if it has the available data
	- If it does, cache sends back the data, otherwise it queries the database and stores the response in the cache
	- this caching strategy is called the read-through cache
- Considerations for using cache
	- use cache when data is read frequently but modified infrequently. Cache data is stored in volatile memory, not good for persisting data. If cache server restarts, all data in memory is lost.
	- Expiration policy. Good practice to implement an expiration policy. Once cached data is expired, the data is removed from the cache.
	- Consistency: involves keeping the data store and the cache in sync. Inconsistency can happen because data-modifying operations on the data store and cache are not in a single transaction
	- Mitigation failures: A single cache server represents a potential single point of failure. Multiple cache servers across different data centers are recommended
	- Eviction Policy: once the cache is full, any request to add items to the cache might cause existing items to be removed. This is called cache eviction. Least-recently-used (LRU) is the most popular eviction policy, others can be Least Frequently Used (LFU) or First in First Out( FIFO)
#### Content Delivery Network (CDN)
- CDN is a geographically dispersed servers used to deliver static content
	- CDN servers cache static content like images, videos, CSS, JS files, etc
- How CDNs work:
	1. User visits a site
	2. CDN server closest to user delivers static content
- Considerations of using a CDN
	- Cost: CDNs are run by third party providers, you are charged for data transfers
	- Setting an appropriate cache expiry, too long will cause content to not be fresh, if too short: it can cause repeat reloading of content from origin servers to the CDN
	- CDN Fallback: consider how your website/application copes with CDN failure. If there is a outage, clients should be able to detect the outage
	- Invalidating files: you can remove a file from the CDN before it expires by performing one of the following operations:
		- Invalidate the CDN object using APIs provided by CDN vendors
		- Use object versioning to serve a different version of the object.
	- adding CDNs has reduced the load on our web servers, they are fetched rom the CDN for better performacne
	- DB load is lightened by caching data
#### Stateless web tier
- Stateful server remembers client data (state) from one request to the next. Stateless keeps no state info.
- Issue with stateful, certain servers contain the session data for the User, if the request was routed to another server but it would add complexity
- Stateless allows for HTTP request from user to be sent to different servers.
- These fetch state data from a shared data store
- The data is no longer stored in the server, can now be taken out of the web tier and store it in a persistent data store.
	- This shared data store can be a relational DB: Redis, NoSQL, etc
#### Data Center
- For normal operations, most data centers are geoDNS-routed to the closest data cetner
- In case of any significant data center outage, we direct all traffic to a healthy data center
- Challenges:
	- Traffic redirection: tools are needed to direct traffic to the correct data center. GeoDNS can be used to direct traffic to the nearest data center depending on where a user is located.
	- Data synchronization: Users use a variety of different local DBs or caches. Can cause traffic to be routed to a data center where data is unavailable. To counter this, we can replicate data across multiple data centers
	- Test and deployment: It is important to test websites and apps at different locations. Automated deployment tools are vital to keep services consistent through all data centers

#### Message Queue
- Message queue is a durable component, stored in memory, that supports asynchronous communication.
	- Servers as a buffer and distributes asynchronous requests.
- Architecture of a message queue:
	- Input services, called producers/publishers
		- create messages
		- publish them to a message queue
	- other services or servers, called producers/publishers
		- connect to the queue
		- perform action's defined by the messages
![[Queue Example]]

- Message queue is an example of decoupling a application
	- It helps reduce the reliance on other services, they can run without waiting on data or instructions from other services
	- Produce can post a message to the queue even if the consumer is unavailable to process it
	- Consumer can read messages form the queue when the producer is unavailable
#### Logging, metrics, automation
- Logging: monitoring error logs is important because it helps to identify errors and problems in your system
	- Can either monitor logs at a server level, or aggregate them in a centralized service
- Metrics: collecting different tpyes of metrics helps us gain business insights and understand the health status of the system
	- Host level: CPU, Memory, disk I/O
	- Aggregated level metrics: ex: performance of the entire database tier, cache tier
	- Key business metrics: daily active users, retention, revenue
- Automation: utilize automation tools to improve productivity
	- An example would be continuous integration, automation does the verification, testing, and allows teams to detect problems eraly
#### Database scaling
- Vertical scaling, also known as scaling up, is the scaling by adding more power (CPU, RAM, DISK, etc) to an existing machine.
	- Example can be adding 24 TB of RAM to and Amazon RDS
	- Hardware limitations, at some point you cannot add more "power" to a server
	- greater risk of single point of failure
	- Overal cost of vertical scaling is high, powerful servers are more expensive
- Horizontal scaling, also known as sharding, practice of adding more servers
	- Process of separating larger databases into smaller, easier to manage parts, called shards.

## Chapter 2: Back-of-the-envelope estimation
- 