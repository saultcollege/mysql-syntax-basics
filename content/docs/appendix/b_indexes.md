---
title: "Appendix B: Indexes"
date: 2019-12-16T10:56:00
weight: 15
---

# Appendix B: Indexes

Indexes are useful for speeding up queries that need to look up specific values in large tables.  Think of the way the index in a textbook works.  Without an index, if you want to find the instances of a specific word in the book you have to comb through each page in the book one by one.  If you have an index, though, you simply need to refer to the index and it will tell you which pages the word is on—much faster!  

A database index is similar in that it is data stored separately from a table for the purpose of making searching for terms in specific columns much faster.  Without an index in the database, looking up the id of some entity, for example, would require the database engine to run through every row in the table until it finds the matching id.  But if there is an index the engine can refer to it to very quickly locate the id in the table.

## Indexes are NOT Free!
{{< hint warning >}}
**WARNING**: There is a cost to adding an index to a table.  While an index can make certain queries (database reads) run much faster, they also cause database writes (INSERT and/or UPDATE) to become slower.  This is because every time data is updated in the table, any relevant indexes must also be updated, and updating an index is a slow operation.  You should always be aware of the trade-off in read speed increase vs write speed slow-down when adding an index to a database.
{{< /hint >}}

## When to Use Indexes

Applying indexes to improve database performance is somewhat of an art, as there is no simple set of rules that work in every situation.  Indexes should only be employed after verifying that they do in fact enhance database performance in the desired ways and don't reduce performance in undesired ways.

{{< hint info >}}
A general rule of thumb is that columns that are often used in `WHERE` clauses or `JOIN` predicates can often be indexed to improve database performance.  (But don't forget to do performance testing to verify performance metrics.)
{{< /hint >}}

#### Example

Imagine a large user table for a social network.  It has a surrogate primary key for each user, but it also has a username and/or firstname and lastname fields.  A common operation on a social network would be to search for a particular user by name.  Without an index, a query like SELECT * FROM user WHERE username = 'lolcat99' would need to search every row in the user table for the username ‘lolcat99’.  Adding an index on the username column could greatly speed up the above query.

## Indexes and Primary/Foreign Keys

In MySQL, all primary keys are automatically indexed.  This is done because primary keys are the main column used to look up table rows.

{{< hint info >}}
**NOTE**: Since primary keys are automatically indexed, you should never need to create an index over a primary key
{{< /hint >}}

{{< hint info >}}
**NOTE**: It is also generally best practice to index all foreign keys because foreign key columns are often used in `JOIN` predicates.
{{< /hint >}}

For further information, read [How MySQL Uses Indexes](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)
