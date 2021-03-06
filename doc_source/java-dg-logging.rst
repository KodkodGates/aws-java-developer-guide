.. Copyright 2010-2016 Amazon.com, Inc. or its affiliates. All Rights Reserved.

   This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0
   International License (the "License"). You may not use this file except in compliance with the
   License. A copy of the License is located at http://creativecommons.org/licenses/by-nc-sa/4.0/.

   This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
   either express or implied. See the License for the specific language governing permissions and
   limitations under the License.

########################
Logging |sdk-java| Calls
########################

The |sdk-java| is instrumented with `Apache Commons Logging
<http://commons.apache.org/proper/commons-logging/guide.html>`_, which is an abstraction layer that
enables the use of any one of a number of logging systems at runtime. Supported logging systems
include the Java Logging Framework and Apache Log4j, among others. This topic explains how to use
Log4j. You can learn more about Log4j on the `Log4j <http://logging.apache.org/log4j/2.x/>`_ page at
the `Apache website <http://www.apache.org/>`_. You can use the SDK's logging functionality without
making any changes to your application code.

.. note:: This topic focuses on Log4j 1.x. Log4j2 doesn't directly support Apache Commons Logging, but
    provides an adapter that directs logging calls automatically to Log4j2 using the Apache Commons
    Logging interface. For more information, see: `Commons Logging Bridge
    <http://logging.apache.org/log4j/2.x/log4j-jcl/index.html>`_ in the Log4j2 documentation.

In order to use Log4j with the SDK, you need to download the Log4j jar from the Apache website. The
jar is not included in the SDK. Copy the jar file to a location that is on your classpath.

Log4j uses a configuration file, log4j.properties. Example configuration files are shown below. Copy
this configuration file to a directory on your classpath. The Log4j jar and the log4j.properties
file do not have to be in the same directory.

The log4j.properties configuration file specifies properties such as `logging level
<http://logging.apache.org/log4j/2.x/manual/configuration.html#Loggers>`_, where logging output is
sent (such as `to a file or to the console
<http://logging.apache.org/log4j/2.x/manual/appenders.html>`_), and the `format of the output
<http://logging.apache.org/log4j/2.x/manual/layouts.html>`_. The logging level is the granularity of
output that the logger generates. Log4j supports the concept of multiple logging
:emphasis:`hierarchies`. The logging level is set independently for each hierarchy. The following
two logging hierarchies are available in the SDK.

*   log4j.logger.com.amazonaws

*   log4j.logger.org.apache.http.wire

.. _sdk-net-logging-classpath:

Setting the Classpath
=====================

Both the Log4j jar and the log4j.properties file must be located on your classpath. If you are using
`Apache Ant <http://ant.apache.org/manual/>`_, set the classpath in the :code:`path` element in your
Ant file. Here is an example path element from the Ant file for the Amazon S3 example included with
the SDK:

.. code-block:: xml

    <path id="aws.java.sdk.classpath">
      <fileset dir="../../third-party" includes="**/*.jar"/>
      <fileset dir="../../lib" includes="**/*.jar"/>
      <pathelement location="."/>
    </path>

If you are using the Eclipse IDE, you can set the classpath by opening the menu and navigating to
:guilabel:`Project` | :guilabel:`Properties` | :guilabel:`Java Build Path`.


.. _sdk-net-logging-service:

Service-Specific Errors and Warnings
====================================

We recommend that you always leave the "com.amazonaws" logger hierarchy set to "WARN" in order to
catch any important messages from the client libraries. For example, if the Amazon S3 client detects
that your application hasn't properly closed an :code:`InputStream` and could be leaking resources,
it will report it through a warning message to the logs. This will also ensure that messages are
logged if the client has any problems handling requests or responses.

The following log4j.properties file sets the :code:`rootLogger` to WARN, which will cause warning
and error messages from all loggers in the "com.amazonaws" hierarchy to be included. Alternatively,
you can explicitly set the com.amazonaws logger to WARN.

