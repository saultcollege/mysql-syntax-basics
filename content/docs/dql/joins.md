---
title: "Joins"
date: 2020-02-17 20:28:44
weight: 40
---

# Joins

See [Appendix C: Joins]({{< ref "c_joins" >}}) for general information about joins.

## Cross Joins

{{< sqldiagram >}}
```mysql
FROM <table1_name>
    CROSS JOIN <table2_name>
```
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
A list of all possible film genres and ratings
```mysql
SELECT genre.name, rating.name
FROM genre CROSS JOIN rating
```
{{< /sqldiagram >}}

{{< hint warning >}}
You may encounter the following syntax for a cross join, which is equivalent in function to the syntax shown above, but is **not recommended** for general use as the syntax is not as clear as the explicit syntax above:

{{< sqldiagram >}}
```mysql
SELECT genre.name, rating.name 
FROM genre, rating
```
{{< /sqldiagram >}}
    
{{< /hint >}}

## Inner Joins

{{< sqldiagram >}}
```mysql
FROM <table1_name> [AS <alias1>]
    [INNER] JOIN <table2_name> [AS <alias2>] 
    [
        ON <<ConditionExpression>>
      | USING (<column_name>,..)
    ]
```
[ConditionExpression]({{< ref "operators#condition-expression" >}})
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
A list of customer names alongside their sales reps’ names
```mysql
SELECT salesperson.name, customer.name
FROM salesperson 
    JOIN customer ON customer.salesperson_id = salesperson.id
```
{{< /sqldiagram >}}

## Left Joins

{{< sqldiagram >}}
```mysql
FROM <table1_name> [AS <alias1>]
    LEFT [OUTER] JOIN <table2_name> [AS <alias2>] 
    [
        ON <<ConditionExpression>>
      | USING (<column_name,..)
    ]
```
[ConditionExpression]({{< ref "operators#condition-expression" >}})
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
A list of all salespeople alongside their customers’ names
```mysql
SELECT salesperson.name, customer.name
FROM salesperson
    LEFT JOIN customer USING (salesperson_id)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A list of all salespeople who have no customers assigned.

Note the check for `NULL` on a customer column to determine that a salesperson has no customers
```mysql
SELECT salesperson.name, customer.name
FROM salesperson
    LEFT JOIN customer USING (salesperson_id)
WHERE customer.name = NULL
```
{{< /sqldiagram >}}

## Multi Joins

There is no limit (aside from computer hardware limits) to the number tables you may join together in a single `FROM` clause, and you may combine different types of joins in a single `FROM` clause

#### Examples

{{< sqldiagram >}}
A list of related manager, salesperson, and customer names
```mysql
SELECT manager.name, salesperson.name, customer.name
FROM manager
    JOIN salesperson USING (manager_id)
        JOIN customer USING (salesperson_id)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A list of product names alongside their manufacturers’ names and any orders of that product (if any)
```mysql
SELECT product.name, manufacturer.name
FROM product 
JOIN manufacturer USING (manufacturer_id)
    LEFT JOIN order USING (product_id)
```
{{< /sqldiagram >}}

## Self Joins

In some situations such as in the case of a recursive relationship it is necessary to join a table to itself.

Suppose you have a person table that stores family tree relationships so that each `person` row has a `mother_id` and a `father_id` column each of which point to another row in the same table.  If you wanted to create a report that shows each person's parents you could do something like the query below:

{{< sqldiagram >}}
```mysql
SELECT child.name, mother.name, father.name
FROM person AS child
    -- NOTE: we are joining the same table twice with different aliases
    LEFT JOIN person AS mother ON mother.id = child.mother_id
    LEFT JOIN person AS father ON father.id = child.father_id;
```
{{< /sqldiagram >}}

{{< hint info >}}
Note the use of table aliases.  It is necessary to alias the table in a self join with unique names for each join so that the individual joins can be distinguished semantically.
{{< /hint >}}