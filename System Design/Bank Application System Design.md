1. Client Layer
	- The web clients and mobile clients
	- Can be built using React, React native, and backend via the HTTPS frameworks
2. API gateway
	- Serves as a unified entry point for client requests
	- Handles
		- Routing: directing requests to the appropriate microservices
		- Rate Limiting: protecting against denial of service DOS attacks
		- Authentication and Authorziation: Validating user tokens (using OAuth2 or JWT) before forwarding requests to the backend
		- Request aggregation: combining data from multiple services into one response for complex requests
3. Microservices Layer (Business Logic):
	- Designed to handle specific business functions, and each service is independent. Communciate via Rest APIs or asynchronous message, KAFKA (Probably easier with Kafka)
	- Services:
		- Account Service:
			- Manages User accounts (savings, checkings, etc)
			- Handles account creation, updates, and balance queries
			- Each account has its own unique identifier (AccountID)
		- Transaction Service:
			- Handles all financial transactions, such as transfers, deposits, withdrawals, and payments
			- Supports real time processing and ensures idempotency to avoid duplicate transactions
		- User Service:
			- Manages customer profiles and handles user authentication (sign up and login), and authorization
			- Integrates with an Identity Provider (eg, Oath, LDAP) for sercure user authentication
		- Loan Service:
			- Manges loan applications, interest calculations, and repayments plans
		- Payment service:
			- Integrates with thrid party gateways for online payments, bill payments, and card processing
		- Notification service
			- handles notification via SMS, email, or push notifs for events such as successful transactions, low balances, and account activity
4. Database Layer
	- Each microservice typically has its own database to ensure loose coupling
		- Relational Database:
			- For account, transaction, and user services, we may want to use something like PostgreSQL. Enforces ACID compliant transactions complex queries. Assume more reading over writing involved
			- NoSQL databases: For logs and notifications, can use Cassandra or MongoDB. Prefer OpenSearch for logs/reporting
	- Data consistency and transactions
		- Use the Saga pattern to manage distributed transactions across multiple services, ensuring consistency without relying on a single database
5. Security Layer
	- Data encryption: both data at rest (in databases) and in transit (between clients and services) are encrypted (eg aes for at rest, TLS for transit)
	- Authentication: using something like multi-factor or OAuth2
	- Role Based Access Control: only authorized users can perform specific actions
	- Monitoring and auditing: extensive logging of user activity and system events for auditing and fraud detection
6. External Integretagions
	- Third party: Payment gateways, suchs as Visa or Stripe. external financies servcies
	- Core Banking Systems: a core banking system might handle critical financial functions
		- settlements, credit checks, etc
		- API integretaion with these systems might be necessary
7. Communication Layer
	- Synchronous (REST) for real-time, blocking calls like checking balances or initiating payments
	- Asychronous message broker (Kafka or RabbitMQ): event-driven processes like notifications or updating reports, message queues
8. Caching Layer: Use Redis or Memcached for frequently access data, such as user balances, exchange rates, and maybe most recent transactions
	- reduces load on DB and improves response time
9. Monitoring and Logging
	- Prometheus for monitoring, cloudwatch.
	- Elk stack for for logging and visualization. ensures that system health is tracked and any anomalies are detected quickly
10. Load balancer
	- NGINX or AWS Elastic Load Balancing ensures incoming traffic is distributed evenly across multiple instances of microservices to prevent overload an ensure availability
11. Scalability and High Availability
	- Horizontal scaling: each microservice can be scaled independently based on load, transaction service can have more instances during peak usage
	- Database replication: databases are replicated across multiple data center for high availability and disaster recovery
	- Containerization: docker containers for microservices and kubernetes for orchestrtation for auto scaling and easier deployment

DISTRIBUTED Transactions: EITHER WE ALL PASS OR WE FAIL, single unit of work
ACID:
- Atomic: All operations in the transaction are treated as a single unit
Two phase commit:
- Prepare phase, where a coordinator node tells the service nodes to prepare for a transaction, locking down both nodes
- Commit/Rollback: sends commit message to permanently apply the changes. If both arent ready, a rollback messgae is sent back to rollback the changes
- additional communcations and coordination stewps can introduce latency and lower performance
- Services blocked depend on each other and the coordinator
- Complextiy since network communication, error handling, and other services are needed for it
Sagas, local transactions, each updating a single service
ex:
- Flight reservation -> local transation1
- payment processing -> local transaction2, waits for local transaction1 to finis
- hotel booking -> local transaction3....
- car rental
- notification
How do sagas work?
- orchestrated - central orchestrator, easoer debugging since has a clear audit log for it. Highly coupled and has a single point of failure, the saga orchestrator
	- Waits for a response for every service before continue to the next local transaction
		- If succeeded, the service sends back a confirmation message and elts the saga orchestrator move on to the next service
		- If it fails, it retries the transaction an X amount of times before sending back a error message. Orchestrator will then send out a compensating transaction to undo the actions done on previous messages/transactions
- In choreographed saga, no central node. Participants react to events done by other services. loosely couopled. Better scalable. Complex implementation since each rely on each other. higher debugging costs
	- One service will wait and react when the previous service has been completed
	- Asynchronous communication, and error handling


![[Pasted image 20250413000827.png]]
