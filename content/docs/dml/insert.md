---
title: "Insert"
date: 2020-01-09 16:23:14
draft: false
weight: 10
---

# Insert

```mysql
INSERT [INTO] <table_name> [(<col_name>,..)] 
{ 
    VALUES ({<value>|DEFAULT},..),..
  | <<SelectStatement>>
}
```

{{< hint info >}}
You can insert multiple rows by including multiple sets of values in a comma-separated list, each set of values in parentheses.
{{< /hint >}}
{{< hint info >}}
If no set of column names is specified, the set of values will be inserted in the order of the columns in the table.
{{< /hint >}}
{{< hint info >}}
Using the `DEFAULT` keyword instead of an explicit value causes the default value to be inserted for that column.
{{< /hint >}}
{{< hint info >}}
An `INSERT .. SELECT` statement will fail if the set of columns in the select statement does not match in order and data type the set of columns into which the values are being inserted.
{{< /hint >}}

#### Examples

A basic insert

```mysql
INSERT INTO person VALUES (1, 'Alice', 'Ali')
```

A multi-row insert

```mysql
INSERT INTO account (id, amount) VALUES
    (13912, 100.00),
    (37322, 828.02),
    (23820, DEFAULT)
```

A multi-row insert, including AUTO_INCREMENT surrogate key values

```mysql
INSERT INTO person (id, fname, lname) VALUES
   (1, 'Alice', 'Ali'),
   (2, 'Bob', 'Borden'),
   (3, 'Charlie', 'Crox')
```

A multi-row insert, NOT including the AUTO_INCREMENT surrogate key valuues.  (The database engine determines and inserts unique values automatically.)

```mysql
INSERT INTO person (lname, fname) VALUES
   ('Ali', 'Alice'),
   ('Borden', 'Bob'),
   ('Crox', 'Charlie')
```

Inserting the results of a `SELECT` into a table

```mysql
INSERT INTO old_person SELECT * FROM person WHERE person.age > 65
```

Inserting specific columns of the results of a `SELECT` into a table

```mysql
INSERT INTO old_person (first_name, last_name)
    SELECT fname, lname FROM person WHERE person.age > 65
```