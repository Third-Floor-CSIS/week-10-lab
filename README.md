# Lab 10

## Excercise 1

```yaml
version: '3.8'

services:
  db:
    image: mysql:9.1
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: testing
    ports:
      - "3306:3306"
    volumes:
      - ./dev/database:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
```

```sql
SELECT * 
FROM actors
WHERE
    (first_name LIKE 'John%' OR first_name LIKE 'Jo%'OR first_name LIKE 'H%')
    AND
    (last_name LIKE 'Doe%' OR last_name LIKE 'D%')
    AND
    (gender = 'M' OR gender = 'F')   
```

## Exercise 2

```sql
EXPLAIN
SELECT *
FROM actors
WHERE
(first_name LIKE 'John%' OR first_name LIKE 'Jo%'OR first_name LIKE 'H%')
AND
(last_name LIKE 'Doe%' OR last_name LIKE 'D%')
AND
(gender = 'M' OR gender = 'F')
```


## Exercise 3
```sql
CREATE INDEX idx_last_name ON actors(last_name);
CREATE INDEX idx_first_last_name ON actors(first_name, last_name);
CREATE INDEX idx_gender ON actors(gender);
```

## Exercise 5

### Prep
To do this part we will have to wipe and reset the database.

1. Shutdown the docker compose
2. Delete the existing database
   * ``sudo rm -rf ./dev``
3. Restart the docker compose

### Arguments

| Argument                           | Common With Mysql | Description                                |
|------------------------------------|-------------------|--------------------------------------------|
| ``mysqlslap``                      |                   | SqlSlap binary                             |
| ``--host=localhost``               | yes               | The IP address of the server               |
| ``--port=3306``                    | yes               | The Port of the server                     |
| ``--user=root``                    | yes               | User to access                             |
| ``--password=1234``                | yes               | Password of user                           |
| ``--concurrency=50``               | no                | How many connections to use                |
| ``--iterations=5``                 | no                | How many times to retry the test           |
| ``--create-schema=testing``        | no                | What database we will use for testing      |
| ``--query="SELECT * FROM actors"`` | no                | Pass in a query for testing                |
| `` --query=./query.sql"``          | no                | Same as above but use a file (single line) |


### Direct query
Using this command with teh query embedded within it:  
``mysqlslap --host=localhost --port=3306 --user=root --password=1234 --concurrency=50 --iterations=5 --create-schema=testing --query="SELECT * FROM actors"``

You should get something akin to this
```shell
Benchmark
        Average number of seconds to run all queries: 4.204 seconds
        Minimum number of seconds to run all queries: 4.140 seconds
        Maximum number of seconds to run all queries: 4.256 seconds
        Number of clients running queries: 50
        Average number of queries per client: 1
```

You can also specify a query file such as ``./query.sql``:  
``mysqlslap --host=localhost --port=3306 --user=root --password=1234 --concurrency=50 --iterations=5 --query=./query.sql --create-schema=testing``
```shell
Benchmark
        Average number of seconds to run all queries: 2.940 seconds
        Minimum number of seconds to run all queries: 2.856 seconds
        Maximum number of seconds to run all queries: 3.003 seconds
        Number of clients running queries: 50
        Average number of queries per client: 1
```

### Impact of indices
in a seperate terminal login to mysql and apply the indices again:
```sql
CREATE INDEX idx_last_name ON actors(last_name);
CREATE INDEX idx_first_last_name ON actors(first_name, last_name);
CREATE INDEX idx_gender ON actors(gender);
```

Then re-run the previous command, note the reduction in time
```shell
Benchmark
        Average number of seconds to run all queries: 1.641 seconds
        Minimum number of seconds to run all queries: 1.629 seconds
        Maximum number of seconds to run all queries: 1.666 seconds
        Number of clients running queries: 50
        Average number of queries per client: 1
```