* [Overview](#overview)
* [Cluster Configuration](#cluster-configuration)
* [Thread-safety](#thread-safety)
* [Prerequisites](#prerequisites)
* [Building ODBC Driver](#building-odbc-driver)
* [Installing ODBC Driver](#installing-odbc-driver)
[block:api-header]
{
  "type": "basic",
  "title": "Overview"
}
[/block]
Ignite now includes ODBC driver that allows you to retrieve distributed data from cache using standard SQL queries and native ODBC API.

For detailed info on ODBC please refer to [ODBC Programmer's Reference](https://msdn.microsoft.com/en-us/library/ms714177.aspx).

Apache Ignite ODBC driver implements version 3.0 of the ODBC API.
[block:api-header]
{
  "type": "basic",
  "title": "Cluster Configuration"
}
[/block]
ODBC driver is treated as a dynamic library on Windows and a shared object on Linux. An application does not load it directly and instead it uses Driver Manager API which in its own way loads and unloads ODBC drivers whenever it's needed.

Internally, Ignite ODBC driver uses TCP protocol to connect to an Ignite cluster. The connection works over Ignite component called `OdbcProcessor`. By default `OdbcProcessor` is not enabled and not started when an Ignite node is being launched​. To enable the processor you need to set `OdbcConfiguration` in your `IgniteConfiguration`:
[block:code]
{
  "codes": [
    {
      "code": "<bean id=\"ignite.cfg\" class=\"org.apache.ignite.configuration.IgniteConfiguration\">\n  ...\n  <!-- Enabling ODBC. -->\n  <property name=\"odbcConfiguration\">\n    <bean class=\"org.apache.ignite.configuration.OdbcConfiguration\"/>\n  </property>\n  ...\n</bean>\n",
      "language": "xml"
    },
    {
      "code": "IgniteConfiguration cfg = new IgniteConfiguration();\n...\nOdbcConfiguration odbcCfg = new OdbcConfiguration();\ncfg.setOdbcConfiguration(odbcCfg);\n...",
      "language": "java"
    }
  ]
}
[/block]
After `OdbcProcessor` is configured in a way like above it will be started with default settings some of which are listed below:
* `endpointAddress` - Address to bind to in format `hostname[:port_from[..port_to]]`. Default value is `0.0.0.0:10800..10810`. If you specify host name but won't specify a port range then the default port range will be used.
* `maxOpenCursors` - Limit for the number of cursors that can be opened simultaneously. The default value is 128.
* `socketSendBufferSize` - Size of the TCP socket send buffer. The default value is 0 which instructs to use a system default value.
* `socketReceiveBufferSize` - Size of the TCP socket receive buffer. The default value is 0 which instructs to use a system default value.
* `threadPoolSize` - Number of request-handling threads in the thread pool. The default value is `IgniteConfiguration.DFLT_PUBLIC_THREAD_CNT`.

It's always possible to change these parameters as it's shown in the example below:
[block:code]
{
  "codes": [
    {
      "code": "<bean id=\"ignite.cfg\" class=\"org.apache.ignite.configuration.IgniteConfiguration\">\n  ...\n  <!-- Enabling ODBC. -->\n  <property name=\"odbcConfiguration\">\n    <bean class=\"org.apache.ignite.configuration.OdbcConfiguration\">\n      <property name=\"endpointAddress\" value=\"127.0.0.1:12345..12346\"/>\n      <property name=\"maxOpenCursors\" value=\"512\"/>\n      <property name=\"socketSendBufferSize\" value=\"65536\"/>\n      <property name=\"socketReceiveBufferSize\" value=\"131072\"/>\n      <property name=\"threadPoolSize\" value=\"4\"/>\n    </bean>\n  </property>\n  ...\n</bean>\n",
      "language": "xml"
    },
    {
      "code": "IgniteConfiguration cfg = new IgniteConfiguration();\n...\nOdbcConfiguration odbcCfg = new OdbcConfiguration();\n\nodbcCfg.setEndpointAddress(\"127.0.0.1:12345..12346\");\nodbcCfg.setMaxOpenCursors(512);\nodbcCfg.setSocketSendBufferSize(65536);\nodbcCfg.setSocketReceiveBufferSize(131072);\nodbcCfg.setThreadPoolSize(4);\n\ncfg.setOdbcConfiguration(odbcCfg);\n...",
      "language": "java"
    }
  ]
}
[/block]
A connection that is established from ODBC driver side to the cluster via `OdbcProcessor` is also configurable and [here](doc:connecting-string) you can find more details on how to alter connection settings from the driver side.
[block:api-header]
{
  "type": "basic",
  "title": "Thread-safety"
}
[/block]
Current implementation of the Ignite ODBC driver only provides thread-safety on the connections level. It means you should not access the same connection from several threads without additional synchronization, though you can create separate connections for every thread and use them simultaneously.
[block:api-header]
{
  "type": "basic",
  "title": "Prerequisites"
}
[/block]
Apache Ignite ODBC Driver was officially tested on:
[block:parameters]
{
  "data": {
    "0-0": "OS",
    "0-1": "Windows (XP and up, both 32-bit and 64-bit versions), \nWindows Server (2008 and up, both 32-bit and 64-bit versions)\nUbuntu (14.x and 15.x 64-bit)",
    "1-0": "C++ compiler",
    "1-1": "MS Visual C++ (10.0 and up), g++ (4.4.0 and up)",
    "2-1": "2010 and above",
    "2-0": "Visual Studio"
  },
  "cols": 2,
  "rows": 3
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Building ODBC driver"
}
[/block]
Ignite ODBC Driver shipped in sources as a part of Apache Ignite package. It means that you need to build it before you are going to be able to use it. For instructions on how to acquire and set up Apache Ignite itself please refer to [Getting Started](doc:getting-started) page.

As ODBC Driver is written in C++ it is shipped as as part of the Apache Ignite C++ and depends on some of the C++ libraries. More specifically it depends on `utils` and `binary` Ignite libraries. It means you will need to build them prior to building ODBC driver itself.

## Building on Windows
You need MS Visual Studio 2010 or later to be able to build ODBC driver on Windows. Once you have it open Ignite solution `%IGNITE_HOME%\platforms\cpp\project\vs\ignite.sln` (or ignite_86.sln if you are running 32-bit platform) left-click odbc project in Solution Explorer and choose "Build". Visual Studio will automatically detect and build all necessary dependencies.
[block:callout]
{
  "type": "warning",
  "title": "",
  "body": "If you are using VS 2015 or later (MSVC 14.0 or later), you need to add `legacy_stdio_definitions.lib` as an additional library to `odbc` project linker's settings in order to be able to build the project. To add this library to the linker input in the IDE, open the context menu for the project node, choose `Properties`, then in the `Project Properties` dialog box, choose `Linker`, and edit the `Linker Input` to add `legacy_stdio_definitions.lib` to the semi-colon-separated list."
}
[/block]
Once the build process is over you can find `odbc.dll` in `%IGNITE_HOME%\platforms\cpp\project\vs\x64\Release`.

## Building on Linux
On a Linux-based operation system you will need to install ODBC Driver Manager of your choice manually to be able to build and use Ignite ODBC Driver. Apache Ignite ODBC Driver has been tested with [UnixODBC](http://www.unixodbc.org).

Also you will need `GCC`, `G++`, and `Make` to build the driver and its dependencies.

Once all is installed you can build Ignite ODBC driver:
[block:code]
{
  "codes": [
    {
      "code": "cd $IGNITE_HOME/platforms/cpp\nlibtoolize && aclocal && autoheader && automake --add-missing && autoreconf\n./configure --enable-odbc --disable-node --disable-core\nmake\n\n#The following step will most probably require root privileges:\nmake install",
      "language": "shell"
    }
  ]
}
[/block]
Once the build process is over you can find out where is your ODBC driver has been placed running the following command:
[block:code]
{
  "codes": [
    {
      "code": "whereis libignite-odbc",
      "language": "shell"
    }
  ]
}
[/block]
The path will be most likely `/usr/local/lib/libignite-odbc.so`
[block:api-header]
{
  "type": "basic",
  "title": "Installing ODBC driver"
}
[/block]
In order to use ODBC driver you need to register it in your system so your ODBC Driver Manager will be able to locate it.

## Installing on Windows
For 32-bit Windows you should use 32-bit version of the driver while for the
64-bit Windows you can use 64-bit driver as well as 32-bit. You may want to install both 32-bit and 64-bit drivers on 64-bit Windows to be able to use your driver from both 32-bit and 64-bit applications.

To install driver on Windows you should first choose a directory on your
file system where your driver or drivers will be located. Once you have
chosen a place you should put your driver there and ensure that all driver
dependencies can be resolved i.e. they can be found either in the %PATH% or
in the same directory as the driver.

After that you should use one of the install scripts from the directory 
`%IGNITE_HOME%/platforms/cpp/odbc/install`. Note that most likely you will need OS administrator privileges to execute these scripts.
[block:code]
{
  "codes": [
    {
      "code": "install_x86 <absolute_path_to_32_bit_driver>",
      "language": "shell",
      "name": "x86"
    },
    {
      "code": "install_amd64 <absolute_path_to_64_bit_driver> [<absolute_path_to_32_bit_driver>]",
      "language": "shell",
      "name": "amd64"
    }
  ]
}
[/block]

## Installing on Linux

To be able to build and install ODBC driver on Linux, you first need to install
ODBC Driver Manager. Apache Ignite ODBC driver has been tested with [UnixODBC]
(http://www.unixodbc.org). 

Once you have built and performed "make install" command the Ignite ODBC Driver i.e. `libignite-odbc.so` is going to be most likely placed to `/usr/local/lib`. To install it as an ODBC driver in your Driver Manager and be able to use it you should perform the following steps:

* Ensure linker is able to locate all dependencies of the ODBC driver. You can check it using `ldd` command like this (assuming ODBC driver is located under `/usr/local/lib`):
  ```ldd /usr/local/lib/libignite-odbc.so```
If there is unresolved links to other libraries you may want to add directories with these libraries to the `LD_LIBRARY_PATH`.

* Edit file `$IGNITE_HOME/platforms/cpp/odbc/install/ignite-odbc-install.ini` and ensure that `Driver` parameter of the `Apache Ignite` section points to the right location where `libignite-odbc.so` is located.
   
* To install Apache Ignite ODBC driver use the following command:
  ```odbcinst -i -d -f $IGNITE_HOME/platforms/cpp/odbc/install/ignite-odbc-install.ini```
  To perform this command you most likely will need root privileges.

Now Apache Ignite ODBC driver is installed and ready for use. You can connect to it and use it just like any other ODBC driver.