* [Prerequisites](#prerequisites)
* [Building Ignite Web Agent](#building-ignite-web-agent)
* [Run Ignite Web Console Frontend](#run-ignite-web-console-in-development-mode)
[block:api-header]
{
  "type": "basic",
  "title": "Prerequisites"
}
[/block]
In order to local deployment of Ignite Web Console you should install before:

* MongoDB (version >=3.2.x) follow instructions from site [http://docs.mongodb.org/manual/installation](http://docs.mongodb.org/manual/installation)
* NodeJS (version >=6.5.x) using installer from site [https://nodejs.org/en/download/current](https://nodejs.org/en/download/current) for your OS.

Before first start you need download dependencies:
* For backend:
`cd $IGNITE_HOME/modules/web-console/backend`
`npm install --no-optional`

* For frontend:
`cd $IGNITE_HOME/modules/web-console/frontend `
`npm install --no-optional`
[block:api-header]
{
  "type": "basic",
  "title": "Building Ignite Web Agent"
}
[/block]
To build Ignite Web Agent from sources run following command in Ignite project root folder:
`mvn clean package -pl :ignite-web-agent -am -P web-console -DskipTests=true`

Once the build process is over you can find `ignite-web-agent-x.x.x.zip` in: 
`<Ignite project root folder>/modules/web-console/web-agent/taget`
[block:api-header]
{
  "type": "basic",
  "title": "Run Ignite Web Console In Development Mode"
}
[/block]
If needed run "npm install --no-optional" (if dependencies changed) and start webpack in development mode "npm run dev".