---
title: "Triggers"
date: 2020-02-25 15:22:10
weight: 30
---

# Triggers

## CREATE

{{< sqldiagram >}}
```mysql
CREATE [OR REPLACE] TRIGGER <trigger_name> {BEFORE|AFTER} {INSERT|UPDATE|DELETE}
    ON <table_name> FOR EACH ROW
    [ {FOLLOWS|PRECEDES} <other_trigger_name> ]
BEGIN
    <<CompoundStatements>>
END
```
[CompoundStatements]({{< ref "compound-statements" >}})
{{< /sqldiagram >}}

{{< hint info >}}
A trigger cannot be called directly like a stored procedure or UDF.  Instead, a trigger is associated with a table event, one of `INSERT`, `UPDATE`, or `DELETE`.  When one of those events occurs, the SQL statements in the trigger body are executed either **before** or **after** the event (as specified in the `CREATE TRIGGER` statement) **for each row** effected by the query.
{{< /hint >}}

{{< hint info >}}
While triggers do not have parameters, they do have the aliases `OLD` and `NEW` for the table as it was before (`OLD`) or after (`NEW`) the event.  `OLD.<column_name>` can be used to refer to the value of a column as it was before an update or delete; `NEW.<column_name>` refers to the new value of a column after it has been inserted or updated.
{{< /hint >}}

{{< hint info >}}
If multiple triggers have been created on one table, the order in which the triggers fire can be controlled via the `FOLLOWS` or `PRECEDES` clause
{{< /hint >}}

{{< hint warning >}}
Triggers do **not** work on views or temporary tables

Triggers may **not** use a `RETURN` statement

Triggers may **not** contain transactions or statements that return a result set (eg. `SELECT` statements other than `SELECT..INTO`)

Triggers **may** contain compound statements as long as they satisfy the above restrictions
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
```mysql
CREATE TRIGGER check_positive_quantity BEFORE UPDATE
    ON products FOR EACH ROW
BEGIN
    IF NEW.quantity < 0 THEN
        SIGNAL SQLSTATE '45000' 
            SET MESSAGE_TEXT 'Product quantity must be positive';
    END IF;
END;
```
{{< /sqldiagram >}}

## DROP
{{< sqldiagram >}}
```mysql
DROP TRIGGER [IF EXISTS] <trigger_name>;
```
{{< /sqldiagram >}}
