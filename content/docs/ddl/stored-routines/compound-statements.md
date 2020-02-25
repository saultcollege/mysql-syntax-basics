---
title: "Compound Statements"
date: 2020-02-25 11:50:55
weight: 100
---

# Compound Statements

A Compound Statement consists of a set of SQL statements enclosed by `BEGIN` and `END`. Compound Statements may only be used inside stored routines (ie, store procedures, functions, or triggers).  In addition to the usual SQL statements that may be included anywhere in an SQL script, there are a number of constructs described below that may be used inside Compound Statements.  These additional constructs extend the capabilities of SQL into a fully-fledged programming language.

## Local Variables

{{< sqldiagram >}}
```mysql
DECLARE <var_name> <<DataType>> [DEFAULT <value>]
```
[DataType]({{< ref "tables#data-type" >}})
{{< /sqldiagram >}}

{{< hint info >}}
The scope of a local variable is the `BEGIN..END` block in which the variable was declared.
{{< /hint >}}

{{< hint info >}}
Unlike User Defined Variables, local variables do not start with a @
{{< /hint >}}

{{< hint info >}}
Local variable declarations must be the first statements inside the BEGIN … END block of a stored routine.
{{< /hint >}}

{{< hint warning >}}
A variable should not have the same name as a table column in the same scope
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
```mysql
DECLARE x INT;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
DECLARE hello VARCHAR(10) DEFAULT 'World!';
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Assigning values to local variables
```mysql
BEGIN
    DECLARE prod_id INT;
    DECLARE prod_name VARCHAR(20);
    SELECT id, name INTO prod_id, prod_name FROM product;
END
```
{{< /sqldiagram >}}

## Conditional Statements

{{< sqldiagram >}}
```mysql
IF <<ConditionExpression>> THEN 
    <<CompoundStatements>>;
[ELSEIF <<ConditionExpression>> THEN 
    <<CompoundStatements>>;]
[ELSE 
    <<CompoundStatements>>;]
END IF;
```
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
```mysql
IF ( msg = 'Hello' ) THEN RETURN 'WORLD';
END IF;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
If / else statement
```mysql
IF ( n < 0 ) THEN SET @msg := 'Negative';
ELSEIF ( n = 0 ) THEN SET @msg := 'Zero';
ELSE SET @msg := 'Positive';
END IF;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Nested if statement
```mysql
IF n = m THEN SET s := 'equals';
ELSE
    IF n > m THEN SET s = 'greater';
    ELSE SET s := 'less';
    END IF;

    SET s := CONCAT('is ', s, ' than');
END IF;
```
{{< /sqldiagram >}}


## Loop Statements

There are a few options for looping in SQL:

{{< sqldiagram >}}
```mysql
[label:] LOOP
    <<CompoundStatements>>
END LOOP;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
[label:] REPEAT
    <<CompoundStatements>>
UNTIL <<ConditionExpression>>
END REPEAT;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
[label:] WHILE <<ConditionExpression>> DO
    <<CompoundStatements>>
END WHILE;
```
{{< /sqldiagram >}}

{{< hint info >}}
You can exit a loop from within the body by using `LEAVE <label>` statement.  This is similar to the `break` statement in many programming languages
{{< /hint >}} 

{{< hint info >}}
You can cause the loop to go to the next iteration by using an `ITERATE <label>` statement.  This is similar to the `continue` statement in many programming languages.
{{< /hint >}}

{{< hint info >}}
Both the `LEAVE` and `ITERATE` statements require a `<label>` as part of the statement, which means that if you use either your loop needs to be labelled, 
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
```mysql
WHILE i < 10 DO
    SET i := i + 1
END WHILE;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
REPEAT
    SET i := i + 1
UNTIL i > 10
END REPEAT;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
this_loop: LOOP
    SET i := i + 1
    IF i > 10 THEN LEAVE this_loop;
    END IF;
END LOOP;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
```mysql
this_loop: WHILE i < 10 DO
    SET i := i + 1
    IF i > 5 THEN ITERATE this_loop;
    END IF;
    SET s := s + 1
END WHILE
```
{{< /sqldiagram >}}