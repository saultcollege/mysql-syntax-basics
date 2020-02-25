---
title: "Stored Procedures"
date: 2020-02-25 11:50:55
weight: 70
---

# Stored Procedures

## CREATE

{{< sqldiagram >}}
```mysql
CREATE PROCEDURE <procedure_name>(
        {[ IN|OUT|INOUT ] <param_name> <<DataType>>},.. 
    )
BEGIN
    <<CompoundStatements>>
END;
```
[DataType]({{< ref "tables#data-type" >}})

[CompoundStatements]({{< ref "compound-statements" >}})
{{< /sqldiagram >}}

{{< hint info >}}
Parameters are `IN` by default, meaning that any changes to the parameter in the procedure are not visible to the caller.  If a parameter is declared to be `OUT` or `INOUT` then changes to the parameter are visible to the caller.
An `OUT` parameter starts with the value `NULL` whereas an `INOUT` parameter starts with the value passed in by the caller.
{{< /hint >}}

### Delimiters

A `CREATE PROCEDURE` statement is interpreted by the SQL parser as a single statement, meaning that if your procedure body contains multiple statements ending in the standard end-of-statement delimiter `;` then it may be interpreted as the end of the `CREATE PROCEDURE` statement rather than the end of a statement inside the procedure body.  To get around this problem, it is sometimes necessary to change the end-of-statement delimiter to something other than `;` and then reset it after the procedure has been declared.

#### Example
{{< sqldiagram >}}
```mysql
DELIMITER ~  -- Change the delimiter to ~
CREATE PROCEDURE show_products()
BEGIN
    SELECT * FROM products; -- original delimiter is now ignored
END~       -- Use the new delimiter to mark end of statement
DELIMITER ; -- Reset the delimiter
```
{{< /sqldiagram >}}  


## CALL

{{< sqldiagram >}}
```mysql
CALL <procedure_name>({@arg1,..})
```
{{< /sqldiagram >}}

## DROP

{{< sqldiagram >}}
```mysql
DROP PROCEDURE [IF EXISTS] proc_name;
```
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
```mysql
CREATE PROCEDURE show_products()
BEGIN
    SELECT * FROM products;
END;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A procedure with parameters
```mysql
CREATE PROCEDURE add_product(
    new_name VARCHAR(100), 
    new_desc VARCHAR(1000)
)
BEGIN
    INSERT INTO products (name, description) VALUES (new_name, new_desc);
END;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A procedure with an OUT param
```mysql
CREATE PROCEDURE add_product(
    new_name VARCHAR(100), 
    new_desc VARCHAR(1000),
    OUT added_id INT
)
BEGIN
    INSERT INTO products (name, description) VALUES (new_name, new_desc);
    SET added_id := last_insert_id();
END;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Calling procedures
```mysql
CALL show_products();
CALL add_product('bike', 'A lovely two-wheeled toy');
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
After calling this procedure the @id variable will contain whatever value the procedure set it to (See the `CREATE PROCEDURE add_product` example above.)
```mysql
CALL add_product('ball', 'Very round', @id);
```
{{< /sqldiagram >}}


