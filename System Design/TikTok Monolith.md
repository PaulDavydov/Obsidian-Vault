- Realtime recommendation model server
	- Many systems up to this point operate in batch
	- relaxed fault tolerance guarantees
- Improved system to handl
	- limited space in memory for holding embeddings
	- "Content drift" user preferences change frequently
### Recommendation Model Background
- Goal Recommend video that each user is interested in!~
- Machine learning problem: Represent a users interest/videos as "embeddings" 
	- Embeddings are vectors, each index represents a "feature"
	- a feature is just a property of the user/video (e.g. language code)
- Users sohuld be interested in video where the vectors are similar!
	- We are training a model that takes in user input and outputs user and video embeddings that accurately reflect user interests
	- Have a neural network making predictions based on user and set of candidate vectors at inference time
### Objectives of Monolith Desiegn
- Efficiently store embedding tables in memory
- Allow frequent updates of embeddings
	- user interests change frequently
	- tiktok sees improvement in model quality updating these as frequently as possible, use 1 min in practice
### hash table redesign
- Monolith employs Cuckoo Hashing, to make the most of its memory
	- O(1) inserts, O(1) lookups
	- avoids IDs being mapped tro the same bucket
	- could lead to lwoer model quality
- Only include frequently appearing IDS
	- Missing thesse doesnt decrease model quality (not enough data on them)
	- can use probabalistic data structures (count min sketch) to lower memory usage
- Expire old embeddings
	- Inactive users/old videos
![[Pasted image 20250402004441.png]]

### Additional Notes
- Flink does lots of caching, use disk if we are out of storage
- Negative events are also logged in kafka(eg user did not comment)
	- Many more of these, so we downsample them
	- This makes positive actions seem more likely in training
		- Reduce the smapling rate or spatial resolution of aa singal or image typically to decrease storage space or bandwidth requirements
	- account for this by applying a correcting factor at inference time
- We need our parameter servers to be (somewhat) fault tolerant
	- accomplish this with checkpointing
	- infrequent checkpointing leads to worse models, too frequently increases system load
### Checkpointing
- dense parameters are synchornized once per day
	- do not change as quickly as sparse parameters, more represented by training data
	- synchronized to inference parameter servers once per day
- Sparse parameters are synchronized every minute!
	- Only send for the IDs that have been modified
	- Model quality quickly deegrades if stale
- We checkpoint all parameters once per day at low load
	- if 1000 parameter servers, one fails ever 10 days
	- no significant decrease in model quality
### Conclusion
- using a streams based approach to recommendations systems allows us to incorporate changing user preferences in near real time!
	- Relaxing fault tolerance allows us to simplify design
- "Collisionless" hashing enables us to store more embeddings per parameter server