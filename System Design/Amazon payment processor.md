### Payment Platform
Amazon
- Do you want to pay?
- Items, sellers, confirm, card

### Problem Requirements
1. Build a payment sysstem for an E-commerce site
	1. Accounting  of payments and profts for buyers and sellers
2. We need our data to be perfect, cannot lose any
	1. Do not allow users to double submit payments
3. Actual handling of payments can be done by an external service
	1. Stripe for payments to us, Tipalti for payments from us to sellers

### Design Considerations
1. Latency is not important to us,  it is okay for a user's payment to take a few seconds if it is correct
2. Not all payments are approved instantly, it will take some time for certain payments to be accepted
3. As long as we have one "source of truth" table, we can derive all other data sources/actions from it
	1. How do we make a "source of truth" table?

### Strong consistency in Databases
- option 1: Synchronous Replication
	- No fault tolerance if either node goes down
- option 2: Use consensus algorithm (like Raft) on Replication Log
	- Can tolerate failure of one node
### Idempotent Payments
- Two face commit, user submits in payment, goes into our database and into stripes database
- Stripe allows us to provide them with an "idempotency key"
	- If the same request is submitted twice they know to ignore it
- Generate an idempotency key
	- Pre-materialize all keys, each request gest the next available one
	- Client generated has or random key, if taken probe for next available
- Lets index our table based on key so that we quickly find a row corresponding to one key!
### Increasing Throughput
- Right now, every single payment needs to go through our very slow table!
	- how can we reduce load/make this faster? PARTITIONING
- Nodes can be partitioned by the hash ranger of the key
	- if using pre-materialization, LB can round robin incoming requests for keys

![[Pasted image 20250407212134.png]]

### Fun with failures
- as mentioned Stripe does not process all payments instantly
	- You can create an APIon your server that stripe hits on completion (webhook)
- Failure scenarios:
	- we create a "pending" payments in our db that never gets back to stripe
	- we never hear back from stripe because the server with the webhook died
### Polling
- Idea: On an infrequent interval, we can check on all  pending payments in stripe
	- Only do this for pending payments where some time has passed
- If stripe returns:
	- Not recognized -> delete from payments taable
	- Completed/failed -> add status to payments table
	- in progress -> do nothing and keep waiting
- How do we quickly figure out which payments these are?
### Pending Payments Cache
- Idea: Maintain a separate cache pending payments
	- can store in memory
	- Dont have to interfere with writes to payments table (because of locking)

![[Pasted image 20250407213122.png]]
### Derived Data
- Aside from our payments/"ledger" table, we want data by other aaggregations
	- Revenue per seller
	- Orders per user
	- LETS USE CDC (Change Data Capture)

![[Pasted image 20250407213557.png]]
