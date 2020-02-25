---
title: "Compound Statements"
date: 2020-02-25 11:50:55
weight: 1000
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
[ConditionExpression]({{< ref "operators#condition-expressions" >}})

[CompoundStatements]({{< ref "compound-statements" >}})
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
[<label>:] LOOP
    <<CompoundStatements>>
END LOOP;

-- OR
[<label>:] REPEAT
    <<CompoundStatements>>
UNTIL <<ConditionExpression>>
END REPEAT;

-- OR
[<label>:] WHILE <<ConditionExpression>> DO
    <<CompoundStatements>>
END WHILE;
```
[ConditionExpression]({{< ref "operators#condition-expressions" >}})

[CompoundStatements]({{< ref "compound-statements" >}})
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
A simple while loop
```mysql
WHILE i < 10 DO
    SET i := i + 1
END WHILE;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A simple repeat-until
```mysql
REPEAT
    SET i := i + 1
UNTIL i > 10
END REPEAT;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Using labels and the `LEAVE` keyword to exit a loop
```mysql
this_loop: LOOP
    SET i := i + 1
    IF i > 10 THEN LEAVE this_loop;
    END IF;
END LOOP;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Using labels and the `ITERATE` keyword to continue a loop
```mysql
this_loop: WHILE i < 10 DO
    SET i := i + 1
    IF i > 5 THEN ITERATE this_loop;
    END IF;
    SET s := s + 1
END WHILE
```
{{< /sqldiagram >}}

## Error Signaling

{{< sqldiagram >}}
```mysql
SIGNAL SQLSTATE <value> SET MESSAGE_TEXT = <message>;
```
{{< /sqldiagram >}}

{{< hint info >}}
Often, the `SQLSTATE` value is '45000', which means ‘unhandled user-defined exception’
{{< /hint >}}

{{< hint info >}}
The message text should be a description of the error
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
```mysql
SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Bazinga!';
```
{{< /sqldiagram >}}

##  Error Handling

By default, SQL errors cause a stored routine to exit at the point of the error, but this behaviour may be overridden via the following handler syntax:

{{< sqldiagram >}}
```mysql
DECLARE {CONTINUE|EXIT} HANDLER 
FOR {<err_no>|SQLSTATE <state_code>|SQLEXCEPTION} 
    <<CompoundStatements>>
```
[CompoundStatements]({{< ref "compound-statements" >}})
{{< /sqldiagram >}} 

{{< hint info >}}
A handler may be specified as a `CONTINUE` or `EXIT` handler.  A `CONTINUE` handler will allow the routine to continue after the handler’s `<<CompoundStatements>>` has executed, but an `EXIT` handler will exit the routine.
{{< /hint >}}

{{< hint info >}}
`<err_no>` is an `INT`, one of the pre-defined [SQL error numbers](https://dev.mysql.com/doc/refman/8.0/en/server-error-reference.html)
{{< /hint >}}

{{< hint info >}}
`<state_code>` is a **string**, one of the pre-defined [SQL state codes](https://dev.mysql.com/doc/refman/8.0/en/error-message-components.html)
{{< /hint >}}

{{< hint info >}}
The `SQLEXCEPTION` condition is a catch-all that will result in the handler handling **all** SQL errors
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
Yield the message ‘Something happened’ on any error (This is obviously not very useful)
```mysql
DECLARE EXIT HANDLER FOR SQLEXCEPTION SELECT 'Something happened'
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Rollback any open transactions and display a message on error
```mysql
DECLARE EXIT HANDLER FOR SQLEXCEPTION
BEGIN
    ROLLBACK;
    SELECT 'Operation rolled back after error';
END;
```
{{< /sqldiagram >}}