- What do we want from databases:
	1. Persistently store data
	2. Store on a hard drive
	3. in a database
- The closer you have data together on disk, the faster it is to access

| Name   | hand size |
| ------ | --------- |
| Jordan | 18        |
| donald | 7         |
| shaq   | 24        |
Find rows where name = Jordan
- typically db goes row by row finding the correct data
	- This is O(n) time complexity (reading and writing)
- O(n) is not good enough for db read speeds
- Range query: Show rows with names between a-b -> return all rows that have this condition
- Database Index -> read speeds go up, write speeds go down for a specific key
- 