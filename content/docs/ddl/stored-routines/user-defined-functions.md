---
title: "User Defined Functions"
date: 2020-02-25 15:12:44
weight: 20
---

# User Defined Functions (UDFs)

Think of UDFs as a way to create new functions in addition to those provided by MySQL such as `CONCAT`, `RANDOM`, `DATEDIFF`, etc.  UDFs can be used anywhere that the built-in MySQL functions can be used.

## CREATE

{{< sqldiagram >}}
```mysql
CREATE [OR REPLACE] FUNCTION <function_namne>( [ {<param_name> <<DataType>>},.. ] )
    RETURNS <<DataType>>
BEGIN
    <<CompoundStatements>>
END
```
[DataType]({{< ref "tables#data-type" >}})

[CompoundStatements]({{< ref "compound-statements" >}})
{{< /sqldiagram >}}

{{< hint info >}}
A user defined function must have a `RETURN` statement that returns a value of the **same data type** as specified in the function’s `RETURNS` declaration
{{< /hint >}}

{{< hint info >}}
Unlike stored procedures, UDF parameters are always `IN` parameters
{{< /hint >}} 

{{< hint warning >}}
UDFs may **not** contain transactions or statements that return a result set (eg. `SELECT` statements other than `SELECT..INTO`).

UDFs may **not** be recursive

UDFs **may** contain compound statements as long as they satisfy the above restrictions
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
Creating a UDF
```mysql
DELIMITER ~
CREATE FUNCTION hello() RETURNS VARCHAR(50)
BEGIN
    RETURN 'Hello, World!';
END~
DELIMER ;

SELECT hello();  -- Calling the UDF
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A UDF that uses other functions
```mysql
DELIMITER ~
CREATE FUNCTION full_name(fname VARCHAR(50), lname VARCHAR(50)) 
RETURNS VARCHAR(500)
BEGIN
    RETURN CONCAT(fname, ' ', lname);
END~
DELIMITER ;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A UDF that uses compound statements
```mysql
DELIMITER ~
CREATE FUNCTION is_answer_to_life(answer VARCHAR(50)) RETURNS BOOLEAN
BEGIN
    IF answer = '42' THEN RETURN TRUE;
    ELSE RETURN FALSE;
END~
DELIMITER;

-- An example of calling the above UDF
-- This statement yields only those products with a price of $42
SELECT * FROM product WHERE is_answer_to_life(price);
```
{{< /sqldiagram >}}


## DROP
{{< sqldiagram >}}
```mysql
DROP PROCEDURE [IF EXISTS] <function_name>;
```
{{< /sqldiagram >}}

