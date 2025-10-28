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

## Saving Output to File

Saving the output of a `SELECT` command to a file:

```
mgarcia@PC-KL-26743:~$ mysql -h localhost -P 3336 -u irts -p test_irts -e "SELECT rowID,added,sourceData FROM sourceData LIMIT 1;" |tee /tmp/source_data.txt
mgarcia@PC-KL-26743:~$
mgarcia@PC-KL-26743:~$ head -n 2 /tmp/source_data.txt
rowID   added   sourceData
1       2024-03-26 05:00:05     {"30001599":{"orgID":30001599,"name":"911 & CCC","startDate":"2017-07-01","shortName":"9111CCC","parentOrgID":"30000106","type":"officeunit","visibility":"BACKEND"},"30001379":{"orgID":30001379,"name":"Academic & Admin Facilities Maintenance","startDate"(...)
```

# Docker MySQL

## Image

MySQL docker image:

```
garcm0b@KW20207:/data/databases/zipped$ docker image ls
REPOSITORY    TAG               IMAGE ID       CREATED        SIZE
mysql         8.0.43-bookworm   30e5fef766c9   4 weeks ago    610MB
hello-world   latest            74cc54e27dc4   9 months ago   10.1kB
garcm0b@KW20207:/data/databases/zipped$
```

## Secrets

Define a file with variables used by MySQL container:

```
garcm0b@KW20207:~$ vim .env_mysql_docker
```

Then read the variables to the shell environment

```
garcm0b@KW20207:~$ export $(cat .env_mysql_docker | xargs)
garcm0b@KW20207:~$ echo $MYSQL_ROOT_PASSWORD
secret_password
garcm0b@KW20207:~$
```

## Initializing a New Instance

To initialize the new container with a MySQL dump file, start the container mount the directory with the dump file binded to the docker folder `/docker-entrypoint-initdb.d.` The installation of MySQL are mounted to another directory because we ingestion of the dump file was reaching the limit of Docker volume. The initialization of the container require extra parameters because the the dump file is very large (44GB) and the machine runnig Docker has high specs.

```
garcm0b@KW20207:/data/databases/test$ docker run \
  --rm \
  --name prod_irts \
  --cpus="32" \
  --memory="64g" \
  --memory-swap="64g" \
  -e MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD \
  -e MYSQL_INITDB_SKIP_TZINFO=1 \
  --mount type=bind,src=/data/databases/test,dst=/docker-entrypoint-initdb.d \
  --volume /data/databases/mysql_data:/var/lib/mysql \
  --detach \
  mysql:8.0.43-bookworm \
  --innodb-buffer-pool-size=48G \
  --innodb-log-file-size=2G \
  --innodb-flush-log-at-trx-commit=2 \
  --innodb-flush-method=O_DIRECT \
  --max-allowed-packet=1G \
  --innodb-write-io-threads=16 \
  --innodb-read-io-threads=16
3357ea920ee28548d301d58efa6ff784d303352133ec120ac3d11ba04d14a715
garcm0b@KW20207:/data/databases/test$
```

Accessing the database

```
garcm0b@KW20207:/data/databases/mysql_data$ docker exec -it \
> prod_irts mysql -u root -p prod_irts
Enter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

(...)

mysql> show tables;
+---------------------+
| Tables_in_prod_irts |
+---------------------+
| deletedMetadata     |
| deletedSourceData   |
| mappings            |
| messages            |
| metadata            |
| sourceData          |
| transformations     |
| users               |
+---------------------+
8 rows in set (0.01 sec)

mysql> select count(*) from messages;
+----------+
| count(*) |
+----------+
|  7109425 |
+----------+
1 row in set (5.35 sec)

mysql>
```



## Reference
[https://hub.docker.com/\_/mysql/](https://hub.docker.com/_/mysql/)


# DuckDB

Accessing a MySQL via DuckDB. 

```
D attach 'host=localhost user=irts port=3336 database=test_irts password=XXXX' as mysqldb (type mysql);
D
D SELECT rowID,source FROM mysqldb.test_irts.sourceData LIMIT 1;
┌────────┬─────────┐
│ rowID  │ source  │
│ int32  │ varchar │
├────────┼─────────┤
│ 145347 │ arxiv   │
└────────┴─────────┘
D .quit
mgarcia@PC-KL-26743:~$
```

Describing a table

```
D describe mysqldb.prod_irts.sourceData;
┌─────────────────┬──────────────────────────┬─────────┬─────────┬─────────┬─────────┐
│   column_name   │       column_type        │  null   │   key   │ default │  extra  │
│     varchar     │         varchar          │ varchar │ varchar │ varchar │ varchar │
├─────────────────┼──────────────────────────┼─────────┼─────────┼─────────┼─────────┤
│ rowID           │ INTEGER                  │ NO      │ NULL    │ NULL    │ NULL    │
│ source          │ VARCHAR                  │ NO      │ NULL    │ NULL    │ NULL    │
│ idInSource      │ VARCHAR                  │ NO      │ NULL    │ NULL    │ NULL    │
│ sourceData      │ VARCHAR                  │ NO      │ NULL    │ NULL    │ NULL    │
│ format          │ VARCHAR                  │ NO      │ NULL    │ NULL    │ NULL    │
│ added           │ TIMESTAMP WITH TIME ZONE │ NO      │ NULL    │ NULL    │ NULL    │
│ deleted         │ TIMESTAMP WITH TIME ZONE │ YES     │ NULL    │ NULL    │ NULL    │
│ replacedByRowID │ INTEGER                  │ YES     │ NULL    │ NULL    │ NULL    │
└─────────────────┴──────────────────────────┴─────────┴─────────┴─────────┴─────────┘
D
```

