### Apache Flink
- unified batch and stream processing system
	- Share common abstractions
- Flink allows for "stateful" operators
	- operators may be chained together by intermediate streams
	- state is updated by each input event "excatly once"
### Stream Processing
- why not process chunks of data on some fixed interval?
	- The majority of the time, data is produced at arbitrary moments
- We want to cut down the feedback loop of returning data.
- Recall: Lambda architecture -> use streams for fast approximaions and batches for slow exact computations
	- FLINK CAN DO BOTH
### Streams in Flink
- in flink, a stream is just a (potentially unbounded) source of records
	- Batch jobs are represented as stream processing tasks on a finite input stream
- Flink jobs can be represented as a DAG of operators and streams

![[Pasted image 20250408221151.png]]
### Types of streams
- flink supports different types of streams when sedning data between operators
- Pipelined -> e.g. two consectutive map functions on an incoming record
	- Leads to backpressure between consecutive operators
	- Optional configurable buffer pools to mitigate backpressure
		- size threshold -> when reached buffer is sent to next operator -> larger = more throughput
		- time threshold
			- when reached buffer is sent to next operator
			- smaller = lower latency
- blocking (for bounded streams)
	- whole thing materialized, possibly to disk
### Flink Event Ordering
- Operators are split into many paritions!
	- Each operator parition produces events into its own separate stream parition
	- if an operator reads from just one stream parition, it will receive those events in order
- Generally speaking it is best practice to write operators that do not rely on event order
### Flink Checkpointing
- To recover from failures, flink employs checkpointing!
	- On an interval, take a "consistent" snapshot of all operator states and the offset of all incoming streams for each
	- In the even of a failure, revert each operator bvack to its state and begin reading each input stream form its associated offset
- How can we ensure that no even affect operator state twice?
- How do we avoid coordination (e.g. distributed locking/stalling computation) when taking checkpoints? 
### Snapshotting
- flink implements "asynchronous barrier snapshotting" for consistent snapshots
	- the job maanager occasionally puts in barriers to each top level input stream
	- the checkpoints are consistent across "logical time"
		- The result of completely handling every event before the barrier in each input queue
- Because we need to be able to replay stream events, input streams must be durable (e.g. kafka) or the producing operator must have a write ahead log
### Barrier Alignment
- what if an operator has many input queues?
	- It must perform alignment
![[Pasted image 20250408223033.png]]

### Iterative workflows
- many types of jobs may run some sort of looping behavior
	- graph processing, machine learning
- Iterative part of plan mdoeled by a "feedback stream"
	- integrates with checkpointing
	- end of iterations marked by special "control event" records
### Time in Flink
- event time 
	- external record publish time
- Ingestion time
	- time record enters flink
- Processing time
	- time that record is handled by operator
	- distributed timestamps are unreliable, hard to synchronize across many paritions of the same operator

### Time watermakring
- idea: if an operator reports a low watermark of time Tm we have completely processed all events with time < T
	- Inject watermark events into each input stream on some interval
### Stream Windowing
- windows in flink are responsbile for placing events into buckets
	- Assigner: map events to bucket
	- Trigger: starts some bucket computation based on certain conditions
	- Evictor: responsible for removing events form buckets
- Flink has many prebuilt windowing implementations
- "Global" windows span many paritions and require coordination
### Batch processing
- Flink allows running batch jobs via its DataSet API
	- Still converted to a DAG with "bounded" streams
- Under the hood, batch jobs run more similarly to Spark
	- No checkpointing for "non pipeline breaking operators" (narrow dependencies)
	- Just replay the failed computations
- Query optimization via paritioning/sorting information
	- hard to do optimization because Flink allows arbitrary functions
### Conclusion
- flink allows to write user defined functions to process streaming records from persistent message brokers, and build up state over time
- It employs asynchronous barrier snapshotting to ensure that every event in a stream impacts operator state exactly once. In the event of a operator failure, we restore them all to their last checkpointed state and reply subsequent events. Checkpointing does not block computation