.. code-block:: properties

    log4j.rootLogger=WARN, A1
    log4j.appender.A1=org.apache.log4j.ConsoleAppender
    log4j.appender.A1.layout=org.apache.log4j.PatternLayout
    log4j.appender.A1.layout.ConversionPattern=%d [%t] %-5p %c -  %m%n
    # Or you can explicitly enable WARN and ERROR messages for the AWS Java clients
    log4j.logger.com.amazonaws=WARN


.. _sdk-net-logging-request-response:

Request/Response Summary Logging
================================

Every request to an AWS service generates a unique AWS request ID that is useful if you run into an
issue with how an AWS service is handling a request. AWS request IDs are accessible programmatically
through Exception objects in the SDK for any failed service call, and can also be reported through
the DEBUG log level in the "com.amazonaws.request" logger.

The following log4j.properties file enables a summary of requests and responses, including AWS
request IDs.

.. code-block:: properties

    log4j.rootLogger=WARN, A1
    log4j.appender.A1=org.apache.log4j.ConsoleAppender
    log4j.appender.A1.layout=org.apache.log4j.PatternLayout
    log4j.appender.A1.layout.ConversionPattern=%d [%t] %-5p %c -  %m%n
    # Turn on DEBUG logging in com.amazonaws.request to log
    # a summary of requests/responses with AWS request IDs
    log4j.logger.com.amazonaws.request=DEBUG

Here is an example of the log output:

.. code-block:: none

    2009-12-17 09:53:04,269 [main] DEBUG com.amazonaws.request - Sending Request: POST https://rds.amazonaws.com / Parameters: (MaxRecords: 20, Action: DescribeEngineDefaultParameters, SignatureMethod: HmacSHA256, AWSAccessKeyId: ACCESSKEYID, Version: 2009-10-16, SignatureVersion: 2, Engine: mysql5.1, Timestamp: 2009-12-17T17:53:04.267Z, Signature: 4ydexGGkC77PovHhbfzAMA1H0nDnqIQxG9q+Yq3uw5s=, ) 2009-12-17 09:53:04,464 [main] DEBUG com.amazonaws.request - Received successful response: 200, AWS Request ID: 06c12a39-eb35-11de-ae07-adb69edbb1e4 2009-12-17 09:53:04,469 [main] DEBUG com.amazonaws.request - Sending Request: POST https://rds.amazonaws.com / Parameters: (ResetAllParameters: true, Action: ResetDBParameterGroup, SignatureMethod: HmacSHA256, DBParameterGroupName: java-integ-test-param-group-1261072381023, AWSAccessKeyId: ACCESSKEYID, Version: 2009-10-16, SignatureVersion: 2, Timestamp: 2009-12-17T17:53:04.467Z, Signature: 9WcgfPwTobvLVcpyhbrdN7P7l3uH0oviYQ4yZ+TQjsQ=, )
    2009-12-17 09:53:04,646 [main] DEBUG com.amazonaws.request - Received successful response: 200, AWS Request ID: 06e071cb-eb35-11de-81f7-01604e1b25ff


.. _sdk-net-logging-verbose:

Verbose Wire Logging
====================

In some cases, it may be useful to see the exact requests and responses being sent and received by
the AWS SDK for Java. This logging should not be enabled in production systems since writing out
large requests (e.g., a file being uploaded to Amazon S3) or responses can significantly slow down
an application. If you really need access to this information, you can temporarily enable it through
the Apache HttpClient 4 logger. Enabling the DEBUG level on the :code:`apache.http.wire` logger
enables logging for all request and response data.

The following log4j.properties file turns on full wire logging in Apache HttpClient 4 and should
only be turned on temporarily since it can have a significant performance impact on your
application.

.. code-block:: properties

    log4j.rootLogger=WARN, A1
    log4j.appender.A1=org.apache.log4j.ConsoleAppender
    log4j.appender.A1.layout=org.apache.log4j.PatternLayout
    log4j.appender.A1.layout.ConversionPattern=%d [%t] %-5p %c -  %m%n
    # Log all HTTP content (headers, parameters, content, etc)  for
    # all requests and responses. Use caution with this since it can
    # be very expensive to log such verbose data!
    log4j.logger.org.apache.http.wire=DEBUG


