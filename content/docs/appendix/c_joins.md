---
title: "Appendix C: Joins"
date: 2020-02-18 10:26:02
weight: 30
---

# Appendix C: Joins

The `FROM` clause of an SQL query may refer simply to a table as in

{{< sqldiagram >}}
```mysql
SELECT * FROM t1;
```
{{< /sqldiagram >}}

We have also seen that you may use the aliased results of a subquery as the ‘table’ for a `FROM` clause:

{{< sqldiagram >}}
```mysql
SELECT * FROM ( <<SelectStatement>> ) AS alias
```
[SelectStatemnet]({{< ref "select" >}})
{{< /sqldiagram >}}

In both of the above cases, you can think of the `FROM` clause as operating on a single table-like dataset.  We call this dataset a **derived table**.

There is another option: `JOIN` clauses.  As in the above two types of `FROM` clauses the final data used by the `FROM` clause is simply a single table-like dataset of rows and columns.

In the case of joins, information from two tables is brought together and the derived table will contain **all columns** from both tables involved in the join.  The set of rows, however, depends on the type of join.

There are three general kinds of joins: `CROSS`, `INNER`, and `OUTER`.

## Cross Joins

Suppose there are two tables with one column named ‘value’ and three rows each.  T1 has values ‘A’, ‘B’, and ‘C’, and T2 has value ‘B’, ‘C’, ‘D’.  In a cross join, the derived table is simply each row from the first table paired with each row from the second table.  So the join in

{{< sqldiagram >}}
```mysql
SELECT * FROM T1 CROSS JOIN T2
```
{{< /sqldiagram >}}

yields a derived table containing all columns from both tables (`T1.val` and `T2.val`) and each row from T1 paired with each row from T2:

| T1.val |    T2.val |
|--------|-----------|
| A |        B |
| B |        B |
| C |        B |
| A |        C |
| B |        C |
| C |        C |
| A |        D |
| B |        D |
| C |        D |

{{< hint info >}}
The number of rows in the derived table of a CROSS JOIN is the number of rows in the one table multiplied by the number of rows in the other.
{{< /hint >}}

{{< hint info >}}
Cross joins are only useful in very specific circumstances, but they are helpful in understanding inner and outer joins
{{< /hint >}}

{{< hint info >}}
Cross joins are sometimes referred to a ‘cartesian products’
{{< /hint >}}

## Inner Joins

You can think of an inner join as a **filtered cross join**.  A condition or 'join predicate', which filters rows much like a `WHERE` clause, determines which rows from the cross join remain in the derived table.  (A join with no predicate is simply a cross join.) So, the join in

{{< sqldiagram >}}
```mysql
SELECT * FROM T1 JOIN T2 ON T1.val = T2.val
```
{{< /sqldiagram >}}

yields a derived table containing all columns from both tables again, but this time only those rows for which the join predicate (`T1.val = T2.val`) is true:

| T1.val  |  T2.val |
|---------|--------|
| B |        B |
| C |        C |

In most (but not all!) cases the join predicate involves matching the value of a foreign key column with the value of the column in the referred-to table.  For example, suppose you had two tables, ‘bank’ and ‘account’ with data like the following:

Bank:               

| bank_id | name |  
|---------|------|  
| 1       |  BMO |  
| 2       |  TD  |  
                    
Account:

| id |     bank_id |     balance |
|----|-------------|-------------|
| 1    | 1       |  1000.00 |
| 2    | 2       |  2301.09 |
| 3    | 2       |  9212.92 |
| 4    | 1       |  12.22 |
                    

A typical inner join using these two tables would be required if, for example, you wanted to display a bank’s name alongside an account’s balance.  The information required is in two separate tables, so the data must be joined.  If we simply did a cross join we would get:

{{< sqldiagram >}}
```mysql
SELECT * FROM bank CROSS JOIN account
```
{{< /sqldiagram >}}


| bank_id |   name    |    id |    bank_id |    balance |
|---------|-----------|-------|------------|------------|
| 1       |  BMO      |    1  |  1         | 1000.00 |
| 1       |  BMO      |    2  |  2         | 2301.09 |
| 1       |  BMO      |    3  |  2         | 9219.92 |
| 1       |  BMO      |    4  |  1         | 12.22 |
| 2       |  TD       |    1  |  1         | 1000.00 |
| 2 |  TD       |    2  | 2    | 2301.09 |
| 2       |  TD       |    3  |  2         | 9219.92 |
| 2       |  TD       |    4  |  1         | 12.22 |

