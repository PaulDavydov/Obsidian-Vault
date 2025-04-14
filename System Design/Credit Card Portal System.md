Problem: Design a credit card account management application. The application should represent a bank with the opportunity to apply for credit cards, manage credit cards, manage payments, and report usages.

Features that need to be supported:

- Process a credit card application

- Create a credit card online account / access online portal

- Provide API to authorize credit card transactions

- Support calculating daily, weekly and monthly statistics

- Pay credit card balance using a third-party payment service



Core Requirements
1. Process Credit Card applications
	1. manage credit card online account
2. Provide a way to process payments
	1. Can be an external gateway, could be using the banks legacy system
	2. oay credit card baance using a third party service
3. Provide a API to authorize credit card transactions
4. Support collection of daily, weekly, and monthly statistics, Reporting service

Non-functional requirements
- Consistency in data
- Logs to restore service downtime
- Transacitons need to be durable and consistent
- Fault tolerant
- idempotent

Components:
1. client layer: Web or Mobile UI built in React or React Native
2. API Gateway: AWS API Gateway. Allow us to send rest commands to various services, user authentication and security, deal with rate limiting
3. MicroServices Layer (Business Logic)
   - Receive communications form API Gateway using REST while inter communications can be done using asynchronous messaging such as a message broker Kafka
   - Services:
	   - Accounts Service:
		   - Manages user accounts.
		   - Allows for credit card applications and creates accounts for that
		   - allows updating info on these cards and balance queries
		   - Unique identifier AccountID
	   - Transactions Service:
		   - handles all credit card transactions
		   - when needing attention from user, sends a status update to accounts and a notification mssage to notification service
	- Notifcation service:
		- Handles notificaionts via SMS, email, or push notifs. Updates user for successful transactions, low balances and other activities
	- Payment Service:
		- Integrate with external thrid part gateway, such as Stripe, for paying off credit card balances. Can use legacy system of the bank to process these payments
	- Reporting:
		- Process logs using kafka to then stream it into Apache Flink.
		- Why use flink
			- fine grained access to state
			- flexible data structures for state stroage and querying
			- Real time processing of data streams, allows for real time analytics, fraud detection and monitoring applicatioons
				- if we need to know what if there was some kind of fraudulent transactions, flink can help us process that in real time. Allow for stateful stream processing
			- Perfect for financial instituions
	- Messaging
		- Received REST calls from API gateway. Data brought in using json most likely or aggregated in some way
		- Any communication needed to be done between services is done through a message broker such as Kafka, can also set up a pub sub relationship between certain services such as Transactions and Payments
	- use Stripe, Visa, Mastercard as a our third party gateway
	- Caching can incorporate for certain frequently access data such as credit card balances, recent transactions
		- Reduces load on DB and drastically improves response time
	- Dockerization and kubernetes for mainting containers. Allows for quicker orechestration, auto scaling and easier deployment to our servers or cloud providers
	


