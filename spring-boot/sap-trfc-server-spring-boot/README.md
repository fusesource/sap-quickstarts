Spring Boot SAP Transactional RFC Server Endpoint Quick Start
=======================================================================================================================
**Demonstrates the sap-trfc-server component running in a spring boot camel runtime.**  
![SAP Tool Suite](../../sap_tool_suite.png "SAP Tool Suite")

***  
Author: William Collins - Fuse Team  
Level: Beginner  
Technologies: SAP, Camel, Spring  
Summary: This quickstart demonstrates how to configure and use the sap-trfc-server component in a spring boot Camel environment to handle a remote function call from SAP. This component handles a remote function call from SAP using the *Transactional RFC* (tRFC) protocol.       
Target Product: Fuse  
Source: <http://github.com/punkhorn/sap-quickstarts/>  

***  

What is it?  
-----------  

This quick start shows how to integrate Apache Camel with SAP using the JBoss Fuse SAP Transactional Remote Function Call Server Camel component. This component and its endpoints should be used in cases where the sending SAP system requires **AT-MOST-ONCE** delivery of its requests to a Camel route. To accomplish this the sending SAP system generates a *transcation ID* (*tid*) which accompanies every request it sends to the component's endpoints. The sending SAP system will first check with the component whether a given tid has been received by it before sending a series of requests associated with the tid. The component will check a list of received tids it maintains, record the sent tid if it is not in that list and then respond to the sending SAP system with whether the tid had already been recorded. The sending SAP system will only then send the series of requests if the tid has not been previously recorded. This enables a sending SAP system to reliably send a series of requests once to a camel route. 

This quick start handles requests from SAP for the `CreateFromData` method of the `FlightCustomer` BAPI to create flight customer records in the Flight Data Application. The route of this quick start simply mocks the behavior of this method by logging the requests it receives. The `sap-trfc-server` endpoint at the beginning of the route consumes a request from SAP and its contents is placed into the message body of the exchange's message. The request is then logged to the console. 

**NOTE:** The tRFC protocol used by this component is asynchronous and does not return a response and thus the endpoints of this component do not return a response message.  

**NOTE:** The sending SAP system will send a request using the tRFC protocol to the compponent's endpoint only when invoking the endpoint with the **IN BACKGROUND TASK** option within SAP. These calls are stored in the SAP database and are not sent until the local transaction within SAP is committed.   

**NOTE:** If the endpoints of this component receive a request from SAP using the sRFC protocol, the request will be processed as in the JBoss Fuse SAP Synchronous Remote Function Call Server Camel component however a response will not be sent back to the calling SAP system.    

**NOTE:** This component does not guarantee that a series of requests sent through its endpoints are handled and processed in the receiving Camel route in the same order that they were sent. The delivery and processing order of these requests may differ on the receiving Camel route due to communication errors and resends of a document list. This component only guarantees that each request is processed **AT-MOST-ONCE**. To guarantee the delivery **and** processing order of a series of requests from SAP it is incumbent upon the sending SAP system to serialize its requests to an **outbound queue** when sending them to this component to achieve **IN-ORDER** delivery and processing guarantees.  

In studying this quick start you will learn:

* How to configure the Camel runtime environment in order to deploy the JBoss Fuse SAP Transactional Remote Function Call Server Camel component. 
* How to define a Camel route containing the JBoss Fuse SAP Transactional Remote Function Call Server Camel component using the Spring XML syntax.
* How to use the JBoss Fuse SAP Transactional Remote Function Call Server Camel component to handle requests from SAP. 
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
3. Ensure the destination `QUICKSTART` has been defined in your SAP instance:   
	a. Using the SAP GUI, run transaction `SM59` (RFC Destinations).    
    b. Create a new destination (Edit > Create):  
		1. **RFC Destination** : `QUICKSTART`.    
        2. **Connection Type** : `T`.    
        3. **Technical Settings** :    
            i. **Activation Type** : `Registered Server Program`.    
            ii.**Program ID** : `QUICKSTART`.   
        4. **Unicode**:   
        	i. **Communication Type with Target System** : `Unicode`   
