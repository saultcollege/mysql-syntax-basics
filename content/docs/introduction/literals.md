---
title: "Literals"
weight: 15
date: 2019-12-04T11:15:23-05:00
---

# Literals

## Numbers

Number values can be written directly.  The following are all valid number representations in MySQL:

```mysql
1
1.0
0.1
.1
+1
-.2
-1.2e-3       -- scientific notation
0xBEEF        -- hexadecimal values start with 0x
```

## Booleans

In MySQL, `TRUE` and `FALSE` are aliases for 1 and 0 respectively


## Strings

A string is a sequence of characters.  You may use single quotes to indicate a string:

```mysql
'This is a string'
```

### Escape Sequences

Within strings, a backslash `\` has a special meaning as the start of an **escape sequence**.  Escape sequences can be used to insert certain special characters into a string.

| Escape Sequence | Meaning |  Example  | Example Output |
|-----------------|--------------------------------|-----------|--------|
| `\n` | new line | `'One line\nAnother line'` | One line <br> Another line
| `\'` | `'`         | `'A nested quote: \''` | A nested quote: '
| `\\ ` | `\` | `'Give me a \\!'` | Give me a \\!

{{< hint warning >}}
**NOTE**: The `\` character gets **ignored** in strings unless it is part of an escape sequence!
{{< /hint >}}

#### Escape Sequences in LIKE clauses

In the context of a `LIKE` clause, the `%` and `_` characters are interpreted by the MySQL engine as wildcards.  If you want to compare these characters literally, the escape sequences `\%` and `\_` can be used.

This LIKE clause would match any values ending with the `%` character, for example:

```mysql
LIKE '%\%'
```
{{< hint warning >}}
**NOTE**: The `\%` and `\_` escape sequences **only** work in the context of a `LIKE` clause
{{< /hint >}}

## Dates

There are a number of ways to represent dates, but here we will recommend one: use a string in the format `YYYY-MM-DD hh:mm:ss`

The following are all valid date/time literals in MySQL

```mysql
'2000-01-23'                 -- date only
'2000-01-23 04:30'           -- date and time
'2000-01-23 04:30:30'        -- date and time with seconds
'2000-01-23 04:30:30.5'      -- date and time with fractional seconds
'04:30:30'                   -- time only

