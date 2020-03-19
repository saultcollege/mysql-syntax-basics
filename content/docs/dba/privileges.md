---
title: "Privileges"
date: 2020-03-18 22:51:31
weight: 30
---

# Privileges

There is a large set of [predefined privileges](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_index) in MySQL that can be assigned to both users and roles.  Below is a list of common useful privilege types.

| Privilege Type | Description |
|----------------|-------------|
| ALL | All privileges |
| SELECT | Allows rows to be read from database tables |
| INSERT | Allows rows to be inserted into database tables |
| UPDATE | Allows updating rows in database tables |
| DELETE | Allows rows to be deleted from database tables |
| CREATE | Allows `CREATE` statements for databases and tables |
| CREATE_ROUTINE | Allows making stored procedures or user defined functions |
| DROP | Allows `DROP` statements |
| INDEX | Allows creation and manipulation of indexes |
| TRIGGER | Allows triggers to activate |
| ROLE_ADMIN | Allows use of `GRANT` and `REVOKE` statements |
| CREATE_USER | Allows the creation of new users |

## Grant privileges to users or roles

{{< sqldiagram >}}
```mysql
GRANT <privilege_name>,.. ON <scope> TO <username>[@<hostname>]|<role_name>
```
{{< /sqldiagram >}}

{{< hint info >}}
`<privilege_name>` may be one or a comma-separated list of any of the predefined privileges
{{< /hint >}}

{{< hint info >}}
`<scope>` specifies which databases objects (tables, routines, etc.) the `GRANT` applies to:

 - `*.*` means any database, any table
 - `<db_name>.*` means any object in the named database 
 - `<db_name>.<object_name>` means only the named object in the named database

{{< /hint >}}

#### Examples

{{< sqldiagram >}}
The ‘test’ user may `SELECT` rows from any table in any db
```mysql
GRANT SELECT ON *.* TO test
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
The ‘test’ user may `INSERT`, `UPDATE`, and `DELETE` rows in the `store` db
```mysql
GRANT INSERT, UPDATE, DELETE ON store.* TO test
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
The ‘test’ user has all privileges in only the store.product table
```mysql
GRANT ALL ON store.product TO test
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
The ‘reader’ role has the `SELECT` privilege
```mysql
GRANT SELECT ON store.* TO reader
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
The ‘writer’ role has the `INSERT`, `UPDATE`, and `DELETE` privileges
```mysql
GRANT INSERT, UPDATE, DELETE ON store.* TO writer
```
{{< /sqldiagram >}}

## Grant roles to users

{{< sqldiagram >}}
```mysql
GRANT <role_name>,.. TO <username>[@<hostname>]
```
{{< /sqldiagram >}}

#### Examples

{{< sqldiagram >}}
Grant the reader role to user ‘test’
```mysql
GRANT reader TO test;
```
{{< /sqldiagram >}}


{{< sqldiagram >}}
Grant the reader and writer roles to user ‘test’
```mysql
GRANT reader, writer TO test
```
{{< /sqldiagram >}}

## Revoke

The REVOKE command works the same as the GRANT command but revokes privileges from users/roles instead of granting them

{{< sqldiagram >}}
```mysql
REVOKE <privilege_name>,.. ON <scope> FROM <username>[@<hostname>]|<role_name>
```
{{< /sqldiagram >}}

{{< hint info >}}
Note the use of `FROM` in the `REVOKE` statement instead of `TO` as in `GRANT`
{{< /hint >}}

## Show

{{< sqldiagram >}}
```mysql
SHOW GRANTS FOR <username>[@<hostname>]|<role_name>
```
{{< /sqldiagram >}}

