---
title: "Views"
date: 2020-02-04 15:27:28
weight: 30
---

# Views

Views may be thought of as queries that are 'saved' in the database.  Each view has a name, and views can be used in any context in which a table could be used.  Views can be useful for...

 - making a query reusable
 - encapsulating business logic
 - providing data security
 - providing backward compatibility in a database

## Create

{{< sqldiagram >}}
```mysql
CREATE [OR REPLACE] VIEW <view_name> AS <<SelectStatement>>
```
[<<SelectStatement>>]({{< ref "select" >}})
{{< /sqldiagram >}}

{{< hint info >}}
**NOTE:** If `OR REPLACE` is used any view with the same name will be replaced with the new view, otherwise an error occurs if a view with the same name already exists.
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
A simple view
```mysql
CREATE OR REPLACE VIEW seniors AS
    SELECT id FROM customer WHERE age > 65
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
Getting data from a view
```mysql
SELECT * FROM seniors
```
{{< /sqldiagram >}}
{{< sqldiagram >}}
Using a view in a `WHERE` clause
```mysql
SELECT name FROM customer WHERE id IN (seniors)
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
A view that is simply today's date formatted in a certain way
```mysql
CREATE VIEW today AS SELECT DATE_FORMAT(NOW(), '%W %M %Y');
```
{{< /sqldiagram >}}

## Drop

{{< sqldiagram >}}
```mysql
DROP VIEW [IF EXISTS] <view_name>,..
```
{{< /sqldiagram >}}