# ActiveGNS
ActiveGNS is built upon Auspice GNS (Global Name Service), see [GNS document](https://github.com/MobilityFirst/GNS) for details about Auspice.

## Prerequisites
To fetch, compile, and run the code in this project, you need to make sure the following have been installed:
* [ant](http://ant.apache.org/)
* [Java 8](https://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)

## Getting started with ActiveGNS
To fetch ActiveGNS from git:
```bash
git clone https://github.com/ZhaoyuUmass/ActiveGNS
```

After the repository is downloaded, cd into ActiveGNS folder and run ant to compile code:
```bash
ant
```

After the code is compile, you can start an single ActiveGNS server by using the script:
```bash
bin/gpServer.sh start all
```

A simple client side example can be found in ActiveCodeHelloWorldExample.java. It creates an GNS account first, then use the account to create a field. After the field is created, it reads the field without active code. Then it deploys its own active code on single field read operation. In the end, the client reads the field again with the active code deployed.
To run the client with no-op active code, use the following command:
```bash
bin/gpClient.sh \
edu.umass.cs.gnsclient.client.testing.activecode.ActiveCodeHelloWorldExample
```
This no-op active code returns the original value of the field without any change, the end of the output should look like:
>...

>Before the code is deployed, the value of field(someField) is original value

>After the code is deployed, the value of field(someField) is original value

To run another simple hello world active code, you could specify the path to the code and run as:
```bash
bin/gpClient.sh \
edu.umass.cs.gnsclient.client.testing.activecode.ActiveCodeHelloWorldExample \
scripts/activeCode/HelloWorld.js
```

This code changes the original value of the field, and returns a new value as a String "hello world!". The end of the output should look like:
>...

>Before the code is deployed, the value of field(someField) is original value

>After the code is deployed, the value of field(someField) is hello world!

## Throughput Test
After you have run the hello world example with no-op active code as shown above, you could test the throughput for no-op active code as:
```bash
bin/gpClient.sh edu.umass.cs.gnsclient.client.testing.activecode.CapacityTestForThruputClient \
NUM_REQUESTS=400000
```
This test sends 400000 requests to saturate the server and the end of the test shows the unsigned read rate as:
>...

>parallel_unsigned_read_rate=5.7K/s


To test it in a loop with 20 runs, use the script as below:
```bash
./scripts/loop
"bin/gpClient.sh edu.umass.cs.gnsclient.client.testing.activecode.CapacityTestForThruputClient \
NUM_REQUESTS=400000"
```
NOTE: It's more meaningful to run server and client on separate physical machines to test the system throughput, the detail about how to run GNS with distributed settings, please refer to [GNS documentation](https://mobilityfirst.github.io/documentation/).

## Stop Server
To stop the running server, use the following command:
```bash
bin/gpServer.sh stop all
```

You can also clear the data being written into database with the following command:
```bash
bin/gpServer.sh clear all
```

## Active code
The active code supported by ActiveGNS should be written in Javascript, and it must implement the following function:
```Javascript
function run(value, field, querier) {
	return value;
}
```
where _field_ is a string, _value_ is a [JSONObject](http://docs.oracle.com/javaee/7/api/javax/json/JsonObject.html) with the queried field and its corresponding value in it,  _querier_ is an object which allows user-defined code to query some other users field. 
Learn how to deploy your code on a certain action, refer to the aforementioned [Hello World example](https://github.com/ZhaoyuUmass/ActiveGNS/blob/master/src/edu/umass/cs/gnsclient/client/testing/activecode/ActiveCodeHelloWorldExample.java).

## 