---
title: "Reading Syntax Diagrams"
weight: 10
date: 2019-12-04T11:15:23-05:00
---

# Reading Syntax Diagrams

This site uses 'syntax diagrams' to consicely describe valid SQL syntax for certain operations.  Syntax diagrams are **not** valid SQL, but are instead meant to indicate a number of different valid **interpretations**.  There are a number of symbols that will not be part of an SQL command based on the syntax diagram.  Rather, the symbols provide information about the meaning of the syntax diagram.

## Optional Syntax

Square brackets `[]` indicate text that is **optional**.  For example, in the syntax diagram...

{{< sqldiagram >}}
```mysql
UNIQUE [KEY]
```
{{< /sqldiagram >}}

... the `KEY` part is optional, so both the statements `UNIQUE` and `UNIQUE KEY` are valid interpretations of the diagram, but the statement `UNIQUE [KEY]` is **not** because the square brackets are not meant to be part of an SQL statement—they are just there to indicate which part of the statement is optional.

## One-of Syntax

The pipe symbol `|` is used to separate a set of options, **one of** which must be chosen.  For example, in the syntax diagram...

{{< sqldiagram >}}
```mysql
ASC | DESC
```
{{< /sqldiagram >}}

... you could interpret it as meaning either `ASC` or `DESC`, but not both, and certainly not `ASC | DESC` (because the pipe symbol is again not part of a valid SQL statement but is instead there in the syntax diagram to indicate a set of choices).

## Group Syntax

Curly braces `{}` are often used to **group** a set of options separated by the pipe symbol.  

 For example, given the syntax diagram...

{{< sqldiagram >}}
 ```mysql
 { UNIQUE [KEY] | [PRIMARY] KEY }
 ```
 {{< /sqldiagram >}}

...the following are all valid interpretations...

{{< sqldiagram >}}
```mysql {linenos=table}
UNIQUE
UNIQUE KEY
PRIMARY KEY
KEY
```
{{< /sqldiagram >}}

...but these are not...

{{< sqldiagram >}}
```mysql {linenos=table}
UNIQUE PRIMARY   -- You must choose either UNIQUE [KEY] OR [PRIMARY] KEY
PRIMARY          -- If you choose the [PRIMARY] KEY option then KEY is required
{UNIQUE [KEY]|}  -- Curly braces, square brackets, and pipes are NOT SQL, they are just syntax diagram meta data
```
{{< /sqldiagram >}}


{{< hint info >}}
If square brackets were used instead of the curly braces in the example above then the entire segment of syntax would be optional.  With the curly braces, though, then **one** of the options **must** be chosen.
{{< /hint >}}

## Identifier Syntax

Angle brackets `<>` may be used around a name to indicate a portion of the SQL that is meant to be an identifier (eg. a table name or a column name) or a value.  For example, the syntax diagram...

{{< sqldiagram >}}
```mysql
CREATE SCHEMA <db_name>
```
{{< /sqldiagram >}}

...indicates that you must use a valid name in place of the `<db_name>`.  So `CREATE SCHEMA mydb` would be a valid interpretation of the above syntax diagram.  (Note that unlike the syntax diagram, there are **no angle brackets** in the actual valid SQL statement interpretation.)

## Repeating Syntax

The `..` symbol means ‘the previous segment of syntax may be repeated.  If the `..` symbol is preceeded by a comma (`,`) then repetitions should be separated by a comma.  For example...

{{< sqldiagram >}}
```mysql
<col_name>, ..
```
{{< /sqldiagram >}}

...can be interpreted to mean 'a comma-separated list of at least one column name'.  Each of the lines below is a valid interpretation of the above syntax diagram...

{{< sqldiagram >}}
```mysql {linenos=table}
id
first_name, last_name
street, city, prov, postal_code
```
{{< /sqldiagram >}}

...but none of the lines below is valid...

{{< sqldiagram >}}
```mysql {linenos=table}
                        -- at least one <col_name> must be given
first_name last_name    -- the list must be comma separated
```
{{< /sqldiagram >}}

## Sub-diagrams

Sometimes trying to get the complete syntax for one SQL statment into a single syntax diagram makes things too complicated to read.  Instead, a sub-diagram may be referred to using double angle brackets `<<>>`

For example, in the diagram below, `<<DataType>>` refers to a separate sub-diagram labelled 'Data Type':

{{< sqldiagram >}}
```mysql
<<DataType>> [[NOT] NULL] [DEFAULT <default_value>] 
    [AUTO_INCREMENT] [ UNIQUE [KEY] | [PRIMARY] KEY ]
```
{{< /sqldiagram >}}

#### Data Type

{{< sqldiagram >}}
```mysql
    INT[EGER][(<length>)] [UNSIGNED]
  | DATE
  | VARCHAR(length)
```
{{< /sqldiagram >}}

A valid interpretation of the first diagram above must include a valid interpretation of the sub-diagram in place of the `<<DataType>>` reference.

For example, each the following lines is a valid SQL statement based on the above two diagrams:

{{< sqldiagram >}}
```mysql {linenos=table}
DATE   -- Only the <<DataType>> is a required component
INT UNSIGNED AUTO_INCREMENT PRIMARY KEY
VARCHAR(50) NOT NULL DEFAULT ''
```
{{< /sqldiagram >}}

But the following is not valid because it does not include a `<<DataType>>`:

{{< sqldiagram >}}
```mysql
NOT NULL
```
{{< /sqldiagram >}}
