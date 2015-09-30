Ignite offers a JMS Data Streamer to consume messages from JMS brokers, convert them into cache tuples and insert them in Ignite caches. 
[block:api-header]
{
  "type": "basic",
  "title": "Features supported"
}
[/block]
This data streamer supports the following features:

* Consumes from queues or topics.
* Supports durable subscriptions from topics.
* Concurrent consumers are supported via the `threads` parameter. 
  * When consuming from queues, this component will start as many `Session` objects with separate `MessageListener` instances each, therefore achieving *natural* concurrency.
  * When consuming from topics, obviously we cannot start multiple threads as that would lead us to consume duplicate messages. Therefore, we achieve concurrency in a *virtualized* manner through an internal thread pool.
* Transacted sessions are supported through the `transacted` parameter.
* Batched consumption is possible via the `batched` parameter, which groups message reception within the scope of a local JMS transaction (XA not used supported). Depending on the broker, this technique can provide a higher throughput as it decreases the amount of message acknowledgement round trips that are necessary, albeit at the expense possible duplicate messages (especially if an incident occurs in the middle of a transaction).
  * Batches are committed when the `batchClosureMillis` time has elapsed, or when a Session has received at least `batchClosureSize` messages. 
  * Time-based closure fires with the specified frequency and applies to all `Session`s in parallel. 
  * Size-based closure applies to each individual `Session` (as transactions are `Session-bound` in JMS), so it will fire when that `Session` has processed that many messages.
  * Both options are compatible with each other. You can disable either, but not both if batching is enabled.
* Supports specifying the destination with implementation-specific `Destination` objects or with names.

We have tested our implementation against [Apache ActiveMQ](http://activemq.apache.org), but any JMS broker is supported as long as it client library implements the [JMS 1.1 specification](http://download.oracle.com/otndocs/jcp/7195-jms-1.1-fr-spec-oth-JSpec/).
[block:api-header]
{
  "type": "basic",
  "title": "Instantiating a JMS Streamer"
}
[/block]
When you instantiate the JMS Streamer, you will need to concretualize the following generic types:

* `T extends Message` => the type of JMS `Message` this streamer will receive. If it can receive multiple, use the generic `Message` type.
* `K` => the type of the cache key.
* `V` => the type of the cache value.

To configure the JMS streamer, you will need to provide the following compulsory properties:

* `connectionFactory` => an instance of your `ConnectionFactory` duly configured as required by the broker. It can be a pooled `ConnectionFactory`.
* `destination` or (`destinationName` and `destinationType`) => a `Destination` object (normally a broker-specific implementation of the JMS `Queue` or `Topic` interfaces), or the combination of a destination name (queue or topic name) and the type as a `Class` reference to either `Queue` or `Topic`. In the latter case, the streamer will use either `Session.createQueue(String)` or `Session.createTopic(String)` to get a hold of the destination.
* `transformer` => an implementation of `MessageTransformer<T, K, V>` that digests a JMS message of type `T` and produces a `Map<K, V>` of cache entries to add. It can also return `null` or an empty `Map` to ignore the incoming message.
[block:api-header]
{
  "type": "basic",
  "title": "Example of usage"
}
[/block]
The example in this section populates a cache with `String` keys and `String` values, consuming `TextMessage`s with this format:
[block:code]
{
  "codes": [
    {
      "code": "raulk,Raul Kripalani\ndsetrakyan,Dmitriy Setrakyan\nsv,Sergi Vladykin\ngm,Gianfranco Murador",
      "language": "text"
    }
  ]
}
[/block]
Here is the code:
[block:code]
{
  "codes": [
    {
      "code": "// create a data streamer\nIgniteDataStreamer<String, String> dataStreamer = ignite.dataStreamer(\"mycache\"));\ndataStreamer.allowOverwrite(true);\n\n// create a JMS streamer and plug the data streamer into it\nJmsStreamer<TextMessage, String, String> jmsStreamer = new JmsStreamer<>();\njmsStreamer.setIgnite(ignite);\njmsStreamer.setStreamer(dataStreamer);\njmsStreamer.setConnectionFactory(connectionFactory);\njmsStreamer.setDestination(destination);\njmsStreamer.setTransacted(true);\njmsStreamer.setTransformer(new MessageTransformer<TextMessage, String, String>() {\n    @Override\n    public Map<String, String> apply(TextMessage message) {\n        final Map<String, String> answer = new HashMap<>();\n        String text;\n        try {\n            text = message.getText();\n        }\n        catch (JMSException e) {\n            LOG.warn(\"Could not parse message.\", e);\n            return Collections.emptyMap();\n        }\n        for (String s : text.split(\"\\n\")) {\n            String[] tokens = s.split(\",\");\n            answer.put(tokens[0], tokens[1]);\n        }\n        return answer;\n    }\n});\n\njmsStreamer.start();\n\n// on application shutdown\njmsStreamer.stop();\ndataStreamer.close();",
      "language": "java"
    }
  ]
}
[/block]
To use this component, you must import the following module through your build system (Maven, Ivy, Gradle, sbt, etc.):
[block:code]
{
  "codes": [
    {
      "code": "<dependency>\n    <groupId>org.apache.ignite</groupId>\n    <artifactId>ignite-jms11</artifactId>\n    <version>${ignite.version}</version>\n</dependency>",
      "language": "xml"
    }
  ]
}
[/block]