---
title: "User Defined Variables"
date: 2020-02-25 11:33:04
weight: 60
---

# User Defined Variables

{{< sqldiagram >}}
```mysql
SET {@var_name := expr},..
```
{{< /sqldiagram >}}

{{< hint info >}}
A variable name begins with ‘@’ followed by alphanumeric characters or ‘.’, ‘_’, or ‘$’
{{< /hint >}}

{{< hint info >}}
Variables can be used anywhere that data literals could be used in an SQL query.  They can **not** be used in place of identifiers such as column or table names.
{{< /hint >}}

{{< hint info >}}
If a query yields a single row, variables can also be declared and assigned inside of `SELECT` statements.  (See examples below.)
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
Single assignment
```mysql
SET @d := now();
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Multiple assignments in a single statement
```mysql
SET @i := 1, @x := 'foo';
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Using a variable in a statement
```mysql
SELECT @d;
SELECT CONCAT(@x, 'bar');
SELECT name FROM product WHERE product_id = @i;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Declaring variables within `SELECT`s
```mysql
-- NOTE: these only work because each query yields a single row
SELECT @m := MAX(price) FROM products;
SELECT @n := name FROM products WHERE product_id = @i;
SET @tax := 1.13
SELECT @total := price * @tax FROM products WHERE product_id = @i;
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Assigning variables using `SELECT..INTO`
```mysql
SELECT name INTO @n FROM products WHERE product_id = @i;
-- Here, @n will contain the name, and @q will contain the quantity
SELECT name, quantity INTO @n, @q FROM products WHERE product_id = @i;
```
{{< /sqldiagram >}}
