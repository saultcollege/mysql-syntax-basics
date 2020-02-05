---
title: "Subqueries"
date: 2020-02-05 10:08:52
weight: 30
---

# Subqueries

Since the results of a query are simply a set of rows and columns that can be treated as a table, it is possible (and often very useful) to embed one query within another when the results of one query are required as the basis for some part of another query.

## Subqueries in a WHERE or HAVING clause...

### ...as a value operand (=, <, >, etc)

If a subquery returns a **single row and column** it may be used as an argument in any expression that could accept a value literal as in the examples below:

{{< sqldiagram >}}
The name of the person with the highest salary
```mysql
SELECT first_name, last_name FROM person
WHERE person.salary = (
    -- Get the highest salary
    SELECT MAX(salary) FROM person
)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
All products with price higher than the average product price
```mysql
SELECT product_code, price 
FROM product
WHERE price > ( 
    -- Get the average price
    SELECT AVG(price) FROM product 
)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
All products with price in the same range as products in the  toy category
```mysql
SELECT * FROM product
WHERE price BETWEEN ( 
        -- Get the lowest toy price
        SELECT MIN(price) FROM product 
        WHERE product_category = 'toy'
    ) AND (
        -- Get the highest toy price
        SELECT MAX(price) FROM product
        WHERE product_category = 'toy'
    )
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
All product categories for which the lowest priced product in the category has a price higher than the average product  price
```mysql
SELECT product_category FROM product
GROUP BY product_category
HAVING MIN(price) > ( 
    -- Get the average product price
    SELECT AVG(price) FROM product 
)
```
{{< /sqldiagram >}}

### ...as a list operand (IN, NOT IN)

If a subquery returns a **single column** (with any number of rows) it may be used as the list in an `IN` or `NOT IN` expression as in the examples below:

{{< sqldiagram >}}
All products made by Canadian manufacturers
```mysql
SELECT * FROM product
WHERE product.manufacturer_name IN ( 
    SELECT manufacturer_name FROM manufacturers WHERE country = 'CA'
)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
All customers that have never placed an order
```mysql
SELECT * FROM customer
WHERE customer.id NOT IN (
    SELECT DISTINCT customer_id FROM `order`
)
```
{{< /sqldiagram >}}

### ...as a table operand (EXISTS, NOT EXISTS)

If a subquery may return **multiple rows and columns** then it may be used as the operand in an `EXISTS` or `NOT EXISTS` expression.  For example:

{{< sqldiagram >}}
All products that have never been part of an order
```mysql
SELECT * FROM product
WHERE NOT EXISTS (
    SELECT * FROM `order` WHERE `order`.product_id = product.id
)
```
{{< /sqldiagram >}}

## Correlated Subqueries

If you observe the above `EXISTS` example carefully, you will notice that the subquery refers to a column from the outer query (`product.id`).  Any subquery that refers to a column in an outer query is called a correlated subquery.

{{< hint warning >}}
**WARNING:** Care must be taken when using correlated subqueries because the subquery must be rerun for each row in the outer query, which can be very costly if the outer query has many rows.
{{< /hint >}}


## Subqueries in a FROM clause

A subquery may be used as the 'table' in a `FROM` clause as in the example below

{{< sqldiagram >}}
Average of monthly order totals
```mysql
SELECT AVG(monthly_totals.total)
FROM (
    -- Get monthly order totals
    SELECT MONTH(date) AS m, SUM(total) AS total
    FROM order
) AS monthly_totals  -- NOTE: subquery alias is necessary
```
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** Subqueries in a `FROM` clause **must** be given an alias
{{< /hint >}}

## Nested subqueries

Subqueries may be nested such that a subquery makes use of another subquery, and so on to any depth (within the limits of the host computer’s memory).

#### Examples

{{< sqldiagram >}}
Average of monthly order totals for procucts in the toy category

```mysql
SELECT AVG(monthly_totals.total)
FROM (
    -- Get monthly order totals
    SELECT MONTH(date) AS m, SUM(total) AS total
    FROM `order`
    -- Consider ONLY toy products
    WHERE `order`.product_id IN (
        SELECT id FROM product WHERE product_category = 'toy'
    )
) AS monthly_totals
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Names of manufacturers of products that have a price greater than the average product price
```mysql
SELECT manufacturer.name 
FROM manufacturers
WHERE manufacturer.id IN (
    -- Get manufacturer ids that produce ‘expensive’ products
    SELECT product.manufacturer_id 
    FROM product
    WHERE product.price > ( 
        -- Get average product price
        SELECT AVG(product.price) FROM product 
    )
)
```
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** The above query could also be done using a `JOIN` as shown below.  It is generally the case that `JOIN` queries run faster than equivalent queries using subqueries, but profiling should be done to determine the most efficient approach.

{{< sqldiagram >}}
```mysql
SELECT manufacturer.name
FROM product
    JOIN manufacturer ON manufacturer.id = product.manufacturer_id
WHERE product.price > (
    SELECT AVG(product.price) FROM product
)
```
{{< /sqldiagram >}}
{{< /hint >}}

## Subqueries in a SELECT clause

Subqueries may be used in the `SELECT` clause.

{{< hint warning >}}
**WARNING:** This kind of subquery is usually a [correlated subquery]({{< ref "#correlated-subqueries" >}}), which can make it computationally expensive, and should be avoided.  It is often possible to write a query that uses subqueries in the SELECT clause in such a way that it does not use subqueries in the SELECT clause.

#### Example

{{< sqldiagram >}}
Customer Ids alongside the number of orders made by that customer
```mysql
SELECT 
    id, 
    -- BAD: this subquery must run for each row in the out query result set
    ( SELECT COUNT(*) FROM `order` WHERE customer.id = `order`.customer_id ) AS n
FROM customer
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A better solution to the above query:
```mysql
SELECT customer_id, COUNT(*) AS n
FROM `order`
GROUP BY customer_id
```
{{< /sqldiagram >}}

{{< /hint >}}