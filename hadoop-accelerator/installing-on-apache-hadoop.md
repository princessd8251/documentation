This article explains how to install Apache Ignite Hadoop Accelerator on Apache Hadoop distribution.

Please read the following articles first to get better understanding of product's architecture:
* http://apacheignite.readme.io/v1.0/docs/overview
* http://apacheignite.readme.io/v1.0/docs/map-reduce
* http://apacheignite.readme.io/v1.0/docs/file-system
[block:api-header]
{
  "type": "basic",
  "title": "Ignite"
}
[/block]
1) Download the latest version of Apache Ignite Hadoop Accelerator and unpack it somewhere.

2) Set `IGNITE_HOME` variable to the directory where you unpacked Apache Ignite Hadoop Accelerator.

3) Ensure that `HADOOP_HOME` environment variable is set and valid. This is required for Ignite to find necessary Hadoop classes.

4) If you are going to use Ignite `FileSystem` implementation, configure `IGFS` in XML configuration (default configuration is `${IGNITE_HOME}/config/default-config.xml`):
[block:code]
{
  "codes": [
    {
      "code": "<bean class=\"org.apache.ignite.configuration.IgniteConfiguration\">\n  ...\n  <property name=\"fileSystemConfiguration\">\n    <list>\n      <bean class=\"org.apache.ignite.configuration.FileSystemConfiguration\">\n        <property name=\"metaCacheName\" value=\"myMetaCache\" />\n        <property name=\"dataCacheName\" value=\"myDataCache\" />       \n      </bean>\n    </list>    \n  </property>\n  ...\n</bean>",
      "language": "xml"
    }
  ]
}
[/block]
IGFS with this configuration will listen for incoming file system requests with default endpoint bound to `127.0.0.1:10500`. 
If you want to override it, provide alternate `ipcEndpointConfiguration`  (see http://apacheignite.readme.io/v1.0/docs/file-system).

5) If you are going to use Ignite map-reduce engine for your jobs, no additional configuration is required, as node will listen for job execution requests with default endpoint bound to `127.0.0.1:11211`. 
If you want to override it, provide alternate 'ConnectorConfiguration' (see http://apacheignite.readme.io/v1.0/docs/map-reduce).

At this point Ignite node is ready to be started:
[block:code]
{
  "codes": [
    {
      "code": "$ bin/ignite.sh",
      "language": "shell"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Hadoop"
}
[/block]
1) Enusre that `IGNITE_HOME` environment variable is set and points to the directory where you unpacked Apache Ignite Hadoop Accelerator.

2) Stop all Hadoop services.

3) Set Ignite JARs `${IGNITE_HOME}\libs\ignite-core-[version].jar` and `${GNITE_HOME}\libs\hadoop\ignite-hadoop-[version].ja`" to Hadoop CLASSPATH.
You can either copy (or symlink) these JARs directly to Hadoop installation (e.g. to `${HADOOP_HOME}/share/hadoop/common/lib`) 
[block:code]
{
  "codes": [
    {
      "code": "ln -s ${IGNITE_HOME}/libs/ignite-core-1.0.0.jar ${HADOOP_HOME}/share/hadoop/common/lib/ignite-core-1.0.0.jar\nln -s ${IGNITE_HOME}/libs/hadoop/ignite-hadoop-1.0.0.jar ${HADOOP_HOME}/share/hadoop/common/lib/ignite-hadoop-1.0.0.jar",
      "language": "shell"
    }
  ]
}
[/block]
or set them to `HADOOP_CLASSPATH` environment variable:
[block:code]
{
  "codes": [
    {
      "code": "export HADOOP_CLASSPATH=${IGNITE_HOME}/libs/ignite-core-1.0.0.jar:${IGNITE_HOME}/libs/ignite-hadoop/ignite-hadoop-1.0.0.jar",
      "language": "shell"
    }
  ]
}
[/block]
4) If you want to use Ignite `FileSystem`, configure it either in the separate `core-site.xml` file, or in default `core-site.xml` located in `${HADOOP_HOME}/etc/hadoop`:
[block:code]
{
  "codes": [
    {
      "code": "<configuration>\n  ...\n  <property>\n    <name>fs.default.name</name>\n    <value>igfs:///</value>\n  </property>\n  ...\n  <property>\n    <name>fs.igfs.impl</name>\n    <value>org.apache.ignite.hadoop.fs.v1.IgniteHadoopFileSystem</value>\n  </property>  \n  ...\n</configuration>",
      "language": "xml"
    }
  ]
}
[/block]
Note that if you change `fs.default.name` to use Ignite FIleSystem in default `core-site.xml`, Hadoop will not be able to work with `HDFS` anymore. If you want to use both Ignite `FileSystem` and `HDFS` at the same time, consider creating separate configuration file.

5) If you want to use Ignite `MapReduce` job tracker, configure it either in the separate `mapred-site.xml` file, or in default `mapred-site.xml` located in `${HADOOP_HOME}/etc/hadoop`:
[block:code]
{
  "codes": [
    {
      "code": "<configuration>\n  ...\n  <property>\n    <name>mapreduce.framework.name</name>\n    <value>ignite</value>\n  </property>\n  <property>\n    <name>mapreduce.jobtracker.address</name>\n    <value>127.0.0.1:11211</value>\n  </property>\n  ...\n</configuration>",
      "language": "xml"
    }
  ]
}
[/block]
6) Start Hadoop.

7) At this point installation is finished and you can start running jobs. 
Run a job with separate `core-site.xml` and/or `mapred-site.xml` configuration files:
[block:code]
{
  "codes": [
    {
      "code": "hadoop --config [path_to_config] [arguments]",
      "language": "shell"
    }
  ]
}
[/block]
Run a job with default configuration:
[block:code]
{
  "codes": [
    {
      "code": "hadoop [arguments]",
      "language": "shell"
    }
  ]
}
[/block]