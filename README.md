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