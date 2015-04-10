<properties 
	pageTitle="How to create web tests" 
	description="Learn how to create web tests in Azure." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="awills"/>

# Web tests for Web Apps
Is your web app in Microsoft Azure Web Services still running? Is it responding properly, and fast enough? Tests your website at regular intervals by configuring a web test. If the site goes down or responds slowly or incorrectly, you'll receive an email alert. And you'll get graphs showing its availability and responsiveness over time.  

*Want to test any other website? Use [Application Insights web tests][availability] for non-Azure web apps.*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

You can set up availability monitoring for any Web App that is using a Basic or Standard plan.  You can create up to 3 Web tests and run each of these tests from up to 3 geographic locations. You don't have to alter the web app in any way.

You can also pause web tests during deployments or known outages so your overall availability isn't affected.  Overall availability is calculated over all the web tests including the different locations selected.

## How to set up a web test
1. To configure a web test, first ensure your web app is either **Basic** or **Standard**.
2. Then, choose the **Web Test** part on the **Web app** blade:  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. In the **Create web test** blade, name the web test and specify the URL to run the test against.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. Then choose up to 3 out of the 8 locations
5. Specify the criteria for success including HTTP status code checks or string contains on the site itself.
6. Then choose alert settings including the sensitivity and who to email.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - High sensitivity will create an alert whenever a test failure is detected in just 1 location.
    - Medium sensitivity requires at least half of the locations have seen a failure in 10 minutes.
    - Low sensitivity requires that the test at all locations have failed within 15 minutes.

Once you are done click in the **Create** button. After your web test can been created, it will execute every 5 mins from the location(s) specified, so it may take a little while for the data to show up.

### What are the locations about?
We send a request to the website from those locations, in the same way that users will access the site from different parts of the world. If your site becomes unavailable in the USA but still available in Europe, you'll know that the problem is a network issue, rather than in your server.

### Using Success Criteria
Typically you'll want to test that the HTTP status code equals 200, which signals that the server recognized the URI and returned a page.

You cannot use wildcards in the content match string, but you can test any plain text.

## Uh oh - my web app is down!
If your web test doesn't pass the success criteria then it will be marked as a failed test and reduce the overall availability for your web app. Failed tests (As well as successful tests) are shown on a scatter chat on the specific web test blade.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

Failed tests can be analyzed to determine why they failed.  Drill into a failed web test and download and open Visual Studio Web Test Result File to analyze and understand why the test failed.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)


## Get more Application Insights

* [Monitor usage][azure-usage] to find out how many users you have, how often they visit, and how the pages perform on their browsers
* [Monitor performance][azure-perf] to diagnose issues with your code's dependencies


[AZURE.INCLUDE [app-insights-azure-learn-more](../includes/app-insights-azure-learn-more.md)]

