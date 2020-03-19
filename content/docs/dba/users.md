---
title: "Users"
date: 2020-03-18 21:40:38
weight: 10
---

# Users

MySQL itself uses a database to keep track of certain information such as user accounts.  When you connect to MySQL you do so with a user account.  A user may have restricted access to certain databases or even certain tables, views, and routines within a database, and may also be restricted in which operations are permitted by that user.  For example, a user could be created that is only allowed to read data from one specific table and nothing else, and only if they connect from the same machine as the server.  A ‘root’ user is often given full access to all tables and databases in the system.

In MySQL, database user information is stored in the ‘user’ table of the buit-in ‘mysql’ database, and can be viewed using a query such as 

{{< sqldiagram >}}
```mysql
SELECT * FROM mysql.user
```
{{< /sqldiagram >}}

## Database Users vs Application Users

While most RDBMSs have advanced user management capabilities, it is often the case that database applications do not make much use of them. Instead, most web and desktop applications make use of just a *single* database user with which to connect to the database, and then leave application user management and authentication to the application code.  User info in this scenario is usually stored in a dedicated table in the application’s database.

## Create 

{{< sqldiagram >}}
```mysql
CREATE USER [IF NOT EXISTS] <username>[@<hostname>]
[ IDENTIFIED BY <password> ]
[ DEFAULT ROLE <role_name>,.. ]
```
{{< /sqldiagram >}}

{{< hint warning >}}
**NOTE**: Since a user is always associated with a hostname, it is possible to assign different roles and privileges for the same user depending on which host they connect from.
{{< /hint >}}

{{< hint info >}}
The hostname can be an IP, a domain name, `localhost`, and it may contain the wildcard character `%` (eg `%.saultcollege.ca`)

The hostname determines the host (or range of hosts hosts if the wildcard symbol `%` is used) constrains the user to connecting to the database server from that host (or range of hosts if the wildcard symbol % is used).  

If the hostname is not specified, the default is `%` (any host)

Wildcards do not work for IPv4 address hostnames.  Instead, a netmask can be used, as in `198.51.100.0/255.255.255.0` (meaning "any IP starting with 198.51.100")
{{< /hint >}}

{{< hint info >}}
The password is stored encrypted
{{< /hint >}}

#### Examples

{{< sqldiagram >}}
User ‘test’ can connect from anywhere
```mysql
CREATE USER `test`@`%`
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
User ‘test’ can only connect from same machine as the DB server
```mysql
CREATE USER `test`@`localhost`
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
User ‘test’ can only connect from machines with an IP address that starts with 192.168.0
```mysql
CREATE USER `test`@`192.168.0.0/255.255.255.0`
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
User ‘test’ has a password
```mysql
CREATE USER `test`@`%` IDENTIFIED BY 'badpassword'
```
{{< /sqldiagram >}}

{{< sqldiagram >}}
User ‘test’ has a password and some roles
```mysql
CREATE USER `test` IDENTIFIED BY 'pwd' DEFAULT ROLE reader, writer
```
{{< /sqldiagram >}}

## Drop

{{< sqldiagram >}}
```mysql
DROP USER [IF EXISTS] username[@hostname]
```
{{< /sqldiagram >}}

{{< hint warning >}}
**NOTE**: If there are multiple user accounts that share the same username across multiple hostnames, each username-hostname combination must be dropped separately.
{{< /hint >}}