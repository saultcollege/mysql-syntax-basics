---
title: "Operators"
weight: 20
date: 2019-12-04T11:15:23-05:00
type: docs
---

# Operators

Operator | Description | Example
---------|-------------|---------
`=` | Equal to | `author = 'Alcott'`
`<>` | Not equal to | `dept <> 'Sales'`
`>` | Greater than | `date > '2012-01-31'`
`<` | Less than | `bonus < 50000.00`
`>=` | Greater than or equal | `dependents >= 2`
`<=` | Less than or equal | `rate <= 0.05`
`BETWEEN` | Between an inclusive range | `cost BETWEEN 1 AND 5`
`LIKE` | Begins with a character pattern | `full_name LIKE 'Will%'`
`LIKE` | Contains a character pattern | `full_name LIKE '%Will%'`
`LIKE` | Single character matching | `zip LIKE 'P0R 1__'`
`[NOT] IN` | Equal to one of multiple possible values | `deptcode IN (101, 103)`
`IS [NOT] NULL` | Compare to null (missing data) | `address IS NOT NULL`
`IS [NOT] {TRUE|FALSE}` | Boolean truth value test | `paid_vacation IS TRUE`
`AS` | Used to alias a column or table name | `SELECT MAX(*) AS biggest`