Many of the rows in the cross join are irrelevant because they pair information about a bank with information about an account that does not belong to that bank.  However, some rows do have bank information paired with information for an account from that bank, and if you look closely you will see that it is precisely those rows for which the `bank_id` on the bank side of the join is the same as the `bank_id` on the account side of the join—this is the condition on which a join predicate can be helpful.  For example,

{{< sqldiagram >}}
```mysql
SELECT * FROM bank JOIN account ON bank.bank_id = account.bank_id
```
{{< /sqldiagram >}}


yields **only** the rows where the `bank_id`s are equal, so that for each account the bank info in that row corresponds to that account’s bank:

| bank_id |   name    |    id |    bank_id |    balance |
|---------|-----------|-------|------------|------------|
| 1       |  BMO      |    1  |  1         | 1000.00 |
| 1       |  BMO      |    4  |  1         | 12.22 |
| 2 |  TD       |    2  | 2    | 2301.09 |
| 2       |  TD       |    3  |  2         | 9219.92 |

## Outer Joins

The derived table of an inner join includes only those rows for which the join predicate is true.  However, there are times when you want all rows of one of the tables in a join to be present whether or not there are rows in the other table for which there is a match on the join predicate.  For this scenario, an `OUTER JOIN` can be used.  For example the derived table for the join in,

{{< sqldiagram >}}
```mysql
SELECT * FROM T1 LEFT JOIN T2 ON T1.val = T2.val
```
{{< /sqldiagram >}}

yields a result set that contains at least one instance of every row in T1 (the left table) paired with either a corresponding row in T2 according to the predicate, or `NULL`s if there is no match:

| T1.val  |  T2.val |
|---------|--------|
| A | NULL |
| B |        B |
| C |        C |

There are three kinds of outer joins: `LEFT`, `RIGHT`, and `FULL`.

#### Left Joins

In a left join, as in the example above, all rows of the left-side table in the join will be included at least once, even if there are no rows in the right-side table that match the join predicate.  For any unmatched rows, the columns in the derived table that correspond to the right-side table are left as `NULL`.

#### Right Joins

In a right join, all rows of the right-side table in the join will be included at least once, even if there are no rows in the left-side table that match the join predicate.  For any unmatched rows, the columns in the derived table that correspond to the left-side table are left as `NULL`

{{< hint info >}}
**NOTE**: A left join can easily be rewritten as a right joing (or vice versa) by changing the order of the tables in the join clause.
{{< /hint >}}

#### Full Outer Joins

A full outer join is essentially a combination of left and right outer joins, where rows in which the left-side table has no predicate match in the right-side table will be present in the result set with `NULL` in the right-side columns **and** vice versa. 

MySQL does not have a `FULL OUTER JOIN` (the need for them is rare), but you can achieve the same results by combining a left and right join in a specific way.  The details of this implementation are beyond the scope of this book.  (But see [this StackOverflow question](https://stackoverflow.com/questions/4796872/how-to-do-a-full-outer-join-in-mysql) for more information, paying attention to the comments regarding the subtle errors that can be introduced if you don't get the details right.)

## Joins vs Subqueries

It is often the case that a query using subqueries can be written to use a join instead (and vice versa).

As a practical example, suppose we want a list of all films that are not in stock in the sakila database:

{{< sqldiagram >}}
```mysql
SELECT title
FROM sakila.film
WHERE film_id NOT IN ( SELECT DISTINCT film_id FROM sakila.inventory )
```
{{< /sqldiagram >}}

You can use a left join to yield the same results:

{{< sqldiagram >}}
```mysql
SELECT title
FROM sakila.film as f
    LEFT JOIN sakila.inventory i USING (film_id)
WHERE i.inventory_id IS NULL
```
{{< /sqldiagram >}}

Which is better?  It depends.  In many cases, if a query can be written using either a join or a subquery, the join version is faster.  However, current DB engines are often able to automatically convert to the faster version.

{{< hint info >}}
In general with SQL: write the query in the most intuitive and readable way first, then optimize if the query becomes a bottleneck in your system.
{{< /hint >}}
