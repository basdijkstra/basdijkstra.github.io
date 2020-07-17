---
id: 229
title: Generating and deploying web service stubs using WSO2
date: 2013-11-07T08:16:20+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=229
permalink: /generating-and-deploying-web-service-stubs-using-wso2/
categories:
  - Service virtualization
tags:
  - java
  - service virtualization
  - wso2
---
In case you need a relatively simple stub that simulates a third-party web service or application during your testing process, you have several options at your disposal. In this article, I will introduce the WSO2 Enterprise Service Bus, which is one of these options, and show you how you can use it to quickly simulate an existing web service. This is particularly useful when the actual web service or application is not (always) available during testing, or when you want to simulate particular behavior in your testing process.

**The WSO2 Enterprise Service Bus**  
The WSO2 Enterprise Service Bus is a lightweight, open source ESB implementation that has gained significant popularity in recent years. It is used by companies such as Ebay, British Airways and Volvo. Downloading and installation is simple: go to [the product website](http://wso2.com/products/enterprise-service-bus/) and download a zip file from there. Unpack it and run _wso2server.bat_ (Windows) or _wso2server.sh_ (Linux) from the _/bin_ directory. Note that you need a Java JDK to be installed for WSO2 ESB to run on your system.

**Developing WSO2 stubs in Eclipse**  
As we are going to generate our own web service stub and deploy it using WSO2, it is a good idea to do all the work from within our IDE. I prefer to use Eclipse for this. Stop WSO2 using Ctrl+C as we are going to restart it from within Eclipse again later on. Start Eclipse and install the WSO2 Developer Studio using the Eclipse Marketplace.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_install_wso2_developer_studio.png" alt="stub_install_wso2_developer_studio" width="583" height="314" class="aligncenter size-full wp-image-232" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_install_wso2_developer_studio.png 583w, https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_install_wso2_developer_studio-300x161.png 300w" sizes="(max-width: 583px) 100vw, 583px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_install_wso2_developer_studio.png)

**Generating a web service skeleton**  
For this example, I used a sample web service providing weather forecasts for cities in the US. Its WSDL is available [here](http://wsf.cdyne.com/WeatherWS/Weather.asmx?wsdl). In order to be able to generate a stub for this web service using WSO2, you should download it to your local machine.

In Eclipse, choose File > New > Project &#8230; > WSO2 > Service Hosting > Project Types > Axis2 Service Project. Next, select &#8216;Create New Axis2 Service Using WSDL File&#8217; (this is why we need a copy of the WSDL on our system). Select the WSDL file and give your project a name:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_generate_skeleton_from_wsdl.png" alt="stub_generate_skeleton_from_wsdl" width="681" height="502" class="aligncenter size-full wp-image-233" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_generate_skeleton_from_wsdl.png 681w, https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_generate_skeleton_from_wsdl-300x221.png 300w" sizes="(max-width: 681px) 100vw, 681px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_generate_skeleton_from_wsdl.png)

Click finish to generate a web service skeleton for the service described in the WSDL.

**Deploying the web service**  
To deploy our web service and interact with it, we need to carry out two steps. First, create a Carbon Application Project in Eclipse. This project bundles our web service project so it can be deployed. Select File > New &#8230; > Carbon Application Project, give the project a name, select our generated service as a dependency and click Finish.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_create_carbon_project.png" alt="stub_create_carbon_project" width="714" height="636" class="aligncenter size-full wp-image-235" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_create_carbon_project.png 714w, https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_create_carbon_project-300x267.png 300w" sizes="(max-width: 714px) 100vw, 714px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_create_carbon_project.png)

Next, we need to create a server in Eclipse on which to deploy the web service. Add a new server and choose WSO2 > WSO2 Carbon 4.0 based server as the server type. Click Next and select the installation directory of WSO2 as your CARBON_HOME folder. Click Next twice and add the Carbon Application project for our web service to the web server in the &#8216;Add and Remove&#8217; window (this can be done later as well by right-clicking on the server and selecting &#8216;Add and Remove&#8217;). Finally, start the server.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_service_deployed.png" alt="stub_service_deployed" width="426" height="76" class="aligncenter size-full wp-image-237" srcset="https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_service_deployed.png 426w, https://www.ontestautomation.com/wp-content/uploads/2013/11/stub_service_deployed-300x53.png 300w" sizes="(max-width: 426px) 100vw, 426px" />](http://www.ontestautomation.com/wp-content/uploads/2013/11/stub_service_deployed.png)

**Testing the web service**  
Now that our web service skeleton has been deployed, let&#8217;s see whether we can communicate with it. The WSDL for the local web service implementation can be found using the WSO2 Management Console (user: admin, pass: admin) that is automatically opened when you start the server in Eclipse. You can then use a tool such as SmartBear SoapUI or Parasoft SOAtest to test the web service skeleton.

By default, none of the operations in the web service will be implemented yet, and sending a request message will result in a response containing a SOAP Fault:

<pre class="brush: xml; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"&gt;
 &lt;soapenv:Body&gt;
  &lt;soapenv:Fault&gt;
   &lt;faultcode&gt;soapenv:Server&lt;/faultcode&gt;
   &lt;faultstring&gt;Please implement com.cdyne.ws.weatherws.WeatherSkeleton#getCityForecastByZIP&lt;/faultstring&gt;
   &lt;detail/&gt;
  &lt;/soapenv:Fault&gt;
 &lt;/soapenv:Body&gt;
&lt;/soapenv:Envelope&gt;</pre>

The good news is that we have successfully created a web service skeleton from the WSDL web service description, that we deployed it on a local web server for testing purposes and that we are able to communicate with the web service, without having to write a single line of code.

**Implementing the web service operations**  
In order for our web service simulation to return more intelligent responses, we will have to implement the methods that construct the actual responses. When you generate a web service skeleton using the method and example WSDL above, these methods will all be located in the _WeatherSkeleton.java_ file. After generating the skeleton, the only action performed by each method is to throw the exception message we have seen when we first communicated with the web service:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public com.cdyne.ws.weatherws.GetCityForecastByZIPResponse getCityForecastByZIP (com.cdyne.ws.weatherws.GetCityForecastByZIP getCityForecastByZIP) {
            //TODO : fill this with the necessary business logic
            throw new  java.lang.UnsupportedOperationException("Please implement " + this.getClass().getName() + "#getCityForecastByZIP");
}</pre>

A simple implementation of this method, generating the same response no matter what is in the request message,is shown below:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public GetCityForecastByZIPResponse getCityForecastByZIP(GetCityForecastByZIP getCityForecastByZIP) {
	 
	 // generate new response for the web service
	 try {
		 GetCityForecastByZIPResponse cityForecastResponse = GetCityForecastByZIPResponse.class.newInstance();
		 
		 // create a new response object and the required objects to fill it
		 ForecastReturn fr = new ForecastReturn();
		 ArrayOfForecast aof = new ArrayOfForecast();
		 Forecast forecast = new Forecast();
		 Calendar cal = Calendar.getInstance();
		 Temp temp = new Temp();
		 POP pop = new POP();
		 
		 // set field values
		 fr.setCity("Boulder");
		 fr.setState("Colorado");
		 fr.setWeatherStationCity("Boulder");
		 fr.setResponseText("Forecast for Boulder, generated on " + new SimpleDateFormat("MM/dd/yyyy HH:mm:ss").format(cal.getTime()));
		 fr.setSuccess(true);
		 
		 // create weather forecast array and forecast entry
		 forecast.setDate(cal);
		 forecast.setDesciption("Sample forecast");
		 temp.setDaytimeHigh("90");
		 temp.setMorningLow("40");
		 forecast.setTemperatures(temp);
		 pop.setDaytime("12:00:00");
		 pop.setNighttime("00:00:00");
		 forecast.setProbabilityOfPrecipiation(pop);
		 
		 // add entry to forecast array
		 aof.addForecast(forecast);
		 
		 // add forecast to response and return it
		 fr.setForecastResult(aof);
		 cityForecastResponse.setGetCityForecastByZIPResult(fr);
		 return cityForecastResponse;
		 
	 } catch (Exception e) {
		 throw new UnsupportedOperationException("Unexpected error occurred during message processing");
	 }
 }</pre>

When we redeploy our web service (by right-clicking on the server in Eclipse and selecting &#8216;Redeploy&#8217;) and call the GetCityForecastByZIP operation again, we now get a sensible response from our web service:

<pre class="brush: xml; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;soapenv:Envelope
   xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"&gt;
 &lt;soapenv:Body&gt;
  &lt;ns1:GetCityForecastByZIPResponse xmlns:ns1="http://ws.cdyne.com/WeatherWS/"&gt;
   &lt;ns1:GetCityForecastByZIPResult&gt;
    &lt;ns1:Success&gt;true&lt;/ns1:Success&gt;
    &lt;ns1:ResponseText&gt;Forecast for Boulder, generated on 11/07/2013 09:02:54&lt;/ns1:ResponseText&gt;
    &lt;ns1:State&gt;Colorado&lt;/ns1:State&gt;
    &lt;ns1:City&gt;Boulder&lt;/ns1:City&gt;
    &lt;ns1:WeatherStationCity&gt;Boulder&lt;/ns1:WeatherStationCity&gt;
    &lt;ns1:ForecastResult&gt;
     &lt;ns1:Forecast&gt;
      &lt;ns1:Date&gt;2013-11-07T09:02:54.274+01:00&lt;/ns1:Date&gt;
      &lt;ns1:WeatherID&gt;0&lt;/ns1:WeatherID&gt;
      &lt;ns1:Desciption&gt;Sample forecast&lt;/ns1:Desciption&gt;
      &lt;ns1:Temperatures&gt;
       &lt;ns1:MorningLow&gt;40&lt;/ns1:MorningLow&gt;
       &lt;ns1:DaytimeHigh&gt;90&lt;/ns1:DaytimeHigh&gt;
      &lt;/ns1:Temperatures&gt;
      &lt;ns1:ProbabilityOfPrecipiation&gt;
       &lt;ns1:Nighttime&gt;00:00:00&lt;/ns1:Nighttime&gt;
       &lt;ns1:Daytime&gt;12:00:00&lt;/ns1:Daytime&gt;
      &lt;/ns1:ProbabilityOfPrecipiation&gt;
     &lt;/ns1:Forecast&gt;
    &lt;/ns1:ForecastResult&gt;
   &lt;/ns1:GetCityForecastByZIPResult&gt;
  &lt;/ns1:GetCityForecastByZIPResponse&gt;
 &lt;/soapenv:Body&gt;
&lt;/soapenv:Envelope&gt;</pre>

In future posts, I will introduce ways to improve the functionality and flexibility of our simulated web service, and make it even more useful when used in our testing processes.