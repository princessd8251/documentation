## Apache Ignite In-Memory Data Fabric 1.0
* Simplified query API.
* Added automatic aggregation, grouping, and sorting support to SQL queries.
* Added dynamic caches, i.e. ability to start and stop caches during runtime.
* Changed IgniteDataLoader to IgniteDataStreamer.
* Added automatic transformation support to streaming.
* Removed old streaming APIs in favor of new IgniteDataStreamer.
* Added many examples using Java8 lambdas and streams.
* Added new streaming examples based on new streaming and SQL querying APIs.
* Added automatic schema-import demo under examples.
* Improved javadoc documentation.
* Added ability to automatically exclude LGPL optional dependencies during build.

## Apache Ignite In-Memory Data Fabric 1.0-RC3
This is the first release of Apache Ignite project. The source code is based on the 7 year old GridGain In-Memory Data Fabric, open source edition, v. 6.6.2, which was donated to Apache Software Foundation in September 2014.

The main feature set of Ignite In-Memory Data Fabric includes:
* Advanced Clustering
* Compute Grid
* Data Grid
* Service Grid
* IGFS - Ignite File System
* Distributed Data Structures
* Distributed Messaging
* Distributed Events
* Streaming & CEP