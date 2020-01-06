---
title: "Databases"
date: 2019-12-04T11:01:01-05:00
weight: 10
---

# Databases

## Create

```mysql
CREATE {DATABASE|SCHEMA} [IF NOT EXISTS] <db_name>
  [ CHARACTER SET [=] <charset_code> ]
  [ COLLATE [=] <collation_code> ]
```

{{< hint info >}}
In MySQL, the terms 'database' and 'schema' have the same meaning, but this is **not** always the case in other RDBMSs
{{< /hint >}}

#### Examples

Create a new database named 'test' using the utf8mb4 character set (both these statements to the same thing)

```mysql {linenos=table}
CREATE DATABASE test CHARACTER SET utf8mb4;
CREATE SCHEMA test CHARACTER SET utf8mb4;
```

Create a database named 'test', but **only** if a database named 'test' doesn't already exist:

```mysql
CREATE DATABASE IF NOT EXISTS test;
```

---

## Drop

```mysql
DROP {DATABASE|SCHEMA} [IF EXISTS] <db_name>
```

#### Examples

Delete the database named 'test'

```mysql
DROP DATABASE test;  -- throws an error if there is no such database
```

Delete the database named 'test' **only** if there is a database with that name
```mysql
DROP DATABASE IF EXISTS test;  -- no error, even if there is no 'test' database
```

---

## Alter

```mysql
ALTER {DATABASE|SCHEMA} <db_name>
  { 
      CHARACTER SET [=] charset_name
    | COLLATE [=] collation_name
  } ..
```

#### Examples

Change the character set of a database:

```mysql
ALTER DATABASE test CHARACTER SET latin1;
```

Change both the character set and collation:

```mysql
ALTER DATABASE test CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

--- 

## Use

```mysql
USE <db_name>
```

{{< hint info >}}
In an SQL script or session, after running a `USE` statement, all subsequent statements will apply to the database selected in the statement.
{{< /hint >}}