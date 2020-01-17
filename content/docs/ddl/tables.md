---
title: "Tables"
date: 2019-12-04T11:01:01-05:00
weight: 20
---

# Tables

## Create

{{< sqldiagram >}}
```mysql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] <table_name> (
  <<ColumnDefinition>>,..
  [<<ConstraintDefinition>>,..]
)
[ENGINE [=] {InnoDB|MyISAM|NDB}]
[AUTO_INCREMENT [=] <value>]
[CHARACTER SET [=] <charset_name>]
[COLLATE [=] <collation_name>]
```
[<<ColumnDefinition>>]({{< ref "#column-definition" >}})

[<<ConstraintDefinition>>]({{< ref "#constraint-definition" >}})
{{< /sqldiagram >}}

{{< hint info >}}
A `TEMPORARY` table only exists for the duration of the current connection to the database.  Temporary tables can be useful as intermediate steps in complicated queries (as opposed to using subqueries).
{{< /hint >}}

{{< hint info >}}
`InnoDB` is almost always the best option for `ENGINE`, but there are certain situations where `MyISAM` may be preferable, and you may encounter it (and [its limitations](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html)) in legacy applications.
{{< /hint >}}

{{< hint info >}}
You may encounter the `NDB` engine in distributed database environments.
{{< /hint >}}

{{< hint info >}}
You usually never set the `AUTO_INCREMENT` value for a table, but you may encounter it when exporting/importing database tables.  It tells the next value that should be assigned to the `AUTO_INCREMENT` column when a new row is inserted.
{{< /hint >}}


#### Examples

{{< sqldiagram >}}
**A very simple table:**

