---
layout: post
title:  "Dapper | Avoid parentheses in SQL statement when using IN clause"
date:   2019-07-16 11:00:00
categories: dapper parameters sql in
---
# >_

Hi there!

My first attempt to write a post in english. Please, forgive me in advance by my poor writing skills in english (worst than the spanish ones, if it is possible).

## Scenario

Regardless this will be a very short post, I don't want to let pass the opportunity to remember to my future me, that when using [Dapper](https://github.com/StackExchange/Dapper), if the query should filter with the SQL clause IN using collection parameter, I shouldn't write the parentheses in the SQL statement itself.

Imagine that you have a set of data that should be filtered to include only those records that are equal to a list of values for a given property. Something like...

```SQL
SELECT * FROM [table] WHERE [Property1] IN ('A', 'B', 'C')
```

Very common, isn't it? 

Also, the list of values that I want to filter by is dynamic, and it is coming from a `IEnumerable<string>` collection in C#.

At last, imagine that you are using dapper to perform your queries to the database.

## Approaches

### Approach 1 - Dynamic query construction using string manipulation classes

Weird. **You don't need to do this.**

Dapper helps you to convert a collection into it's representation using the parameter notation on the query itself, passing to the `Query<>` family methods an anonymous object with a named property matching the parameter indicated in the query statement.

### Approach 2 - Use collection parameter on Dapper

So, you want to use the parameter system from Dapper. Great! Good choice.

In this approach, your code probably looks like:

```csharp
// 'connection' has been initialized previously
var values = new string[] { "A", "B", "C" };
var entity = await connection.QueryAsync<AmazingEntity>(queryStatement, new { values });
```

... and you probably define your query as follows:

```SQL
SELECT * FROM [table] WHERE [Property1] IN (@values)
```

Wait! Wrong statement. This one will throw an exception when will be executed.

Why?

Dapper automatically identify a string collection array passed by parameter and, instead of converting it to `'A', 'B', 'C'` is converting it to `('A', 'B', 'C')`, resulting in a statement like:

```SQL
SELECT * FROM [table] WHERE [Property1] IN (('A', 'B', 'C'))
```

Note the double parentheses in the sentence. **This query is invalid in SQL**. And will throw an exception:

> Incorrect syntax near ','

So, to fix this, just [remove the parentheses from your query and let Dapper do the rest for you](https://dapper-tutorial.net/knowledge-base/31997381/dapper--in--clause-not-working-with-multiple-values).

```SQL
SELECT * FROM [table] WHERE [Property1] IN @values
```