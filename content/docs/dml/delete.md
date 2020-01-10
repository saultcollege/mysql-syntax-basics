---
title: "Delete"
date: 2020-01-09 16:23:14
draft: false
weight: 30
---

# Delete

```mysql
DELETE FROM <table_name> [WHERE <<ConditionExpression>>]
```

{{< hint warning >}}
**WARNING**: Leaving off the `WHERE` clause results in ALL data being removed from the table.
{{< /hint >}}

#### Examples

Delete the row for product #123

```mysql
DELETE FROM product WHERE id = 123
```

Delete all products in the 'hardware' category

```mysql
DELETE FROM product WHERE category = 'hardware'
```

Delete EVERY ROW in the product table

```mysql
DELETE FROM product
```