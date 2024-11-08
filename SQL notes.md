I will be studying SQL for the forseeable future due to having a subpar understanding of it. I will try and reaffirm all my knowledge on it in hopes that future interviews will go better than my previous ones.

- **SELECT** is a way to write a query. You are defining what data you want to grab. It is known as a query usually
	- When querying, a column would be a common property shared by all instances of that entity.
	- SELECT MY_COLUMN, MY_COLUMN2, ... FROM MyTABLE;
- In order to filter out specific data we need, we need to use a **WHERE** clause in our queries.
	- Can be inefficient since it is checking every row of data for a specific value
	- Can create more complex **WHERE** clauses by adding **AND/OR** logical keywords to the query
- **WHERE** clause also works with columns containing text data
	- Table below shows the useful operators to do things like case-insensitive comparisons and wildcard pattern matching

| Operator     | Condition                                                                                             | Example                                                                             |
| ------------ | ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| =            | Case sensitive exact string comparison                                                                | `colName = "abc"`                                                                   |
| != or <>     | Case sensitive exact string inequality comparison                                                     | `colName != "abcd"`                                                                 |
| LIKE         | Case insensitive exact string comparison                                                              | `colName LIKE "ABC"`                                                                |
| NOT LIKE     | Case insensitive exact string inequality comparison                                                   | `colName NOT LIKE "ABCD"`                                                           |
| %            | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) | `colName LIKE "%AT%"` (matches "==AT==", "==AT==TIC", "C==AT==" or even "B==AT==S") |
| __           | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE)                    | `colName LIKE "AN_"` (matches "AND", but not "AN")                                  |
| IN (...)     | String exists in a list                                                                               | `colName IN ("A","B","C")`                                                          |
| NOT IN (...) | String does not exist in a list                                                                       | `colName NOT IN ("D","E","F")`                                                      |

- **DISTINCT** keyword will blindly remove duplicate rows
- SQL provides a way to sort your results by a given column in **ASC** or **DESC** order using the **ORDER BY** clause
	- Each row is sorted alpha-numerically based on the specified column's value
- **LIMIT** and **OFFSET** are clauses used with the **ORDER BY**
	- Useful optimization to indicate to the database the subset of the results you care about
	- **LIMIT** will reduce the number of rows returned
	- **OFFSET** specifies where to begin counting the number rows from
	- These clauses let databases execute queries faster and more efficiently by only returning the requested content
- Database normalization is useful because it minimizes duplicate data in any single table
	- allows for the data to grow independently of each other
- Tables that share info about a single entity, need to have a primary key that indentifies that entity uniquely across the DB
	- Common primary key is an auto-incrementing integer
	- Can be string, hashed value, so long as it is unique
- **JOIN** clause allows us to combine row data across two sepearate tables using this unique key.
- **INNER JOIN** is a process that matches rows from the first table and the second table which have the same key
	- Creates a result row with the combined columns from both tables
- If two tables have asymmetric data it would be best to use a **LEFT JOIN, RIGHT JOIN or FULL JOIN**
- Table A and Table B
	- **LEFT JOIN** simply includes rows form A regardless of a matching row is found in B
	- **RIGHT JOIN** simply includes rows from B regardless of a matching row is found in A
	- **FULL JOIN** means that rows from both tables are kept, regardless of whether a matching row exists in the other table.
- Best practice is to reduce the possibility of NULL values due to having to create special queries
- Alternative to NULL is to have data-type appropriate default values
	- Like 0 for numerical data, "" for text data
- We can use expressions to write more complex logic on column values in a query
	- These can use mathematical and string functions
- SQL supports the use of aggregate fucntions/expressions
	- allows to summarize info about a group of rows of data

`SELECT AGG_FUNC(_column_or_expression_) AS aggregate_description**, … FROM mytable WHERE _constraint_expression_;`

- With specified grouping, each aggregate function is going to run on the whole set of result rows and return a single value

COMMON AGGREGATE FUNCTIONS

| FUNCTION                    | DESCRIPTION                                                                                                                                                                               |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **COUNT(*), COUNT(column)** | A common function used to count the number of rows in the group if no column name is specified. Otherwise count the number rows in the group with non-NULL values in the specifid column. |
| **MIN(column)**             | Finds the smallest numerical value in the specified column for all rows in the group.                                                                                                     |
| **MAX(column)**             | Finds the largest numerical value in the specified column for all rows in the group.                                                                                                      |
| **AVG(column)**             | Finds the average numerical value in the specified column for all rows in the group.                                                                                                      |
| **SUM(column)**             | Finds the sum of all numerical values in the specified columnf or the rows in the group.                                                                                                  |
- **GROUP BY** clause works by grouping rows that have the same value in the column specified
`SELECT AGG_FUNC(_column_or_expression_) AS aggregate_description, … FROM mytable WHERE _constraint_expression_ GROUP BY column;`

