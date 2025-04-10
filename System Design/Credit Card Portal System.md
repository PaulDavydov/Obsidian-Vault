Problem: Design a high-level system architecture for a credit card portal offering application, account management, and payment processing functionalities

User interface/application:
- could be web or mobile
- provide seamless user experience
- Tech:
	- React.js for our web front end, React Native for our mobile suite
	- backend can be built in Spring boot

API gateway: 
- Single entry point for all API requests, securing and controlling access
- Tech:
	- I would use AWS API Gateway

Authentication Service
- Handles user registration, login, and access control using mechanisms like OAuth
- Tech:
	- familiar with Google Firebase and its Authentication process
	- AWS Amplify.
		- Allow us to integrate with DynamoDB, which is a key-value schema.
			- Can hold user data, product data, transaction data, payment data, etc
		- Can use AWS Cognito, a service that helps you manage user authentication, authorization, and accesss for your web and mobile apps. Allows for third party registrations
		- Can use S3, persistent objects storage service. Can hold images, website assets, and user assets
		- Pinpoint, for tracking and analytics for the app
		- Lambda, serverless, event-driven compute service. We can use it to handle all our API communications with third-party vendors, such as payments gateways and courier gateways

Card Management Service
- Manages credit card applications, account information, balances, and transaction history
- Tech:
	- Probably build out a Microservices approach here, using something like Spring Boot

Payment Processing Service
- Integrates with external payment gateways to securely process transactions
- Tech:
	- Built this service using Spring Boot and a Payment gateway, such as Stripe, Paypal, Visa, etc etc

Reporting Services
- Aggregates and analyzes transaction data for generating daily, weekly, and monthly reports
- Tech
	- OpenSearch dashboards. Pull in logs created by our application, and aggregate the data within the logs to be displayed on OpenSearch

Database
- Stores user data, account information, transactions, and other relevant data in a secure and scalable way
- Tech
	- PostgreSQL, a relational DB. Use DynamoDB to store user data such as login info

Data Flow:
1. user interacts with UI/APP.
2. user is routed through the API Gateway to the Authentication Service
3. Authentication Service, authenticates the customer, and grants access.
4. API Gateway sends customer to the Card Management Service
5. Card Management will handle all applications, account mangement, and transaction queries
6. Payment service will do the interactions with the external payment API for secure transactions
7. Reporting service will pull logs created by the actions of the User and create reports based on the logs
