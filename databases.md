Databases
=========

Working with all kinds of databases.

# SQLite

Working SQLite for simple databases.

## Installing

Installing the SQLite on Linux

```
sudo apt install sqlite3
```

## Using SQLite

## Connecting

Connecting to the database:

```
sqlite3 movie_club.db
```

## Schema

Showing the tables in the database

```
sqlite> .tables
directors  movies
sqlite>
```

Showing the [schema of the tables](https://www.sqlitetutorial.net/sqlite-describe-table/)

```sql
sqlite> .schema directors
CREATE TABLE IF NOT EXISTS "directors" (
        "id"    INTEGER NOT NULL,
        "name"  TEXT NOT NULL,
        PRIMARY KEY("id" AUTOINCREMENT)
);
```

> Note that there no semicolon (`;`)

### Schema with Pragma

Using `pragma` command to describe the structure of the table

```
sqlite> .header on
sqlite> .mode column
sqlite> pragma table_info('directors');
cid  name  type     notnull  dflt_value  pk
---  ----  -------  -------  ----------  --
0    id    INTEGER  1                    1
1    name  TEXT     1                    0
sqlite>
```

### Schema from SQLite Info

Querying the table schema from the `sqlite_schema` table:

```sql
sqlite> SELECT sql FROM sqlite_schema WHERE name = 'directors';
sql
---------------------------------------
CREATE TABLE "directors" (
        "id"    INTEGER NOT NULL,
        "name"  TEXT NOT NULL,
        PRIMARY KEY("id" AUTOINCREMENT)
)
sqlite>
```

## Exporting

Exporting the SQLite database as [CSV](https://www.sqlitetutorial.net/sqlite-export-csv/):

```
mgarcia@arda:~/Work/MovieClubSched$ sqlite3 movie_club.db
SQLite version 3.45.1 2024-01-30 16:01:20
Enter ".help" for usage hints.
sqlite> .headers on
sqlite> .mode csv
sqlite> .output movies.csv
sqlite> SELECT title,director_id, year, country, url, screen_date, host, attendance FROM movies;
sqlite> .output stdout
sqlite> SELECT title,director_id, year, country, url, screen_date, host, attendance FROM movies;
title,director_id,year,country,url,screen_date,host,attendance
"The Godfather",1,1972,USA,https://www.imdb.com/title/tt0068646/,2025-02-07,Marcelo,0
(...)
```

We save the output of the `SELECT` command to a file with the dot-command `.output movies.csv` and changed back to the screeen with `.output stdout.`

### Script

SQL script to export the database as a CSV file:

```
mgarcia@arda:~/Work/MovieClubSched$ cat export_dirs.sql
.headers on
.mode csv
.output directors.csv
SELECT name FROM directors;
```

Then run the script:

```
mgarcia@arda:~/Work/MovieClubSched$ sqlite3 movie_club.db < export_dirs.sql
```

And check the result:

```
mgarcia@arda:~/Work/MovieClubSched$ more directors.csv
name
"Francis Ford Coppola"
"Terry Gilliam"
(...)
```

# MySQL

Selecting Rows by Date from TIMESTAMP Field

## Question
How can I select all rows for a specific date (2018-07-08) from a TIMESTAMP field in MySQL 8?

## Table Structure
```
mysql> DESCRIBE messages;
+-----------+--------------+------+-----+-------------------+-------------------+
| Field     | Type         | Null | Key | Default           | Extra             |
+-----------+--------------+------+-----+-------------------+-------------------+
| messageID | int          | NO   | PRI | NULL              | auto_increment    |
| process   | varchar(200) | NO   | MUL | NULL              |                   |
| type      | varchar(20)  | NO   |     | NULL              |                   |
| message   | longtext     | NO   |     | NULL              |                   |
| timestamp | timestamp    | NO   |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
+-----------+--------------+------+-----+-------------------+-------------------+
```

## Best Solution (Index-Friendly)

```sql
SELECT * FROM messages 
WHERE timestamp >= '2018-07-08 00:00:00' 
  AND timestamp < '2018-07-09 00:00:00';
```

### Why This Is Best
- Can utilize an index on the `timestamp` column (if one exists)
- Avoids applying functions to the indexed column
- Very efficient for large tables
- No risk of missing microseconds at day boundary

## Alternative Solutions

### 1. Using DATE() Function (Simpler but Slower)
```sql
SELECT * FROM messages 
WHERE DATE(timestamp) = '2018-07-08';
```

**Pros:** Clean and readable  
**Cons:** Cannot use index on `timestamp` - causes a full table scan

### 2. Using CAST()
```sql
SELECT * FROM messages 
WHERE CAST(timestamp AS DATE) = '2018-07-08';
```

Similar performance characteristics to `DATE()`.

### 3. Using BETWEEN
```sql
SELECT * FROM messages 
WHERE timestamp BETWEEN '2018-07-08 00:00:00' AND '2018-07-08 23:59:59';
```

**Note:** Be careful with `BETWEEN` - it's inclusive on both ends, so you might miss records at exactly `23:59:59.999999`. The first solution using `< '2018-07-09'` is safer.

## Performance Tips

1. **Add an index if not present:**
   ```sql
   CREATE INDEX idx_timestamp ON messages(timestamp);
   ```

2. **Always use the range comparison method for large tables**

3. **Use EXPLAIN to verify the query uses the index:**
   ```sql
   EXPLAIN SELECT * FROM messages 
   WHERE timestamp >= '2018-07-08 00:00:00' 
     AND timestamp < '2018-07-09 00:00:00';
   ```

## Sample Data
```
mysql> SELECT timestamp,message FROM messages ORDER BY timestamp LIMIT 5;
+---------------------+---------------------------------------------------------------------------------------------+
| timestamp           | message                                                                                     |
+---------------------+---------------------------------------------------------------------------------------------+
| 2018-07-08 01:24:50 | repository: 18143 new items, 0 updated items, 0 deleted items, 18585 total items.           |
| 2018-07-08 11:43:13 | repository: 13 new items, 0 updated items, 0 deleted items, 18585 total items.              |
| 2018-07-08 17:29:32 | repository: 0 new items, 0 updated items, 0 deleted items, 1 total items. - Error count: 8 |
| 2018-07-08 17:34:37 | repository: 0 new items, 0 updated items, 0 deleted items, 1 total items. - Error count: 8 |
| 2018-07-08 18:00:20 | repository: 0 new items, 0 updated items, 0 deleted items, 1 total items. - Error count: 8 |
+---------------------+---------------------------------------------------------------------------------------------+
```

## Conclusion

For production environments and scalability, always prefer the range comparison method. It's the professional choice that will perform well even as your data grows.
