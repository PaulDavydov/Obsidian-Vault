- for the genre-topn query, we do a left join lateral
	- implements a nested loop over the genres and allows to fetch our top n. tracks per genre
- the correlated subq runs for each genre and is parameterized with the current genreid thanks to the clause where track.genreid = genre.genreid.
	- this is how the where clause implements the correlation between the outer loop and the inner loops
- once the inner loop is done, the lateral subq named ss we join again with the album and artist tables

## Chapter 8 - Indexing Strategy
- Indexing is thought of as a data modeling activity
- in postgres, some indexes are necessary to ensure data consistency
	- Constraints such as UNIQUE, PRIMARY KEY, or EXCLUDE USING are only possible to implement wiht a backing index
	- Backing index (applies mainly to. Elasticsearch) - a hidden, auto generated index within an Elastichsearch data stream that stores the actual data
- Multiversion concurrency control
- Whenever you declare a unique constrtain, primary key constraint, or an exclusion constraint PostrgreSQL creates an index for you
- Indexing strategy is important for db management
	- Every index we create, while optimizes for searching in the db, every commit we make to the table, will also update our indexes
	- writes take up additional costs to our resources and power
## Chapter 12
- SQL is structured query language, composed of several areas
	- DML (data manipulation language) - covers insert, update, and delete statements, which are used to input data into the system
	- DDL (data definition language) - covers create, alter, and drop statements which are used to define on-disk data structures where to hold the data, and also their constants and indexes. We usually refer to them as SQL objects
	- TCL (transaction control language) - includes begin and commit statements, and also rollback, start transaction and set transaction commands. Also includes the less well known savepoint, release savepoint, and rollback to savepoint commands. Another inclusion is commit protocols, prepare commit, commit prepared, and roll-back prepared commands
	- DCL (data control language) - is covered with the statements grant and revoke
	- PostgreSQL includes its own maintenance commands, such as vacuum, analyze, and cluster. Other commands provided by postgreSQL are prepare, execute, explain, listen, notify, lock and set.
## Chapter 13
- why not to use select * in queries
	- hides intention of the code, listing the columns explicitly helps declare our thinking as a developer
	- codes change are easier to review when column list is explicit
	- not efficient to retreive all the bytes each time even if you do not need them
## Chapter 15
- using the with clause we can create a common table expression
	- provides a way to write auxillary statements for using in larger queries
	- think of them as a defined temporary table that exists for just one query
- 