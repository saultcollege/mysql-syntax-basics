---
title: "Operators"
weight: 40
date: 2019-12-04T11:15:23-05:00
type: docs
---

# Operators

## Common Operators

Operator | Description | Example
---------|-------------|---------
`=` | Equal to | `author = 'Alcott'`
`<>` | Not equal to | `dept <> 'Sales'`
`>` | Greater than | `date > '2012-01-31'`
`<` | Less than | `bonus < 50000.00`
`>=` | Greater than or equal | `dependents >= 2`
`<=` | Less than or equal | `rate <= 0.05`
`BETWEEN .. AND ..` | Between an inclusive range | `cost BETWEEN 1 AND 5`
`LIKE` | Begins with a character pattern | `full_name LIKE 'Will%'`
`LIKE` | Contains a character pattern | `full_name LIKE '%Will%'`
`LIKE` | Single character matching | `zip LIKE 'P0R 1__'`
`[NOT] IN` | Equal to one of multiple possible values | `deptcode IN (101, 103)`
`IS [NOT] NULL` | Compare to null (missing data) | `address IS NOT NULL`
`IS [NOT] {TRUE|FALSE}` | Boolean truth value test | `paid_vacation IS TRUE`
`AND` | Test that both conditions are true | `cost > 1 AND cost < 5`
`OR` | Test that at least one of two conditions is true | `cost < 5 OR cost > 9`
`[NOT] EXISTS` | Tests if a subquery yields any rows or not | `EXISTS (SELECT * FROM order)`

## Condition Expressions

A condition expression consists of an operator and its operand(s).  The `AND` and `OR` operators may use other condition expressions as their operands, and so they can be used to build more complex condition expressions.

{{< hint info >}}
**NOTE**: The `AND` operator has a higher precedence than `OR`, but parentheses can be used to manipulate the order of operations in a condition expression.  See the examples below.
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
**A simple condition expression:**

```mysql
price < 50
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A combined expression using `AND`:**

```mysql
price < 50 AND category = 'toy'
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A combined expression using `OR`:**

```mysql
category = 'model' OR category = 'toy'
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
**A combined expression using both `AND` and `OR`.**

```mysql
price < 50 AND category = 'model' OR category = 'toy'
```
{{< /sqldiagram >}}

### Operator precedence

**WARNING**: `AND` expressions will be evaluated **before** `OR` expressions.  The above query may not do what you expect.  Specifically, it does **not** select any products in the model or toy categories that are also less than $50.  Instead, it selects either any toy (at any price), or any model that is less than $50.  If you want the former you need to use parentheses to force the correct order of operations:

{{< sqldiagram >}}
```mysql
price < 50 AND (category = 'model' OR category = 'toy')
```
{{< /sqldiagram >}}

### A common mistake

{{< sqldiagram >}}
```mysql
-- Don't do this:
category = 'model' OR 'toy'

-- Do this instead:
category = 'model' OR category = 'toy'
```
{{< /sqldiagram >}}