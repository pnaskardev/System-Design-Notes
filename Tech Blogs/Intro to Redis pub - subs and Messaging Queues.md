Redis is an open source, in-memory data-structure store used as a database, cache and message broker.
One of the key features of Redis is the ability to keep all of its data in-memory which gives high performance and low latency access of data.
### In memory data structure store
Redis is very similar to a database, only it is storing the information in memory unlike a typical database. That doesn't mean it doesn't have persistence.
- #### RDB (Redis Database File): -
	- The RDB persistence performs point-in-time snapshots of your dataset at specified intervals. It creates a compact single-file representation of the entire Redis dataset. The snapshotting process can be configured to run at a specified intervals, such as every X minutes if Y keys have been changed.
- `save  300 10      # Save the dataset every 300 seconds if at least 10 keys change`
- AOF (Append Only File):
	- The AOF persistence logs every write operation received by the server, appending each operation to a file. This file can then replayed on startup to reconstruct the dataset.

#### Starting redis locally
Let us start redis locally and start using at a database with the help Docker and Docker volumes.
-  Create docker volume to persist the redis data in the volume
	- `docker volume create redis-data`
-  Let's start redis locally and start using it as a DB 
	- `docker run -d --name redis-container -p 6379:6379 -v redis-data:/data redis`
- Connecting to container
	- `docker exec -it <container_id> /bin/bash`
- Connecting to the Redis CLI
	- `redis-cli`

#### Redis as a DB
- ##### SET/GET/DEL
	- Setting Data
		- `SET sampleKey "HELLO"`
	- Getting Data
		- `GET sampleKey`
	- Deleting Data
		- `DEL samplKey`
- ##### HSET/HGET/HDEL (H=Hash)
	- H basically stands for hash data-structure
	- We can think of Redis hash like a JSON object:
		- ```
		  {
			  "name":"Priyanshu",
			  "role":"DEVELOPER",
			  age:23
		  }
		  ```
	  - We can use **HSET** to set the above data as a Hash data structure in Redis
		  - `HSET user:100 name "Priyanshu" role "DEVELOPER" age 23`
	  - HGET
		  - `HGET user:100 name`
		  - `HGET user:100 role`
		  - `HGET user:100 age`
	  - HDEL
		  - `HDEL user:100 name role age`
### Redis as a queue
You can also push to a `topic`/`queue` on Redis and other processes can `pop` from it 
One good example of this is can be leetcode architecture where submissions need to be processed asynchronously.

`