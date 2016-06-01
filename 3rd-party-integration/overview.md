Ignite Cassandra module implements [persistent store](doc:persistent-store) for Ignite caches by utilizing Cassandra as a persistent storage for expired cache records.

It functions pretty much the same way like [CacheJdbcBlobStore](doc:persistent-store#cachejdbcblobstore) and [CacheJdbcPojoStore](doc:persistent-store#cachejdbcpojostore) and provides such benefits:

1. Utilizes [Apache Cassandra](http://cassandra.apache.org/), which is a very high performance and scalable key-value storage.
2. Uses Cassandra [asynchronous queries](http://www.datastax.com/dev/blog/java-driver-async-queries) for CacheStore batch operation [LOADALL(), WRITEALL(), DELETEALL()](http://apacheignite.gridgain.org/docs/persistent-store#section-loadall-writeall-deleteall-) to provide extremely high performance.
3.  Automatically creates all necessary tables (and keyspaces) in Cassandra if they are absent. Also automatically detects all the necessary fields for Ignite key-values which should be stored as POJO and creates appropriate table structure for you. Thus you don't need to care about Cassandra DDL syntax for table creation and Java to Cassandra type mapping details. You can also use `@QuerySqlField` annotation to provide configuration (column name, index, sort order) for Cassandra table columns.
4. You can optionally specify the settings (replication factor, replication strategy, bloom filter and etc.) for Cassandra tables and keyspaces which should be created.
5. Combines functionality of BLOB and POJO storage, allowing to specify how you prefer to store (as a BLOB or as a POJO) key-value pairs from your Ignite cache.
6. Supports Standard [Java](https://docs.oracle.com/javase/tutorial/jndi/objects/serial.html) and [Kryo](https://github.com/EsotericSoftware/kryo) serialization for key-values which should be stored as a BLOB in Cassandra
7. Supports Cassandra [secondary indexes](http://docs.datastax.com/en/cql/3.0/cql/cql_reference/create_index_r.html) (including custom indexes) through persistence configuration settings for particular Ignite cache or such settings could be detected automatically if you configured [SQL Indexes by Annotations](doc:sql-queries#configuring-sql-indexes-by-annotations) by using `@QuerySqlField(index = true)` annotation
8. Supports sort order for Cassandra cluster key fields through persistence configuration settings or such settings could be detected automatically if you are using `@QuerySqlField(descending = true)` annotation.
9. Supports [Affinity Collocation](doc:affinity-collocation) for the POJO key classes having one of their fields annotated by `@AffinityKeyMapped`. In a such way, key-values pairs which were stored on one node in Ignite cache will be also stored (collocated) on one node in Cassandra. 
[block:api-header]
{
  "type": "basic",
  "title": "Index"
}
[/block]
* [Overview](doc:overview)
* [Base concepts](doc:base-concepts)
* [Examples](doc:examples)
* [DDL generator](#ddl-generator)
* [Load tests](#load-tests)
* [AWS infrastructure deployment](doc:aws-infrastructure-deployment)
* [Unit tests](doc:unit-tests)