# ActiveGNS Remote Query
We provide an object _querier_ for user code to interact with ActiveGNS. All the public methods in Querier interface can be called by user code, see detailed implementation of [Querier](https://github.com/ZhaoyuUmass/ActiveGNS/blob/master/src/edu/umass/cs/gnsserver/activecode/prototype/interfaces/Querier.java) interface. The methods in Querier interface are called **Remote Query**.
The methods of _querier_ exposed to user code include:
```Java
JSONObject readGuid(String guid, String field) throws ActiveException;
void writeGuid(String guid, String field, JSONObject value) throws ActiveException;
```
* _readGuid_ allows customer's Javascript code to read the value of _field_, from _guid_. The parameter _guid_ could be the same as customer's own GUID, and it does not require any ACL check to read the value of _field_. If _guid_ is different from the customer's own GUID, the customer must make sure he is permitted to read the _field_ . Otherwise, he will get an exception to indicate that the read operation is not allowed.
* The method _writeGuid_ allows customer's code to write _value_ into _field_ of _guid_. If the parameter _guid_ is the same as customer's own GUID, then there is no need for ACL check, and the _field_ will be updated. If _guid_ is different from the customer's own GUID, the customer must make sure he is permitted to write into the _field_ of _guid_. Otherwise, he will get an exception to indicate that the write operation is not allowed.

A test to show how it works can be run as follows. First, starts 3 replicated local servers,
```bash
bin/gpServer.sh -DgigapaxosConfig=conf/gnsserver.3local.properties restart all
```
Then run the test client,
```bash
bin/gpClient.sh -DgigapaxosConfig=conf/gnsserver.3local.properties \
edu.umass.cs.gnsclient.client.testing.activecode.TestActiveCodeRemoteQueryClient
```
There are four tests for remote query, and all tests should pass with the output like:
> ...
> Depth query test(a read followed by a read) succeeds!
> Depth query test(a write followed by a read) succeeds!
> ...
> Depth query test(a write followed by a read) succeeds!
> Depth query test(a write followed by a write) succeeds!