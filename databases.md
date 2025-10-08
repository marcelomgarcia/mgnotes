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
sqlite> describe directors
```

Showing the [schema of the tables](https://www.sqlitetutorial.net/sqlite-describe-table/)

```
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

```
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
