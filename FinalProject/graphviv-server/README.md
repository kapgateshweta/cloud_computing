graphviz-server
===============

Graphviz-server is a lightweight Java based HTTP server that invokes the [Graphviz](http://www.graphviz.org/) dot binary installed locally. You simply submit a HTTP POST with the dot graph as the request body and the server returns back a graph in SVG, PDF or PNG format. Graphviz-server uses the [Graphviz Java API](https://github.com/jabbalaci/graphviz-java-api), a Java wrapper that invokes the dot binary using Runtime.exec.


Steps to run web server
* Clone the git repository - `git clone https://github.com/kapgateshweta/cloud_computing/`
* enter the graphviz-server folder and do the following operations
* An executable jar with dependencies is included in the dist folder. The port on which the server listens can be configured as a command line parameter to the jar. To change the default port (8080) edit the DotGraphics.sh in the dist directory:
```
#!/bin/sh
java -jar DotGraphics.jar 8080 > /dev/null 2>&1 &
exit 0
```
* If you want to make changes to the code and build your own jar, you need to have Maven installed. Simply run `mvn package` this will create a jar with dependencies inside the **target** directory.

## Usage

Run the graphviz-server:
```
./DotGraphics.sh
```
