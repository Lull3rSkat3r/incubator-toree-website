---
layout: docpage
title: Overview of Project
type: doc
section: quick-start
weight: 0
tagline: Apache Project !
---


The Spark Kernel has one main goal: provide the foundation for interactive applications to connect and use [Apache Spark][1].

![](overview.png)

The kernel provides several key features for applications:

1. Define and run Spark Tasks

    - Executing Scala code dynamically in a similar fashion to the _Scala REPL_ and _Spark Shell_

    - Plug points for accessing Spark-only objects including an instance of the [Spark Context][2]

2. Collect Results without a Datastore

    - Send execution results and streaming data back via the Spark Kernel to your applications

    - Use the [Comm API][3] - an abstraction of the IPython protocol - for more detailed data 
      communication and synchronization between your applications and the Spark Kernel

3. Host and Manage Applications Separately from Apache Spark

    - The _Spark Kernel_ serves as a proxy for requests to the Apache Spark cluster

The project intends to provide applications with the ability to send both packaged jars and code snippets. As it implements the latest IPython message protocol (5.0), the Spark Kernel can easily plug into the 3.x branch of IPython for quick, interactive data exploration. The Spark Kernel strives to be extensible, providing a [pluggable interface][4] for developers to add their own functionality.

__For more information, please visit the [Spark Kernel wiki][5].__

__For bug reporting and feature requests, please visit the [Spark Kernel issue list][6].__

[1]: https://spark.apache.org/
[2]: http://spark.apache.org/docs/latest/api/scala/org/apache/spark/SparkContext.html
[3]: Guide-to-the-Comm-API-of-the-Spark-Kernel-and-Spark-Kernel-Client
[4]: Guide-to-Developing-Magics-for-the-Spark-Kernel
[5]: https://github.com/ibm-et/spark-kernel/wiki
[6]: https://github.com/ibm-et/spark-kernel/issues
