---
title: "Basic Selects"
date: 2020-01-11 07:23:39
draft: false
weight: 10
---

# Basic Selects

{{< sqldiagram >}}
```mysql
SELECT [DISTINCT] {{ <column_name> | <<Expression>> } [AS <column_alias>]},..
[FROM <table_name>]
[WHERE <<ConditionExpression>>]
[ORDER BY {<column_name> [ASC|DESC]},.. ]
[LIMIT <row_count> [OFFSET <first_row>] ]
```
[<<Expression>>]({{< ref "#expressions" >}})

[<<ConditionExpression>>]({{< ref "operators#condition-expressions" >}})
{{< /sqldiagram >}}

## Columns in the Query Result

The set of columns in the query result will be the set specifiec in the `SELECT` clause.  Often this is just a comma-separated list of column names that must be available in the derived table of the `FROM` clause.  However, there are other options as well.

Each column may be aliased using an `AS <column_alias>` clause.

### The * Operator

The `*` operator may be used to select all columns in the derived table of the `FROM` clause

### Expressions 

An expression may be used to combine values from multiple columns into a single column value in the result.  This expression may be:

 - A literal value
 - An arithmetic expression involving any columns in the derived table of the `FROM` clause
 - Any combination of the [many functions](https://dev.mysql.com/doc/refman/8.0/en/functions.html) available in MySQL

#### Examples

Select all the columns of the person table:

{{< sqldiagram >}}
```mysql
SELECT * FROM person
```
{{< /sqldiagram >}}

Select a number of columns from a table:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name, phone FROM person
```
{{< /sqldiagram >}}

Select a number of columns from a table, but alias some columns:

{{< sqldiagram >}}
```mysql
SELECT 
    first_name AS First, 
    last_name AS Last, 
    phone 
FROM person
```
{{< /sqldiagram >}}

Just yield a plain string:

{{< sqldiagram >}}
```mysql
SELECT 'Hello, world!'
```
{{< /sqldiagram >}}

Yield a plain string, but alias the resultant column:

{{< sqldiagram >}}
```mysql
SELECT 'Hello, world!' AS message
```
{{< /sqldiagram >}}

Do a calculation in a column:

{{< sqldiagram >}}
```mysql
SELECT 
    first_name, 
    last_name, 
    salary / 12 AS monthly_salary 
FROM person
```
{{< /sqldiagram >}}

Refer to multiple columns and use MySQL functions:

{{< sqldiagram >}}
```mysql
SELECT CONCAT(first_name, ' ', last_name) AS Name, role FROM person
```
{{< /sqldiagram >}}

## Distinct

The `DISTINCT` keyword causes duplicate rows in the query result to be removed.

#### Examples

A list of distinct last names:

{{< sqldiagram >}}
```mysql
SELECT DISTINCT last_name FROM person;
```
{{< /sqldiagram >}}

A list of distinct names:

{{< sqldiagram >}}
```mysql
SELECT DISTINCT first_name, last_name FROM person;
```
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** the `DISTINCT` keyword must come immediately after the initial `SELECT` keyword, and it applies to **whole rows**, and not specific columns.  

The example above does **not** fetch a list of distinct first_names alongside last_names; rather, it fetches a list of distinct combinations of first_name and last_name.
{{< /hint >}}

## Filtering with WHERE

The `WHERE` clause can be used to filter the result set according to a [condition expression](operators#condition-expressions).  Only those rows which pass the condition expression will remain in the final result set of the query.

#### Examples

Select only people with the last name 'Dent':

{{< sqldiagram >}}
```mysql
SELECT first_name FROM person WHERE last_name = 'Dent'
```
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** as in the example above, the columns referred to in the `WHERE` clause **do not** need to be used in the `SELECT` clause, they just need to be available in the derived table of the `FROM` clause.
{{< /hint >}}

## Ordering

When ordering by multiple columns, results are sorted by the first column specified, and if there are duplicate values then the results are further sorted by the second column specified, and so on.

If `ASC` or `DESC` is not specified in the `ORDER BY` clause, `ASC` is assumed.

#### Examples

Order people by last name in ascending order:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name 
FROM person
ORDER BY last_name ASC; -- ASC here is optional as it is the default
```
{{< /sqldiagram >}}

Order people by last name in descending order:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name 
FROM person
ORDER BY last_name DESC; -- DESC here is necessary as it is NOT the default
```
{{< /sqldiagram >}}

Order people by last name, and if two people have the same last name order by first name:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name 
FROM person
ORDER BY last_name, first_name;
```
{{< /sqldiagram >}}

Order people by last name, then first name; if two people have the same name then order by phone number in descending order:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name 
FROM person
ORDER BY last_name, first_name, phone DESC;
```
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** the columns referred to in the `ORDER BY` clause **do not** have to be in the `SELECT` clause, but they **do** have to be available in the derived table of the `FROM` clause.
{{< /hint >}}


## Limiting

The `LIMIT` clause allows you to pick a specific number of rows, and the `OFFSET` clause allows you to choose where in the result set to begin keeping results.  

This can be especially useful for scenarios where you need to implement ‘paging’ in a report.  Ie, a report may only show 10 results at a time.  

{{< hint info >}}
**NOTE:** The offset of the first row in a result set is 0, so to get the nth row you need to set the offset to n-1
{{< /hint >}}

#### Examples

Show only 10 people:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name FROM person LIMIT 10
```
{{< /sqldiagram >}}

Show only 10 people, starting at the 5th 'page' of 10:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name FROM person LIMIT 10 OFFSET 40
```
{{< /sqldiagram >}}

The top 3 salary earners:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name, salary 
FROM person
ORDER BY 
```
{{< /sqldiagram >}}

{{< hint info >}}
The following examples combine some or all of the clauses we have discussed above
{{< /hint >}}

The top 3 salary earners in the manager role, sorted in descending order by salary:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name, salary
FROM person
WHERE role = 'manager'
ORDER BY salary
LIMIT 3
```
{{< /sqldiagram >}}

The 5th oldest sales person:

{{< sqldiagram >}}
```mysql
SELECT first_name, last_name
FROM person
WHERE role = 'sales'
ORDER BY birth_date ASC
LIMIT 1 OFFSET 4  -- NOTE: using 4 gives us the 5th result (offset 0 is the first result)
```
{{< /sqldiagram >}}