- **HAVING** is specifically used with the **GROUP BY** clause to allow us to filter grouped rows form the result set
	- Written the same a **WHERE** clause would be written
`SELECT group_by_column, AGG_FUNC(_column_expression_) AS aggregate_result_alias, … FROM mytable WHERE _condition_ GROUP BY column HAVING group_condition;`

`SELECT DISTINCT column, AGG_FUNC(_column_or_expression_), …
`FROM mytable
	`JOIN another_table 
		`ON mytable.column = another_table.column 
	`WHERE _constraint_expression_ 
	`GROUP BY column 
	`HAVING _constraint_expression_ 
	`ORDER BY _column_ ASC/DESC 
	`LIMIT _count_ OFFSET _COUNT_;`

QUERY ORDER OF EXECUTION
1. The **FROM** and **JOIN** clauses are the first executed to determine the total working set of data that is being queried. This includes all subqueries in the clause, and can cause temporary tables to be created under the hood, containing all the columns and rows of the tables being joined.
2. **WHERE** once all the total working set of data is created, the first-pass **WHERE** constraints are appleid to the individual rows. Any row that does not satisfy the constraint are discarded. Each of the constraints can only access the columns directly from the tables requested in the **FROM** clause.
3. **GROUP BY** The remaining rows after the **WHERE** constraint, are then group based on common values in the column specified in the **GROUP BY** clause. As a result of the grouping, there will ony be as many rows as there are unique values in that column.
4. **HAVING** if the query has a **GROUP BY** clause, then the constraints on the **HAVING** clause are then applied to the grouped rows. Similarly how the **WHERE** clause works
5. **SELECT** clause is finally computed
6. **DISTINCT** discards duplicate values in the marked column
7. **ORDER BY** grabs the rows and then sorts them by the specified data in either ASC or DESC order
8. **LIMIT / OFFSET** finally rows that fall outside the range specified by the **LIMIT** and **OFFSET** clause are discarded, leaving the final set of rows to be returned in the query

SIMPLIFIED ORDER OF EXECUTION
1. **FROM AND JOIN**
2. **WHERE**
3. **GROUP BY**
4. **HAVING**
5. **SELECT**
6. **DISTINCT**
7. **ORDER BY**
8. **LIMIT / OFFSET**

- Database Schema is what describes the structure of each table, and the datatypes that each column of the table can contain
- **INSERT** statement allows for inserting data in the a database
	- Declares which table to write into, the columns of data that we are filling, and one or more rows of data to insert.