```mysql
CREATE TABLE mytable ( x INT );
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A table with a little bit of everything:**

```mysql
CREATE TABLE person (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    fname VARCHAR(60) NOT NULL,
    lname VARCHAR(60) NOT NULL,
    hair_color ENUM('blonde', 'brown', 'red', 'black', 'gray') CHARACTER SET ascii,
    demerit_points TINYINT NOT NULL DEFAULT 0,
    parent INT,
    CONSTRAINT fk_person_parent FOREIGN KEY (parent) 
        REFERENCES person(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    INDEX idx_name (lname, fname)

)
ENGINE = InnoDB
CHARACTER SET utf8mb4
COLLATE utf8mb4_general_ci;
```
{{< /sqldiagram >}}

### Column Definition

{{< sqldiagram >}}
```mysql
<col_name> <<DataType>> [[NOT] NULL] [DEFAULT <default_value>] 
    [AUTO_INCREMENT] [ UNIQUE [KEY] | [PRIMARY] KEY ]
```
[<<DataType>>]({{< ref "#data-type" >}})
{{< /sqldiagram >}}

{{< hint info >}}
A column will be nullable unless otherwise specified
{{< /hint >}}

{{< hint info >}}
`AUTO_INCREMENT` may only be applied to integer columns such as `TINYINT`, `INT`, and `BIGINT`
{{< /hint >}}

{{< hint info >}}
You can **not** create composite keys using `UNIQUE KEY` or `PRIMARY KEY` in a column definition.  Use a constraint definition instead.
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**A simple nullable integer column:**

```mysql
id INT
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A surrogate key column:**

```mysql
id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A column with a unique constraint (no two rows may have the same value in this column):**

```mysql
code CHAR(4) UNIQUE
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A column with a default value:**

```mysql
-- Note the use of back ticks because 'date' is an SQL keyword
`date` DATETIME NOT NULL DEFAULT NOW()
```
{{< /sqldiagram >}}

### Constraint Definition

{{< sqldiagram >}}
```mysql
    [CONSTRAINT [<constraint_name>]] PRIMARY KEY (<col_name>,..)
  | [CONSTRAINT [<constraint_name>]] FOREIGN KEY (<col_name>,..) 
        REFERENCES <table_name>(<other_table_col_name>,…)
        [ON UPDATE CASCADE] [ON DELETE CASCADE]
  | {UNIQUE|[UNIQUE] INDEX|KEY} [<index_name>] (<col_name>,..) [ ASC | DESC ]
```
{{< /sqldiagram >}}

{{< hint info >}}
The `<other_table_col_name>` must be a column in the referenced table
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**A composite primary key:**

```mysql
-- The following three lines each achieve the same result
PRIMARY KEY(order_number, line_item_number)
CONSTRAINT PRIMARY KEY (order_number, line_item_number)
CONSTRAINT pk_order PRIMARY KEY (order_number, line_item_number) -- This line gives the primary key the name 'pk_order'
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A simple foreign key:**

```mysql
-- Note the use of back ticks because 'order' is an SQL keyword
FOREIGN KEY (order_number) REFERENCES `order`(id)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A named foreign key with referential integrity constraints:**

```mysql
CONSTRAINT fk_line_item_order FOREIGN KEY (order_number)
    REFERENCES `order`(id)
    ON UPDATE CASCADE
    ON DELETE CASCADE
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A simple index:**

```mysql
INDEX (code) 
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A composite unique index (no two rows may have the same `firstname` and `lastname` values)**

```mysql
UNIQUE INDEX (firstname, lastname)
```
{{< /sqldiagram >}}


### Data Type

{{< sqldiagram >}}
```mysql
    TINYINT[(<length>)] [UNSIGNED]
  | SMALLINT[(<length>)] [UNSIGNED]
  | MEDIUMINT[(<length>)] [UNSIGNED]
  | INT[EGER][(<length>)] [UNSIGNED]
  | BIGINT[(<length>)] [UNSIGNED]
  | REAL[(<length>,<decimals>)] [UNSIGNED] 
  | DOUBLE[(<length>,<decimals>)] [UNSIGNED] 
  | FLOAT[(<length>,<decimals>)] [UNSIGNED]
  | {DECIMAL|NUMERIC}[(<length>[,<decimals>])] [UNSIGNED]
  | DATE
  | TIME[(<second_decimals>)]
  | TIMESTAMP[(<second_decimals>)]
  | DATETIME[(<second_decimals>)]
  | YEAR
  | CHAR[(<length>)]
      [CHARACTER SET <charset_name>] [COLLATE <collation_name>]
  | VARCHAR(length)
      [CHARACTER SET <charset_name>] [COLLATE <collation_name>]
  | LONGTEXT
      [CHARACTER SET <charset_name>] [COLLATE <collation_name>]
  | ENUM(value,...)
      [CHARACTER SET <charset_name>] [COLLATE <collation_name>]
```
{{< /sqldiagram >}}

### Show

Generate the CREATE TABLE statement for an existing table:

{{< sqldiagram >}}
```mysql
SHOW CREATE TABLE <table_name>
```
{{< /sqldiagram >}}

## Create from Another Table

{{< sqldiagram >}}
```mysql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] <new_table_name> 
LIKE <existing_table_name>
```
{{< /sqldiagram >}}

{{< hint info >}}
Creates an **empty** copy of an existing table, preserving column definitions and indexes
{{< /hint >}}

{{< hint info >}}
Does **not** preserve foreign keys (although the indexes for any foreign keys in the original will be copied into the new table).
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**Create an employee table with the same schema as the person table:**

```mysql
CREATE TABLE employee LIKE person
```
{{< /sqldiagram >}}

## Create from Query Results

{{< sqldiagram >}}
```mysql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] <new_table_name> 
[AS] <<SelectStatement>>
```
[<<SelectStatement>>]({{< ref "select" >}})
{{< /sqldiagram >}}

{{< hint info >}}
**Only** the column names and datatypes of the select statement are preserved.  No constraints, keys, default values, etc. are added to the new table
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**Create a temporary table named 'names' that contains two columns, first_name and last_name, which are derived from the fname and lname columns of the person table:**

```mysql
CREATE TEMPORARY TABLE names 
SELECT fname AS first_name, lname AS last_name FROM person
```
{{< /sqldiagram >}}

## Drop

{{< sqldiagram >}}
```mysql
DROP [TEMPORARY] TABLE [IF EXISTS] <table_name>,..
```
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
**Drop the person table:**

```mysql
DROP TABLE person
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Drop a temporary names table, but only if it exists:**

```mysql
DROP TEMPORARY TABLE IF EXISTS names
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Drop multiple tables if they exist:**

```mysql
DROP TABLE IF EXISTS person, employee, student
```
{{< /sqldiagram >}}

## Truncate

{{< sqldiagram >}}
```mysql
TRUNCATE <table_name>
```
{{< /sqldiagram >}}

{{< hint warning >}}
**NOTE**: Truncating a table empties the table of data by dropping and re-creating the table.  This means that unlike `DELETE FROM <table_name>`, it has the following effects:
   - The operation causes an implicit transaction commit and can’t be rolled back
   - ON DELETE constraints are NOT triggered.  (That said, a TRUNCATE on a table that has foreign key references to it will fail.)
   - If the table has an AUTO_INCREMENT field, its value is reset to the default
{{< /hint >}}

## Alter

Most aspects of an existing table may be altered using the `ALTER` statement:

{{< sqldiagram >}}
```mysql
ALTER TABLE <table_name>
{
    ENGINE [=] {InnoDB|MyISAM}
  | AUTO_INCREMENT [=] <value>
  | CHARACTER SET [=] <charset_name>
  | COLLATE [=] <collation_name>
  | ADD [COLUMN] <<ColumnDefinition>> [FIRST | AFTER <col_name>]
  | ADD [COLUMN] (<<ColumnDefinition>>,..)
  | ADD <<ConstraintDefinition>>
  | ALTER [COLUMN] <col_name> {SET DEFAULT <value> | DROP DEFAULT}
  | CHANGE [COLUMN] <old_col_name> <<ColumnDefinition>> [FIRST | AFTER col_name]
  | MODIFY [COLUMN] <<ColumnDefinition>> [FIRST | AFTER <col_name>]
  | DROP [COLUMN] <col_name>
  | DROP PRIMARY KEY
  | DROP FOREIGN KEY <fk_name>
  | DROP {INDEX|KEY} <index_name>
  | RENAME [COLUMN] <old_col_name> TO <new_col_name>
  | RENAME {INDEX|KEY} <old_name> TO <new_name>
  | RENAME [TO|AS] <new_table_name>
},..
```
[<<ColumnDefinition>>]({{< ref "#column-definition" >}})

[<<ConstraintDefinition>>]({{< ref "#constraint-definition" >}})
{{< /sqldiagram >}}

### Changing a Column: Alter vs Modify vs Change

The `ALTER TABLE` command has a few different ways to modify a column, which can be confusing.

The most powerful command (which can be used to accomplish any of the other column-altering commands, albeit with a more wordy syntax) is the `CHANGE` command.  It can be used to rename a column, change its definition, and change its column order.  For example, the command below renames a person table’s birth_date column to ‘birthday’, re-defines its datatype and nullability, and moves it to be the first column of the table:

{{< sqldiagram >}}
```mysql
ALTER TABLE person CHANGE birth_date birthday DATETIME NULL FIRST
```
{{< /sqldiagram >}}

The `MODIFY` command is much like the `CHANGE` command but it cannot be used to rename the column.  Here, the birthday column’s data type and nullability are being modified, and it is moved to be after the ‘last_name’ column:

{{< sqldiagram >}}
```mysql
ALTER TABLE person MODIFY birthday DATE NOT NULL AFTER last_name
```
{{< /sqldiagram >}}

If you simply want to rename a column, use `RENAME` as in:

{{< sqldiagram >}}
```mysql
ALTER TABLE person RENAME birth_date TO birthday
```
{{< /sqldiagram >}}

{{< hint warning >}}
The `RENAME` syntax is not available in versions of MySQL older than 8.0
{{< /hint >}}

The `ALTER` clause allows you to change or remove the `DEFAULT` on a column.  The following example removes the default value from the person table’s birthday column:

{{< sqldiagram >}}
```mysql
ALTER TABLE person ALTER COLUMN birthday DROP DEFAULT
```
{{< /sqldiagram >}}
