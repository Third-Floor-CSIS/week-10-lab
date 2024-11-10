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