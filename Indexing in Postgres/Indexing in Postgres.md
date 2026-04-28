### How does a query work

Lets say that we are running a query `SELECT * FROM users WHERE user_id=1`
There is a `Linear search` happening that means the database is performing a linear search from the top of the dataset to the end of the dataset and looks for any record that matches the constraint and returns the record back to whoever asked.

Sometimes Postgres does some optimizations  , it might spawn 2 threads so that the process of looking thought the dataset it sped up.

The question is what can I do as a developer if I know that the queries are slow.

Postgres already does optimizations what can I do on top of it to make queries faster.

INDEXING is the answer

To kick things off lets start a Postgres table locally and see how Indexing helps in making queries fast.

- Let us a postgres DB locally
	`docker run  -p 5432:5432 -e POSTGRES_PASSWORD=mysecretpassword -d postgres`
- Let us connect to it and create some dummy data in it 
		```
	  docker exec -it container_id /bin/bash
	  psql -U postgres
	  ```
-   Create the schema for a simple medium like app
	 ```
	  CREATE TABLE users (
	  user_id SERIAL PRIMARY KEY,email VARCHAR(255) UNIQUE NOT NULL, password
	  VARCHAR(255) NOT NULL, name VARCHAR(255));
	
	CREATE TABLE posts ( 
		post_id SERIAL PRIMARY KEY, user_id INTEGER NOT NULL, title VARCHAR(255)
		NOT NULL, description TEXT, image VARCHAR(255), FOREIGN KEY (user_id)REFERENCES users(user_id) 
	);
	  ```
- Insert some dummy data in 
	- ```
	  
	  DO $$
	  DECLARE 
		  returned_user_id INT;
	  BEGIN
		  -- Insert 5 users 
		  FOR i IN 1..5 LOOP 
			  INSERT INTO users (email, password, name) VALUES
			  ('user'||i||'@example.com', 'pass'||i, 'User '||i) RETURNING user_id
			  INTO returned_user_id; 
			  FOR j IN 1..500000 LOOP INSERT INTO posts (user_id, title,
			  description) VALUES (returned_user_id, 'Title '||j, 'Description for
			  post '||j); 
			  END LOOP; 
		  END LOOP;
	  END $$;
	  
	  ```
- Try running a query to get all the posts of a user and log the time it took
	- ` EXPLAIN ANALYSE SELECT * FROM posts WHERE user_id=1 LIMIT 10000;`
- Focus on the `execution time`
	- ![[Pasted image 20251226132815.png]]
	- Execution time which is 55 ms is not acceptable
	- The Solution is to add an index
- Add an index to user_id in Posts table
	- `CREATE INDEX idx_user_id ON posts (user_id);`
	- ![[Pasted image 20251226132948.png]]
	- This particular line is trying to tell the database that Hey I will query you a lot so please index yourself based on `user_id` 
- Notice the execution time
	- ![[Pasted image 20251226133031.png]]
	- What really happened in here?? What did this one line of code do to take the execution time of the query from 55 ms to 0.058 ms which is 1000% times faster.

### How indexing works?
- When you create an index on a field, a new data structure (usually B-tree) is created that stores the mapping from the `index column` to the `location` of the record in the original table.

- Search on the index is usually `log(n)`

The current problem is that if there is no index present, the database does a full scan of the table

but whatif we somehow cached the data based on user_id in an index.

When **no index is present**, the database has no shortcut to locate rows. It must perform a full table scan and match the query constraint.
- ![[Pasted image 20251226134531.png]]

With Indexes the database a shortcut and doesn't need to do a full table scan and will only look into parts of the table.
- The data pointer (in case of postgres) is the `page` and `offset` at which this record can be found.
- Think of the index as the `appendix` of a book and the `location` as the `page + offset` of where this data can be found
- ![[Pasted image 20251226134601.png]]

### Overhead of having an index
Writes become heavier cause we are not just inserting the new record into the table, we are also inserting it into the index.

But that is actually fine cause most applications are read heavy and not write heavy, if we take an example of Instagram we are mostly reading a lot of data and not writing a lot of data.

### Complex Indexes
Not every time our queries are going to be simple like `SELECT * FROM users WHERE user_id=1`

In a real world scenario it will be like `SELECT * FROM posts WHERE title='s' AND descriptor='asd`;

Let us try to analyze this query
![[Pasted image 20251226140829.png]]

Notice the Execution time

This suffers from the same problem of not having an index because we are searching based on title and description. 

The index we had won't work anymore.

Each query will have a separate index based on the constraints we are searching.

The solution is simple we just create a another index based on the query constraints.

We can have index on more than one column for more complex queries
For example, 
Give me all the posts of a user with given id with title “Class 1”.

The index needs to have two keys now
- `CREATE INDEX idx_posts_user_id_title ON posts (description, title);`
Let us try searching before the index is added and after it is added.
- ` SELECT * FROM posts WHERE title='title' AND description='my title';`
- ![[Pasted image 20251226141515.png]]
Notice the Execution time.

This is called a Complex Index where we support queries that involve **multiple columns, expressions, conditions, or access patterns**, beyond simple single-column equality lookups.