4. Ensure the following `ZBAPI_FLCUST_CREATEFROMDATA` ABAP program is installed and activated in your SAP client:  

		*&---------------------------------------------------------------------*
		*& Report  ZBAPI_FLCUST_CREATEFROMDATA
		*&
		*&---------------------------------------------------------------------*
		*&
		*&
		*&---------------------------------------------------------------------*
		
		REPORT  ZBAPI_FLCUST_CREATEFROMDATA.
		
		
		DATA: RFCDEST LIKE RFCDES-RFCDEST VALUE 'NONE'.
		
		
		DATA: RFC_MESS(128).
		
		
		DATA: CUSTOMER_DATA LIKE BAPISCUNEW.
		
		RFCDEST = 'QUICKSTART'.
		
		CUSTOMER_DATA-CUSTNAME = 'Fred Flintstone'.
		CUSTOMER_DATA-FORM = 'Mr.'.
		CUSTOMER_DATA-STREET = '123 Rubble Lane'.
		CUSTOMER_DATA-POSTCODE = '01234'.
		CUSTOMER_DATA-CITY = 'Bedrock'.
		CUSTOMER_DATA-COUNTR = 'US'.
		CUSTOMER_DATA-PHONE = '800-555-1212'.
		CUSTOMER_DATA-EMAIL = 'fred@bedrock.com'.
		CUSTOMER_DATA-CUSTTYPE = 'P'.
		CUSTOMER_DATA-DISCOUNT = '005'.
		CUSTOMER_DATA-LANGU = 'E'.
		
		CALL FUNCTION 'BAPI_FLCUST_CREATEFROMDATA'
		  IN BACKGROUND TASK
		  DESTINATION RFCDEST
		  EXPORTING
		    CUSTOMER_DATA = CUSTOMER_DATA.
		
		CUSTOMER_DATA-CUSTNAME = 'Barney Rubble'.
		CUSTOMER_DATA-FORM = 'Mr.'.
		CUSTOMER_DATA-STREET = '123 Pebble Road'.
		CUSTOMER_DATA-POSTCODE = '98765'.
		CUSTOMER_DATA-CITY = 'Flagstone'.
		CUSTOMER_DATA-COUNTR = 'US'.
		CUSTOMER_DATA-PHONE = '800-555-1313'.
		CUSTOMER_DATA-EMAIL = 'barney@flagstone.com'.
		CUSTOMER_DATA-CUSTTYPE = 'P'.
		CUSTOMER_DATA-DISCOUNT = '005'.
		CUSTOMER_DATA-LANGU = 'E'.
		
		CALL FUNCTION 'BAPI_FLCUST_CREATEFROMDATA'
		  IN BACKGROUND TASK
		  DESTINATION RFCDEST
		  EXPORTING
		    CUSTOMER_DATA = CUSTOMER_DATA.
		
		IF SY-SUBRC NE 0.
		
		WRITE: / 'Call ZBAPI_FLCUST_CREATEFROMDATA SY-SUBRC = ', SY-SUBRC.
		WRITE: / RFC_MESS.
		
		ENDIF.
		
		COMMIT WORK.

Build and Run the Quickstart
----------------------------

To build and run the quick start:

1. Change your working directory to the `sap-trfc-server-spring-boot` directory.
* Run `mvn clean install` to build the quick start.
* Run `mvn spring-boot:run` to start the Camel runtime.
* Invoke the camel route from SAP:  Run the `ZBAPI_FLCUST_CREATEFROMDATA` program.  
* In the console observe the requests received by the endpoint.  

Stopping the Quickstart
-----------------------

To stop the camel run-time:

1. Enter Ctrl-c in the console.

