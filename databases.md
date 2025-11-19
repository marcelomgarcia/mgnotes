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

## Selecting Rows from a TIMESTAMP Field

How can I select all rows for a specific date (2018-07-08) from a TIMESTAMP field in MySQL 8?

### Table Structure
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

### Best Solution (Index-Friendly)

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

## Database Size

Printing the size of the databases in MB:

```
mysql> select table_schema "DB Name", ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB size in MB" FROM information_schema.tables GROUP BY table_schema;
+--------------------+---------------+
| DB Name            | DB size in MB |
+--------------------+---------------+
| information_schema |           0.0 |
| mysql              |           2.7 |
| performance_schema |           0.0 |
| prod_doiMinter     |         622.3 |
| prod_ioi           |        3046.1 |
| prod_irts          |       83324.5 |
| prod_repository    |         828.9 |
| sys                |           0.0 |
+--------------------+---------------+
8 rows in set (3.05 sec)

mysql>
```

## Table Size

Finding the [size of a table](https://stackoverflow.com/questions/9620198/how-can-i-get-the-sizes-of-the-tables-of-a-mysql-database):

```
mysql> SELECT table_name AS `Table`, round(((data_length + index_length) / 1024 / 1024), 2) `Size in MB` FROM information_schema.TABLES WHERE table_schema = "prod_irts"  AND table_name = "deletedMetadata";
+-----------------+------------+
| Table           | Size in MB |
+-----------------+------------+
| deletedMetadata |   14531.81 |
+-----------------+------------+
1 row in set (0.00 sec)
```

## List Users

List the [users](https://www.mysqltutorial.org/mysql-administration/mysql-show-users/) of the MySQL instance:

```
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select user from user;
+------------------+
| user             |
+------------------+
| root             |
| mysql.infoschema |
| mysql.session    |
| mysql.sys        |
| root             |
+------------------+
5 rows in set (0.00 sec)

mysql> select user,host,account_locked,password_expired from user;
+------------------+-----------+----------------+------------------+
| user             | host      | account_locked | password_expired |
+------------------+-----------+----------------+------------------+
| root             | %         | N              | N                |
| mysql.infoschema | localhost | Y              | N                |
| mysql.session    | localhost | Y              | N                |
| mysql.sys        | localhost | Y              | N                |
| root             | localhost | N              | N                |
+------------------+-----------+----------------+------------------+
5 rows in set (0.00 sec)

```

## Adding User

Adding and granting permissions to a [user](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql) in the database

```
mysql> CREATE USER 'irts'@'localhost' IDENTIFIED BY 'my-passwd';
Query OK, 0 rows affected (0.07 sec)

mysql> CREATE USER 'irts'@'%' IDENTIFIED BY 'my-passwd';
Query OK, 0 rows affected (0.01 sec)

mysql> quit;
Bye
garcm0b@KW20207:~/Work/IRTSv2/updates$
```

Creating the user is not enough for him to have access to the database:

```
garcm0b@KW20207:~/Work/IRTSv2/updates$ mysql -h localhost --protocol=tcp --port=3336 -u irts -p prod_irts
Enter password:
ERROR 1044 (42000): Access denied for user 'irts'@'%' to database 'prod_irts'
garcm0b@KW20207:~/Work/IRTSv2/updates$
```

So we need to grant permissions to the user:

```
mysql> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'irts'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.02 sec)

mysql> flush privileges
    -> ;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'irts'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.02 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> show grants for 'irts'@'%';
+----------------------------------------------------------------------------------------------------------------------+
| Grants for irts@%                                                                                                    |
+----------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, REFERENCES, ALTER ON *.* TO `irts`@`%` WITH GRANT OPTION |
+----------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> exit;
Bye
```

Testing the user

```
garcm0b@KW20207:~/Work/IRTSv2/updates$ mysql -h localhost --protocol=tcp --port=3336 -u irts -p prod_irts
Enter password:
Reading table information for completion of table and column names

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 8.0.43 MySQL Community Server - GPL
(...)
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> quit;
Bye
garcm0b@KW20207:~/Work/IRTSv2/updates$
```

## Performance Monitoring

Tools and docs to monitor performance of MySQL database:

* [MySQL Performance Monitoring: Best Practices](https://www.percona.com/blog/mysql-performance-monitoring-best-practices/)
* [How to Monitor MySQL Database Performance](https://docs.digitalocean.com/products/databases/mysql/how-to/monitor-databases/)
* [How To Use Mytop to Monitor MySQL Performance](https://www.digitalocean.com/community/tutorials/how-to-use-mytop-to-monitor-mysql-performance)

## Docker MySQL

### Image

MySQL docker image:

```
garcm0b@KW20207:/data/databases/zipped$ docker image ls
REPOSITORY    TAG               IMAGE ID       CREATED        SIZE
mysql         8.0.43-bookworm   30e5fef766c9   4 weeks ago    610MB
hello-world   latest            74cc54e27dc4   9 months ago   10.1kB
garcm0b@KW20207:/data/databases/zipped$
```

### Secrets

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

### Initializing a New Instance

To initialize the new container with a MySQL dump file, start the container mount the directory with the dump file binded to the docker folder `/docker-entrypoint-initdb.d.` The installation of MySQL are mounted to another directory because we ingestion of the dump file was reaching the limit of Docker volume. The initialization of the container require extra parameters because the the dump file is very large (44GB) and the machine runnig Docker has high specs.

```
docker run \
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
```

### Starting the Database

Once the database is initialized, we will start it using a slightly different set of parameters


```
docker run \
  --rm \
  --name prod_irts \
  --cpus="16" \
  --memory="32g" \
  --memory-swap="32g" \
  -e MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD \
  --volume /data/databases/mysql_data:/var/lib/mysql \
  -p 3336:3306 \
  --detach \
  mysql:8.0.43-bookworm \
  --innodb-buffer-pool-size=24G \
  --innodb-flush-log-at-trx-commit=1 \
  --innodb-flush-method=O_DIRECT \
  --max-allowed-packet=256M \
  --innodb-write-io-threads=8 \
  --innodb-read-io-threads=8
```

### Accessing the Database

Accessing the database inside the container:

```
garcm0b@KW20207:/data/databases/mysql_data$ docker exec -it \
> prod_irts mysql -u root -p prod_irts
Enter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

(...)

mysql>
```

Or you can access the container with the `mysql` client specifying the protocol and port to the container:

```
$ mysql -h localhost --protocol=tcp --port=3336 -u root -p prod_irts
Enter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

(...)
```

### Importing SQL Dump File

In case necessary to import more databases after the initialization, import the new database as a restoring a regular MySQL dump. Probably it will be necessary to unzip the dump file

```
garcm0b@KW20207:/data/databases/zipped$ gzip -dc prod_ioi_2025-10-26_06h25m.Sunday.sql.gz > ../test/prod_ioi_2025-10-26_06h25m.Sunday.sql
garcm0b@KW20207:/data/databases/zipped$
garcm0b@KW20207:/data/databases/zipped$ cd ../test/
garcm0b@KW20207:/data/databases/test$ docker exec -i prod_irts sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < prod_ioi_2025-10-26_06h25m.Sunday.sql
mysql: [Warning] Using a password on the command line interface can be insecure.
garcm0b@KW20207:/data/databases/test$
```



### Reference
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

