---
title: "Aggregates and GROUP BY"
date: 2020-01-17 14:13:14
draft: false
weight: 20
---

# Aggregates and GROUP BY

{{< sqldiagram >}}
```mysql
SELECT [DISTINCT] {{ <column_name> | <<Expression>> } [AS <column_alias>]},..
[FROM <table_name>]
[WHERE <<ConditionExpression>>]
[GROUP BY <column_name>,..]
[HAVING <<ConditionExpression>>]
[ORDER BY {<column_name> [ASC|DESC]},.. ]
[LIMIT <row_count> [OFFSET <first_row>] ]
```
[<<Expression>>]({{< ref "#expressions" >}})

[<<ConditionExpression>>]({{< ref "operators#condition-expressions" >}})
{{< /sqldiagram >}}

## Aggregate Functions

Aggregate functions, which can be used in the context of a `SELECT` clause column expression, have the effect of reducing a set of rows down to a single row and 'aggregating' the value of some column(s) in those rows down to one value.  Below is a table of some of the most commonly used aggregate functions:

#### Useful Aggregate Functions

| Syntax | Description |
|------|-------------|
| `COUNT( {[DISTINCT] <column_name>} | *)` | Count the number of rows.  If the `DISTINCT` option is used, the number of distinct values in the specified column are countedinstead. |
| `MIN(<column_name>)` | Calculate the minimum value |
| `MAX(<column_name>)` | Calculate the maximum value |
| `SUM(<column_name>)` | Calculate the sum of the values |
| `AVG(<column_name>)` | Calculate the average value |
| `GROUP_CONCAT([DISTINCT] <column_name> [ORDER BY <column_name> [ASC|DESC]] [SEPARATOR <string>])` | Concatenate all the values with the string specified in `<string>` (default is `,`).  The values may be ordered using the `ORDER BY` clause |

#### Examples

{{< sqldiagram >}}
**Count the number of people:**

```mysql
SELECT COUNT(*) FROM people
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Calculate the average, minimum, and maximum salary:**

```mysql
SELECT AVG(salary), MIN(salary), MAX(salaray) FROM person
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Calculate the difference between the maximum and minimum salaries:**

```mysql
SELECT MAX(salary) - MIN(salary) AS salary_range FROM person
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Show student ids alongside a comma-separated list of each of their test scores**
```mysql
SELECT 
    student_id,
    GROUP_CONCAT(test_score ORDER BY test_score DESC SEPARATOR ', ') AS scores
FROM student_test_scores
GROUP BY student_id;
```
{{< /sqldiagram >}}

## GROUP BY and Aggregate Functions

Adding a `GROUP BY` clause to a basic `SELECT` statement causes the result set rows to be "grouped" into rows that all have the same value for the columns specified in the `GROUP BY` clause.  Each group is reduced to a single row, but aggregate functions may be used to perform calculations over the set of rows that belong to each group.

{{< hint warning >}}
**WARNING**: In a `SELECT` clause, it is usually incorrect to mix expressions using aggregate functions with expressions using no aggregate functions unless the latter use **only** columns that are *functionally dependent* on the columns specified in the `GROUP BY` clause.
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**Count the number of people in each role:**

```mysql
SELECT role, COUNT(*) 
FROM people
GROUP BY role
-- Yields one row per distinct role
-- The value of 'n' for each row will be the number of people in that role.
```
{{< /sqldiagram >}}


{{< sqldiagram >}}
**Count the number of distinct first names in each role:**

```mysql
SELECT role, COUNT(DISTINCT first_name) AS n
FROM person
GROUP BY role
-- Yields one row per distinct role
-- The value of 'n' for each row will be the number of distinct first_names in that role.
```
{{< /sqldiagram >}}

{{< hint warning >}}
It is tempting to think that the query below yields the name of the person with the highest salary, **but it (probably) does not**.  Instead, it yields the first_name and last_name of the **first** person in the table alongside the maximum salary.

{{< sqldiagram >}}
```mysql
-- THIS PROBABLY DOESN'T DO WHAT YOU THINK
SELECT first_name, last_name, MAX(salary) FROM person
```
{{< /sqldiagram >}}

{{< /hint >}}

{{< sqldiagram >}}
**List the highest salary per role**
```mysql
SELECT role, MAX(salary)
FROM person
GROUP BY role
```
{{< /sqldiagram >}}

{{< hint warning >}}
Since first_name and last_name are **not** functionally dependent on role, the first_name and last_name columns in the query below are not meaningful.  (They are really just the value of first_name and last_name in the first row of each group.)
{{< sqldiagram >}}
```mysql
-- THIS PROBABLY DOESN'T DO WHAT YOU THINK
SELECT first_name, last_name, role, MAX(salary)
FROM person
GROUP BY role
```
{{< /sqldiagram >}}

{{< /hint >}}

{{< sqldiagram >}}
**Show the highest earner broken down by both region and role:**
```mysql
SELECT region, role, MAX(salary)
FROM person
GROUP BY region, role
```
{{< /sqldiagram >}}

## Filtering groups with HAVING

A `HAVING` clause is much like `WHERE` clause in that it can be used to filter the final result set according to a [condition expression]({{< ref "operators#condition-expressions" >}}).  Any rows that pass the condition expression will remain in the final result set of the query.

A `HAVING` clause filters the rows **after** the `GROUP BY` groups have been calculated, but a `WHERE` clause filters the rows **before** the groups have been calculated.

Stated another way, in a `WHERE` clause you specify which rows from the derived table of the `FROM` clause to keep, but in a `HAVING` clause you specify which group rows to keep.

{{< hint info >}}
Thus, a `WHERE` clause can never use an aggregate function, but a `HAVING` clause may (and often does)
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**Show the highest earner per role, but only for roles that have an average salary over 50k**
```mysql
SELECT role, MAX(salary) 
FROM person
GROUP BY role
HAVING AVG(salary) > 50000
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**The average salary of baby boomers per role, but only for roles where the lowest salary is 100k**
```mysql
SELECT role, AVG(salary)
FROM person
WHERE YEAR(birth_date) < 1965
GROUP BY role
HAVING MIN(salary) > 100000
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**Same as above, but only shows the one role with the highest average**
```mysql
SELECT role, AVG(salary)
FROM person
WHERE YEAR(birth_date) < 1965
GROUP BY role
HAVING MIN(salary) > 100000
ORDER BY AVG(salary)
LIMIT 1
```
{{< /sqldiagram >}}