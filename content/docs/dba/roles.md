---
title: "Roles"
date: 2020-03-18 22:22:36
weight: 20
---

# Roles

Roles can make user access control simpler to manage by removing the need to assign individual privileges to individual users.  Instead, users can be assigned a role (or roles), and privileges can be given to specific roles.  (See the [Privileges]({{< ref "privileges" >}}) section.)  In this way, changing the set of privileges given to a user can be managed by simply changing which roles the user has rather than keeping track of the set of individual privileges required for each user.

## Create

{{< sqldiagram >}}
```mysql
CREATE ROLL [IF NOT EXISTS] <role_name>,..
```
{{< /sqldiagram >}}

## Drop

{{< sqldiagram >}}
```mysql
DROP ROLE [IF EXISTS] <role_name>,..
```
{{< /sqldiagram >}}