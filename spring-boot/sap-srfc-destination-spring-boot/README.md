Spring Boot SAP Synchronouse RFC Destination Endpoint Quick Start  
=======================================================================================================================
**Demonstrates the sap-srfc-destination component running in a spring boot camel runtime.**  
![SAP Tool Suite](../../sap_tool_suite.png "SAP Tool Suite")

* * *
Author: William Collins - Fuse Team  
Level: Beginner  
Technologies: SAP, Camel, Spring  
Summary: This quickstart demonstrates how to configure and use the sap-srfc-destination component in a Spring Boot Camel environment to invoke remote function modules and BAPI methods within SAP. This component invokes remote function modules and BAPI methods within SAP using the *Synchronous RFC* (sRFC) protocol.       
Target Product: Fuse  
Source: <http://github.com/punkhorn/sap-quickstarts/>  

* * *

What is it?  
-----------  

This quick start shows how to integrate Apache Camel with SAP using the JBoss Fuse SAP Synchronous Remote Function Call Destination Camel component. This component and its endpoints should be used in cases where Camel routes require synchronous delivery of requests to and responses from an SAP system.  

This quick start contains a route with an initial timer endpoint which triggers and executes that route once. The route uses a processor bean to build a request to the `GetList` method of the `FlightCustomer` BAPI to retrieve up to 10 Customer records from SAP. The request is routed to a `sap-srfc-destination` endpoint to invoke the BAPI method and receive its response. The route logs to the console the serialized contents of the request and response messages it sends and receives.   

**NOTE** The sRFC protocol used by this component delivers requests and responses to and from an SAP system **BEST-EFFORT**. When the component experiences a communication error when sending a request to or receiving a response from an SAP system, it will be *in doubt* whether the processing of a remote function call in the SAP system was successful. For the guaranteed delivery and processing of requests in an SAP system please see the JBoss Fuse SAP Transactional Remote Function Call Destination Camel component.     

In studying this quick start you will learn:

* How to configure the Camel runtime environment in order to deploy the JBoss Fuse SAP Synchronous Remote Function Call Destination Camel component. 
* How to define a Camel route containing the JBoss Fuse SAP Synchronous Remote Function Call Destination Camel component using the Spring XML syntax.
* How to use the JBoss Fuse SAP Synchronous Remote Function Call Destination Camel component. 
* How to configure connections used by the component.

For more information see:

* <https://access.redhat.com/documentation/en/red-hat-jboss-fuse/6.3/paged/apache-camel-component-reference/chapter-138-sap-component> for more information about the JBoss Fuse SAP Camel components 
* <https://access.redhat.com/documentation/en/red-hat-jboss-fuse/> for more information about using JBoss Fuse

System requirements
-------------------

Before building and running this quick start you will need:

* Maven 3.1.1 or higher
* JDK 1.8
* JBoss Fuse 7.0.0
* SAP JCo3 and IDoc3 libraries (sapjco3.jar, sapidoc3.jar and JCo native library for your OS platform)
* SAP instance with [Flight Data Application](http://help.sap.com/saphelp_erp60_sp/helpdata/en/db/7c623cf568896be10000000a11405a/content.htm) setup.

Configuring the Quickstart for your environment
-----------------------------------------------

To configure the quick start for your environment: 

1. Deploy the JCo3 library jar and native library (for your platform) and IDoc3 library jar to the `lib` folder of the project.
2. Ensure that the **SAP Instance Configuration Configuration Parameters** in the parent pom.xml file (`../../.pom.xml`) of quick starts project has been set to match the connection configuration for your SAP instance.  

Build and Run the Quickstart
----------------------------

To build and run the quick start:

1. Change your working directory to the `sap-srfc-destination-spring-boot` directory.
* Run `mvn clean install` to build the quick start.
* Run `mvn camel:run` to start the Camel runtime.
* In the console observe the response returned by the endpoint.

Stopping the Quickstart
-----------------------

To stop the camel run-time:

1. Enter Ctrl-c in the console.

