---
title: "Update"
date: 2020-01-09 16:23:14
draft: false
weight: 20
---

# Update

```mysql
UPDATE <table_name> SET {<col_name> = <value> | DEFAULT},.. 
[WHERE <<ConditionExpression>>]
```

{{< hint info >}}
The `<value>` may be a reference to another column from the table, or an expression
{{< /hint >}}

{{< hint warning >}}
**WARNING**: Having no `WHERE` condition results in the specified values getting set for **every row** of the table.
{{< /hint >}}

#### Examples

Set every row’s stock to the default

```mysql
UPDATE product SET stock = DEFAULT
```

Set product 213’s name to Wrench

```mysql
UPDATE product SET name = 'Wrench' WHERE id = 213
```

Setting multiple column values at once

```mysql
UPDATE product SET name = 'Wrench', stock = 4 WHERE id = 23
```

Referring to another column in the same table

```mysql
UPDATE product SET name = 'Wrench', description = name WHERE id = 23
```
