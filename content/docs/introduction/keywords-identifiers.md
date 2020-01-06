---
title: "Keywords and Identifiers"
weight: 13
date: 2019-12-04T11:15:23-05:00
---

# Keywords & Identifiers

Keywords are terms that have signifcant meaning in SQL.  For example, `SELECT` and `TABLE`.  There is a very long list of keywords.  

Sometimes you may want to name a database object with the same name as a keyword.  This can cause syntax errors because the MySQL engine will interpret your name as a keyword.  To avoid this problem, you may quote such names with the backtick character `` ` ``.  

{{< hint warning >}}
**NOTE:** The backtick is **not** a single quote.  Compare `` ` `` vs `'`.  The backtick key is usually located to the left of the `1` key on english keyboards.
{{< /hint >}}

For example, suppose you want to create a table named 'table'.  The following statement causes a syntax error:

```mysql
CREATE TABLE table ( /* column/constraint definitions */ )  
```

But this statement does not:

```mysql
CREATE TABLE `table` ( /* column/constraint definitions */ )  
```