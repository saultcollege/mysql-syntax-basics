---
title: "Set Operations"
date: 2020-02-19 13:49:29
weight: 50
---

# Set Operations

## Union

{{< sqldiagram >}}
```mysql
<<SelectStatement1>>
UNION
<<SelectStatement2>>
```
{{< /sqldiagram >}}

The results of two separate queries may be merged into a single dataset using the `UNION` operator.  Rows that exist in both original result sets of the union will only exist once in the final result set

### Union All

If you **do** wish duplicate rows to remain in the final result set of a union, the `UNION ALL` operator may be used instead:

{{< sqldiagram >}}
```mysql
<<SelectStatement1>>
UNION ALL
<<SelectStatement2>>
```
{{< /sqldiagram >}}

## Intersect

In the SQL standard, there is an `INTERSECT` operator that can be used to select any rows that exist in **both** result sets of two separate queries, as in

{{< sqldiagram >}}
```mysql
<<SelectStatement1>>
INTERSECT
<<SelectStatement2>>
```
{{< /sqldiagram >}}

{{< hint warning >}}
MySQL does **not** support the `INTERSECT` operator; however, an intersection can be simulated by using one of the following two templates:

{{< sqldiagram >}}
```mysql
SELECT DISTINCT s1.*
FROM (<<SelectStatement1>>) AS s1
    -- Assuming 'id' is the primary key column for both SelectStatement 1 & 2
    JOIN (<<SelectStatement2>>) AS s2 USING (id)
```
{{< /sqldiagram >}}

or

{{< sqldiagram >}}
```mysql
SELECT DISTINCT s1.*
FROM (<<SelectStatement1>>) s1
    -- Assuming 'id' is the primary key column for both SelectStatement 1 & 2
    WHERE id IN (SELECT id FROM (<<SelectStatement1>>) AS s2)
```
{{< /sqldiagram >}}
{{< /hint >}}


## Minus

In the SQL standard, there is a `MINUS` operator that can be used to remove any rows from one result set that exist in a second result set, as in

{{< sqldiagram >}}
```mysql
<<SelectStatement1>>
MINUS
<<SelectStatement2>>
```
{{< /sqldiagram >}}

{{< hint warning >}}
MySQL does **not** support the `MINUS` operator; however, it can be simulated by using the following template:

{{< sqldiagram >}}
```mysql
SELECT s1.*
FROM (<<SelectStatement1>>) AS s1
    -- Assuming 'id' is the primary key column for both SelectStatement 1 & 2
    LEFT JOIN (<<SelectStatement2>>) AS s2 USING (id)
WHERE s2.id IS NULL
```
{{< /sqldiagram >}}

{{< /hint >}}
