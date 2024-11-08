https://wiki.c2.com/?CouplingAndCohesion

- Two lines of code, A and B, are **==coupled==** when B must change behavior only because A changed
- Two lines of code, A and B , are **==cohesive==** when a change to A allows B to change so that both add new value
- The main difference between ==coupling== and ==cohesion== is a distinction on a process of change, no static analysis of code's quality.
	- what does *must change behavior* mean
		- it means the explicit modification of behavior, i.e the source code
		- automatically adapting run-time results in a value-added way more closely matches the concept of **cohesion**
		- **coupling** is more readily identified by the way things break
			- B must change its behavior as cause of A changing is that the change to A broke the behavior of B
- **Cohesion** a single module/component is the degree to which its responsibilities form a meaningful unit; higher cohesion is better
- **Coupling** between modules/components is their degree of mutual interdependence; lowering coupling is better
	- size: number of connections between routines
	- intimacy: the directness of the connection between routines
	- visibility: the prominence of the connection between routines
	- flexibility: the ease of changing the connections between routines
- First order principle of software architecture is to increase cohesion and reduce coupling

| Cohesion (Worst to Best) | Description                                                                                                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Coincidental Cohesion    | Module elements are unrelated                                                                                                                                                                              |
| Logical Cohesion         | Elements perform similar activities as selected from outside module, i.e. by a flag that selects operation to perform (body of function is one huge if-else/switch on operation flag)                      |
| Temporal Cohesion        | operations related only by general time performed (initialization() or FatalErrorShutdown() )                                                                                                              |
| Procedural Cohesion      | Elements involved in different but sequential activities, each on different data (usually cold be trivially split into multiple nodules along linear sequence boundaries)                                  |
| Communicational Cohesion | unrelated operations except need same data or input                                                                                                                                                        |
| Sequential Cohesion      | operations on same data in significant order; output from one function is input to next (pipeline)                                                                                                         |
| Informational Cohesion   | a module performs a number of actions, each with its own entry point, with independent code for each action, all performed on the same data structure. Essentially implementation of an abstract data type |
| Functional Cohesion      | all elements contribute to a single, well-defined task, i.e. a function that performs exactly one operation                                                                                                |
- Coupling (interdependence between modules) level names (high coupling is bad)

| Coupling (Worst to Best)      | Description                                                                                                                          |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Content/Pathological Coupling | when a modules uses/alters data in another                                                                                           |
| Control Coupling              | 2 modules communication with a control flag (first tells second what to do via flag)                                                 |
| Common/Global-data Coupling   | 2 modules communicating via global data                                                                                              |
| Stamp/Data-structure Coupling | Communicating via a data structure ==passed== as a parameter. The data structure holds ==more== information than the recipient needs |
| Data Coupling                 | Communicating via parameter passing. The parameters passed are only those that the recipient needs.                                  |
| No data coupling              | independent modules                                                                                                                  |
