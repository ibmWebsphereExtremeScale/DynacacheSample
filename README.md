# About The Application
This web application is a simple web application that uses WebSphere eXtreme Scale Liberty Deployment (XSLD) on IBM Container Services (ICS). It persists dynacache data to a XSLD dynamic cache grid. This application will be built and deployed on Bluemix with Liberty custom buildpack. 

# Requirements
- Websphere eXtreme Scale Liberty Deployment (XSLD) 8.6.1.1 (Download the trial version from: https://www.ibm.com/developerworks/downloads/ws/wsdg/index.html)
    - Follow instructions provided to setup your XSLD instance
    - Create a grid of type Dynamic Cache on XSLD
    
# Getting The Code
The folder wxsClient is provided for this sample. It includes the WAR file DynacacheSample.war

# Bluemix Setup
For the purpose of this tutorial, the application will be deployed on Bluemix. To run your application on Bluemix, you must sign up for Bluemix and install the Cloud Foundry command line tool. To sign up for Bluemix, head to https://console.ng.bluemix.net and register.

You can download the Cloud Foundry command line tool by following the steps in https://github.com/cloudfoundry/cli

After you have installed Cloud Foundry command line tool, you need to point to Bluemix by running
```
cf login -a https://api.ng.bluemix.net
```
This will prompt you to login with your Bluemix ID and password.

# Providing Credentials
This application uses a Bluemix user-provided service instance to provide credentials to connect to Websphere eXtreme Scale. For more information visit https://console.ng.bluemix.net/docs/services/reqnsi.html#add_service

We will store credentials in a json file. Create a json file that follows this format. Replace with valid credentials, making sure that you specify all catalog end points(CEPs), seperated by a comma:
```
  {"catalogEndPoint":"<catalog server endpoint:port, eg: 129.11.111.111:4809,129.22.222.222:4809>",
  "gridName":"<grid name>",
  "username":"<username for WXS>",
  "password":"<password for WXS>"}
  
//Save the file as credentials.json
```

To create a user-provided service on Bluemix with the json file you have created, run the following command:

```
cf cups <service-name> -p <path to/credentials.json file>

//Replace <service-name> with any name of your choosing but service name must have 'XSDyna' as the prefix. For example:XSDyna-credentials
```

# Running The Application (UNDER CONSTRUCTION)
Once you have successfully logged in, let's push the WAR file to your Bluemix account with a Java Buildpack

```
cf push <app name> -p DynacacheSample.war -b https://github.com/cloudfoundry/java-buildpack
```

Next, bind the application to the user-provided service created

```
cf bind-service <app name> <service name>
```

Restage the application so changes made will take effect

```
cf restage <app name>
```

# Accessing The Application
To view the application, log onto the Bluemix console ( https://console.ng.bluemix.net ) and click on your deployed application from the dashboard. On the application page Runtime, click the "View App" button, this will launch the app.

# Troubleshooting (INPROGRESS)
If an operation on the WebSphere eXtreme Scale fails, a failure message will be displayed on the application page. These are some suggestions on how you can troubleshoot the problem. Check the application logs - from the Bluemix UI console, click on your application and select 'Logs'. Check for any error messages in the logs.

If there is a connection exception such as:
```
APP/0[err] java.lang.NullPointerException
APP/0Failed to perform loadData on ConfigurationGrid Application may have failed to connect to the grid
```
- Go to the Bluemix console, click on your application. Select 'Runtime', then select the 'Environment variables' tab. Under VCAp services, ensure that the correct information is passed in for credentials (catalogEndPoint, gridName, password, username)
- Check that the name of the user provided service contains the prefix XSDyna
- Check that the grid created is of type 'Dynamic Cache'

If there is a security exception such as:
```
APP/0    Failed to connect to grid
APP/0    [err] javax.cache.CacheException: com.ibm.websphere.objectgrid.ConnectException: CWOBJ1325E: There was a Client security configuration error. The catalog server at endpoint 129.41.233.108:4,809 is configured with SSL. However, the Client does not have SSL configured. The Client SSL configuration is null.
```
- This error indicates that the client application was not configured with SSL but WebSphere eXtreme Scale was configured with SSL

# License
See LICENSE.txt for license information
