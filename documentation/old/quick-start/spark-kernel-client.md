---
layout: docpage
title: Spark Kernel Client
type: doc
section: quick-start
weight: 0
tagline: Apache Project !
---

The following is a rough guide to building, setting up, and using the _Spark Kernel client_, a library available to communicate with the Spark Kernel.

### Building from Source ###

From the root of the Spark Kernel project invoke the following commands: 
```
sbt compile
sbt publishLocal
```

This will publish the Spark Kernel build artifacts to your `~/.ivy2/local`
directory. You can now include the Spark Kernel Client jar in your SBT 
dependencies:

```scala
libraryDependencies += "com.ibm.spark" %% "client" % "0.1.1-SNAPSHOT"
```

### Usage Instructions ###

A comprehensive example of all the steps below can be found in [DocumentationExamples.scala](https://github.com/ibm-et/spark-kernel/blob/master/client/src/test/scala/examples/DocumentationExamples.scala).

### Client Setup ###

To begin using the Spark Kernel Client you will need to specify connection 
information for the client. This is done by sending a JSON structure to 
TypeSafe's ConfigFactory. This connection information is printed whenever a 
Spark Kernel is started. An example JSON structure for the default config values
for the Spark Kernel would be:

```scala
val profileJSON: String = """
    {
        "stdin_port":   48691,
        "control_port": 40544,
        "hb_port":      43462,
        "shell_port":   44808,
        "iopub_port":   49691,
        "ip": "127.0.0.1",
        "transport": "tcp",
        "signature_scheme": "hmac-sha256",
        "key": ""
    }
  """.stripMargin
```

Once the JSON structure exists, the kernel client can be created:

```scala
val config: Config = ConfigFactory.parseString(profileJSON)
val client = (new ClientBootstrap(config) 
  with StandardSystemInitialization
  with StandardHandlerInitialization).createClient
```

### Executing Code ###

Executing code with the client is done by invoking the `execute` function. 
`execute` takes a string, representing the code to execute, as an argument. 
Example executions include:
 
```scala
//  Create a variable, z, and assign a value to it
client.execute("val z = 0")
//  Perform some computation 
client.execute("1 + 1")
//  Print some message 
client.execute("println(\"Hello, World\")")
```

The execute function returns a deferred object which allows for interacting
with results from the code execution. The methods `onResult`, `onStream`, and 
`onError` are the means to do so and are explained below.

### Receiving Results `onResult` ###

The results of code execution are sent to callback function registered with
`onResult`. The argument to the callback function is an [`ExecuteResult`]
(http://ipython.org/ipython-doc/dev/development/messaging.html#id4) from the
IPython message protocol. Code execution results originate from variable 
assignments and simple Scala statements. Any number of callbacks can be 
registered, even if code execution has already completed. Each callback will be 
invoked once for successful code execution and never in the case of failure. 
Examples include:  

```scala
//  Define our callback
def printResult(result: ExecuteResult) = {
    println(s"Result was: ${result.data.get(MIMEType.PlainText).get}")
}
//  Create a variable, z, and assign a value to it
client.execute("val z = 0").onResult(printResult)
//  Perform some computation, and print it twice 
client.execute("1 + 1").onResult(printResult).onResult(printResult)
//  The callback will never be invoked 
client.execute("someUndefinedVariable").onResult(printResult)
```

### Receiving Print Streams `onStream` ###

The output from code which prints to stdout can be accessed by registering a 
callback with the `onStream` method. All callbacks registered will be invoked
1 time for every `StreamContent` message received. If a callback is registered
after a `StreamContent` message was received, that callback will *NOT* receive
the message. A callback will only received messages received after it has been 
registered. Examples of stream messages include:

```scala
def printStreamContent(content:StreamContent) = {
    println(s"Stream content was: ${content.text}")
}
client.execute("println(1/1)").onStream(printStreamContent)
client.execute("println(\"Hello, World\")").onStream(printStreamContent)
```

### Handling Errors `onError` ###

When an error occurs during code execution all callbacks registered with 
`onError` will be called exactly once. Example usages include:

```scala
def printError(reply:ExecuteReplyError) = {
    println(s"Error was: ${reply.ename.get}")
}
//  Error from executing a statement
client.execute("1/0").onError(printError)
//  Error from invoking a println
client.execute("println(someUndefinedVar").onError(printError)
```
