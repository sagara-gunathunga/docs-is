
{!fragments/deploying-sample-apps.md!}

### Download the sample

To be able to deploy a WSO2 Identity Server sample, you need to download
it onto your machine first.

Follow the instructions below to download the sample from GitHub.

1. Navigate to [WSO2 Identity Server Samples](https://github.com/wso2/samples-is/releases).

2. Download the `travelocity.com.war` file from the latest release assets.


### Deploy the sample web app

Next, deploy the sample web app on a web container.

1. Extract the `travelocity.com.war` file and open the `travelocity.properties` file located in the `<EXTRACT>/WEB-INF/classes` folder.

2.  Configure the following property with the hostname (`wso2is.local`) that you configured above. 

    ``` 
    #The URL of the SAML 2.0 Assertion Consumer
    SAML2.AssertionConsumerURL=http://wso2is.local:8080/travelocity.com/home.jsp
    ```

3. Next, copy the extracted and modified `travelocity.com.war` folder to the `<TOMCAT_HOME>/webapps` folder.

4.  Start the Tomcat server. 