`INSERT INTO mytable 
	`VALUES (value_or_expr, another_value_or_expr, …), 
		`(value_or_expr_2, another_value_or_expr_2, …), 
		`…;`
- If you have incomplete data and the table contains columns that support default values, you can insert rows with only the columns of data you have by specifying them explicitly
	- In these cases, values need to match the number of columns specified
	- This way is forward compatible since if we add a new column, no old insert statement will beed to be cahnged
`INSERT INTO mytable 
`(column, another_column, …) 
`VALUES (value_or_expr, another_value_or_expr, …), 
	(`value_or_expr_2, another_value_or_expr_2, …), 
	`…;`

- **UPDATE** statement allows us to update existing data
	- have to specify exactly which table, column, and rows to update
`UPDATE mytable 
`SET column = value_or_expr, 
``	other_column = another_value_or_expr, … 
`WHERE condition;`

- When needing to delete data from a table, use the **DELETE** statement
	- Choose table to act on, and the rows of the table to delete through the **WHERE** clause
		- If **WHERE** clause is not used, then all rows are deleted
`DELETE FROM mytable 
`WHERE condition;`

- Use the command **CREATE TABLE** to create a new table.
	- Structure of the new table is defined by its table schema, which defines a series of columns.
	- Each column has a name, type of data allowed in the column, an optional table constraint on values being inserted, and an optional default value.

| Data type                                      | Description                                                                                                                                                                                                                                                                                     |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| INTEGER, BOOLEAN                               | Integer can store whole integer values like the count of a number or an age. Boolean values are just represented as an integer value of just 0 or 1                                                                                                                                             |
| FLOAT, DOUBLE, REAL                            | Floating point can store more precise numerical data like measurements or fractional values. Different types can be used depending on the floating point precision required for that value                                                                                                      |
| CHARACTER(num_chars), VARCHAR(num_chars), TEXT | Text base datatypes, can store strings and text in all sorts of locales. Distinction between the types of data types, is determined by the efficiency of the database when working with these columns. CHARACTER and VARCHAR are specified with the max number of character that they can store |
| DATE, DATETIME                                 | SQL can also store date and time stamps to keep track of time series and event data. Can be tricky to work with if dealing with data across timezones                                                                                                                                           |
| BLOB                                           | Stores binary data in blobs right in the DB. Often opaue to the DB, so they must be stored with the right meta data to requery them                                                                                                                                                             |



| Constraint         | Description                                                                                                                                                                                                                                                                                                                               |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PRIMARY KEY        | This mean that the values in this column are unique, and each value can be used to identify a single row in this table                                                                                                                                                                                                                    |
| AUTOINCREMENT      | For integer values, this means that the value is auto filled in and incremented with each row insertion.                                                                                                                                                                                                                                  |
| UNIQUE             | Means that values in this column have to be unique, so you can't insert another row with the same value in this column as another row in the table. Differs from 'PRIMARY KEY' in that it does not have to be a key for a row in the table                                                                                                |
| NOT NULL           | Data inserted cannot be NULL                                                                                                                                                                                                                                                                                                              |
| CHECK (expression) | Allows to run a more complex expression to test whether the values inserted are valid. EX: check if the values are positive, or greater than a specific size, or start with a certain prefix, etc                                                                                                                                         |
| FOREIGN KEY        | Consistency check which ensures that each value in this column corresponds to another value in a column in another table. EX: One table listing all Employees by ID, and another listing their payroll info, the 'FOREIGN KEY' can ensure that every row in the payroll table corresponds to a valid employee in the master Employee list |

`CREATE TABLE IF NOT EXISTS mytable ( 
	`column _DataType_ _TableConstraint_ DEFAULT _default_value_, another_column _DataType_ _TableConstraint_ DEFAULT _default_value_, … 
`);`

`CREATE TABLE movies ( 
	`id INTEGER PRIMARY KEY, 
	`title TEXT, 
	`director TEXT, 
	`year INTEGER, 
	`length_minutes INTEGER 
`);`

- use **ALTER TABLE** statement to add, remove, or modify columns and table constraints
- When adding column use the **ADD** statement
	- Must specify the data type of the column along with any potential table constraints and default values to be applied to both existing and new rows.
	- Use FIRST or AFTER clauses when trying to insert a new column in a certain location
`ALTER TABLE mytable 
`ADD column _DataType_ _OptionalTableConstraint_ 
		`DEFAULT default_value;`
- Dropping a column, use the **DROP** statement
	- Some databases do not support the statement, you have to create a new table and migrate the data over

`ALTER TABLE mytable 
`DROP column_to_be_deleted;`

- To rename the table itself, use **RENAME TO**
`ALTER TABLE mytable 
`RENAME TO new_table_name;`

- When removing a entire table and all its data and metadata, use the **DROP TABLE** statement
	- Differs from **DELETE** due to it removing the entire table schema from the DB
`DROP TABLE IF EXISTS mytable;`

- A subquery can be referenced anywhere a normal table can be referenced
	- Inside a FROM clause, you can JOIN subqueries with other tables
	- Inside a WHERE or HAVING constraint, you can test epxressions against the results of the subquery
	- In SELECT clauses, you can return data directly from the subquery
	- Since subqueries can be nested, each subquery must be fully enclosed in parentheses in order to establish proper hierarchy
	- Subqueries can reference any tables in the DBm and make use of the constructs of a normal query
	  
	EXAMPLE OF A SUBQUERY
	
	`SELECT * 
	`FROM employees 
	`WHERE salary > 
	``	(SELECT AVG(revenue_generated) 
	``	FROM employees);

- Correlated subquery is a more powerful subquery in which the inner query references, and is dependent on, a column or alias from the outer query
	- Each of these inner queries need to be run for each of the rows in the outer query, since the inner query is dependent on the current outer query row

`SELECT * 
`FROM employees 
`WHERE salary > 
	`(SELECT AVG(revenue_generated) 
	`FROM employees AS dept_employees 
	`WHERE dept_employees.department = employees.department);

- Existence tests is used in complex queries this can be extended using subqueries to test whether a column value exists in a dynamic list of values
- When working with mulitple tables, the UNION and UNION ALL operator allows you to append the results of one query to another assuming that they have the same column count, order, and data type.
	- IF UNION is used without the ALL, duplicate rows between the tables will be removed from the results
`SELECT column, another_column 
``	FROM mytable 
`UNION / UNION ALL / INTERSECT / EXCEPT 
`SELECT other_column, yet_another_column 
``	FROM another_table** 
`ORDER BY column DESC 
`LIMIT _n_;

- UNION happens before the ORDER BY and LIMIT
- Similar to UNION, the INTERSECT operator will ensure that only rows that are identical iin both result sets are returned
- the EXCEPT operator will ensure that only rows in the first result set that arent in the second are returned
- 