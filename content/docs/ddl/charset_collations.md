---
title: "Character Sets & Collations"
date: 2019-12-16T10:56:00
weight: 1000
---

# Character Sets & Collations

In MySQL, you can set a character set and/or collation for a whole database, for an individual table, or for a specific column in a table.

See [Appendix A](/docs/appendix/a) for more detailed information about Character Sets and Collations.

```mysql
-- Within the context of a CREATE DATABASE or CREATE TABLE statement, 
-- or in a column's data type definition within a CREATE TABLE statement
[ CHARACTER SET <encoding_name> ] [ COLLATE <collation_name> ]
```

{{< hint warning >}}
**NOTE**: The `<encoding_name>` and `<collation_name>` must be compatible.  You cannot, for example, use the `utf8mb4_general_ci` collation with the `latin1` character set.
{{< /hint >}}

{{< hint warning >}}
## UTF-8 in MySQL

In MySQL there are two UTF-8 character sets: 'utf8' (which is an alias for 'utf8mb3') and 'utf8mb4'.  

When you want to use the UTF-8 encoding it is best practice to **always** use utf8mb4 because it supports the full set of Unicode 4-byte code points, whereas utf8mb3 only supports the code points of Unicode that can be represented using 3 bytes.
{{< /hint >}}

#### Examples

Setting the character set and collation for a whole [database](/docs/ddl/databases):

```mysql
CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci
-- All text-based columns will be encoded using the database's character set unless otherwise specified
```

Setting the character set and collation for an individual [table](/docs/ddl/tables):

```mysql
CREATE TABLE mydb.mytable (
    -- This table's text columns will be encoded using the its character set
) 
CHARACTER SET latin1 
COLLATE latin1_swedish_ci
```

Setting the character set and collation for a specific text column in a table.

```mysql
CREATE TABLE mydb.mytable (
    -- This column uses a specific character set that is different than the table's default
    mycolumn CHAR(4) CHARACTER SET ascii COLLATE ascii_general_ci,
    -- other column and constraint definitions...
)
CHARACTER SET latin1 
COLLATE latin1_swedish_ci
```