# About The Application
This web application is a simple web application that uses WebSphere eXtreme Scale Liberty Deployment (XSLD) on IBM Container Services (ICS). It persists dynacache data to a XSLD dynamic cache grid. This application will be built and deployed on Bluemix with Liberty custom buildpack. 

# Requirements
- Websphere eXtreme Scale (ibm-websphere-extreme-scale) image on IBM Bluemix Container Services will be used for this sample 
    - For documentation on setting up ibm-websphere-extreme-scale image for Bluemix, visit: https://console.ng.bluemix.net/docs/images/docker_image_extreme_scale/ibm-websphere-extreme-scale_starter.html
    - Create a grid of type Dynamic Cache on XSLD
    
# Getting The Code
The folder wxsClient is provided for this sample. It includes the WAR file DynacacheSample.war

# Bluemix Setup
To run your application on Bluemix, you must sign up for Bluemix and install the Cloud Foundry command line tool. To sign up for Bluemix, head to https://console.ng.bluemix.net and register.

You can download the Cloud Foundry command line tool by following the steps in https://github.com/cloudfoundry/cli

After you have installed Cloud Foundry command line tool, you need to point to Bluemix by running
```
cf login -a https://api.ng.bluemix.net
```
This will prompt you to login with your Bluemix ID and password.

# Providing Credentials
This application uses a Bluemix user-provided service instance to provide credentials to connect to Websphere eXtreme Scale. For more information visit https://console.ng.bluemix.net/docs/services/reqnsi.html#add_service

We will store credentials in a json file. Create a json file that follows this format. Replace with valid credentials, making sure that you specify all catalog end points (CEPs), seperated by a comma:
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

//Replace <service-name> with any name of your choosing but service name must have 'XSDyna' as the prefix.
//For example: XSDyna-credentials
```

# Running The Application
Once you have successfully logged in, let's push the WAR file to your Bluemix account with the Liberty Buildpack (by default)

```
cf push <app name> -p DynacacheSample.war
```

Next, bind the application to the user-provided service created

```
cf bind-service <app name> <service name>
```

To allow communication between your application and the Websphere eXtreme Scale (WXS) containers on ICS, set the Bluemix HOSTSALIAS environment variables
```
cf set-env <app name> HOSTSALIAS_wxs1 111.11.111.11
cf set-env <app name> HOSTSALIAS_wxs2 222.22.222.22

//Replace wxs1 and wxs2 with the alias names you call when you start up WXS containers 
(the prefix should remain HOSTSALIAS_)
//the IPs are the public assigned IPs for your WXS containers. 
```

Restage the application so changes made will take effect

```
cf restage <app name>
```

# Accessing The Application
Log onto ACE console: https://console.ng.bluemix.net and find your application on the dashboard. Clicking on the link provided will launch your sample application.
- Use the sample application to load entries to the Dynamic caching grid
- Verify, update, invalidate entries


# Troubleshooting
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
