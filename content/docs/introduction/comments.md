---
title: "Comments"
date: 2019-12-16T11:15:00
weight: 20
---

# Comments

There are a number of different ways to make comments in MySQL scripts:

{{< sqldiagram >}}
```mysql
-- This is a single-line comment
-- Each line must be preceeded by two dashes
-- The second dash must be followed by a whitespace character
--(This is NOT a comment because there is no whitespace after the dash)

# This is also a single-line comment
#And so is this (even without whitespace after the #)

/*
This is a multi-line comment using c-style syntax.
It starts with the slash-star sequence,
and ends with a star-slash sequence.
*/

SELECT 1 + 2;  -- Comments can come at the end of SQL statements

SELECT 1 + /* c-style comments may be placed in-line */ + 2 + 3;

SELECT 1 + -- But single-line comments go to the end of the line -- + 2 + 3;
```
{{< /sqldiagram >}}