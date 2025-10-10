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

