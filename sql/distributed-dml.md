Since 1.8.0, Ignite is capable not only of querying data from cache, but also to modify it. Supported operations include **MERGE** (a.k.a. upsert), **INSERT**, **UPDATE**, and **DELETE**, and each of them maps to a specific cache operation.

Let's have a closer look at basic concepts and how operations work.
[block:api-header]
{
  "type": "basic",
  "title": "Basic Concepts"
}
[/block]
Four DML operations mentioned above can be split in two small groups: the ones that put new items to cache (that would be **MERGE** and **INSERT**) and those that modify existing items (**UPDATE** and **DELETE**). Let's discuss the former group first.

##Put new items to cache
Both **MERGE** and **INSERT** put new key-value pairs to cache, and their syntax is nearly identical as you will see soon. The difference between them is semantic.

**MERGE** puts given pair(s) to cache without considering current presence of keys in the cache, which is identical to, say, MySQL's `INSERT ... ON DUPLICATE KEY UPDATE`.

In its turn, **INSERT** puts only pairs for which the keys are not in cache yet - it's identical to semantic of **INSERT** in conventional RDBMSes.

Both **MERGE** and **INSERT** may work in two modes - rows list based and subquery based. In first case, the user explicitly lists field values in tuples each of which then is converted to key-value pair and put to cache. In second case, first SQL SELECT is done, and then each row of its results serves as base tuple for new key-value pair.

As long as SQL in case of Ignite is merely an interface to query or manipulate cache data, in the end all DML operations boil down to modifying key-value pairs that reside in cache. Thus, as all columns in Ignite's tables correspond either to key or to value, when a tuple (which is a "new row") is processed, key and value get instantiated and get their fields set based on what has been passed in tuple. After all tuples are well-formed, cache modifying operations are performed.

##Modify existing cache items
(TODO)
[block:api-header]
{
  "type": "basic",
  "title": "DML Operations"
}
[/block]
##MERGE

**MERGE** is the most straightforward operation as it translates to cache **put**/**putAll** operation (depending on how many rows are listed in query, or how many rows have been returned by subquery).

SQL syntax example:
[block:code]
{
  "codes": [
    {
      "code": "merge into Person(_key, first_name, second_name) values\n  (1, \"John\", \"Smith\"),\n  (5, \"Mary\", \"Jones\")",
      "language": "sql",
      "name": "Rows List"
    },
    {
      "code": "merge into Person(_key, first_name, second_name)\n  (select _key + 1000, first_name, second_name\n   \tfrom Person\n   \t  where _key > 100 and _key < 200)",
      "language": "text",
      "name": "Subquery"
    }
  ]
}
[/block]
As you may see, there's two modes to **MERGE** - one that takes