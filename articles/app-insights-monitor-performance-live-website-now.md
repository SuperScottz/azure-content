<properties 
	pageTitle="Diagnose performance issues on a running website" 
	description="Monitor a website's performance without re-deploying it. Use standalone or with Application Insights SDK" 
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
	ms.date="03/30/2015" 
	ms.author="awills"/>
 

# Install Application Insights Status Monitor to monitor website performance

*Application Insights is in preview.*

Got an ASP.NET web application that's misbehaving? Diagnose exceptions and performance issues quickly without rebuilding or redeploying it. Install the Application Insights Status Monitor in the server, and you'll be able to find performance issues and get stack traces for any exceptions.

Status Monitor can be used either on its own, or as well as [adding Application Insights SDK][greenbrown] to your code. When used on its own Status Monitor will download the latest Application Insights SDK and apply it to your application. 

Status Monitor works for ASP.NET applications hosted on an IIS server. It enables your application to monitor calls to external dependencies such as SQL databases, other HTTP endpoints and Azure storage accounts, by turning on the code instrumentation feature for all web applications running on computer. It also ensures sufficient permissions of your application identity so that windows performance counters can be collected by the Application Insights SDK.

Beside enabling monitoring, Status Monitor helps troubleshoot issues with Application Insights SDK and shows you the status of monitoring of all web applications running on the computer. 


*Are you using an [Azure VM](http://azure.microsoft.com/documentation/services/virtual-machines/) or [Azure Web App](websites-learning-map.md)? Just [add the monitor from the control panel](insights-perf-analytics.md).*


## Install Application Insights Status Monitor on your web server

1. You need a [Microsoft Azure account](http://azure.com) account. Your organization might have a group account, or you could sign up for a Pay-as-you-go account. While it's in Beta, Application Insights is a free service.

1. On your web server, login with administrator credentials.
2. Download and run the [Status Monitor installer](http://go.microsoft.com/fwlink/?LinkId=506648).

4. In the installation wizard, sign in to Microsoft Azure.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. Pick the installed web application or website that you want to monitor, then configure the name under which you want to see the results in the Application Insights portal. .

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normally, you should choose to configure a new resource.

    You might use an existing resource if, for example, if you already set up [web tests][availability] for your site.  

6. Restart IIS.
    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

6. Notice that ApplicationInsights.config has been inserted into the websites that you want to monitor.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   There are also some changes to web.config.

### Want to (re)configure later?

After you complete the wizard, you can re-configure the agent whenever you want. You can also use this if you installed the agent but there was some trouble with the initial setup.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## View performance telemetry

Open [the Azure Preview portal](http://portal.azure.com), browse Application Insights and open the resource that you created.

![](./media/appinsights/appinsights-08openApp.png)

You'll see data under Diagnostics.

![](./media/appinsights/appinsights-038-diagnostics.png)

Click through any chart to see more details.


![](./media/appinsights/appinsights-038-dependencies.png)

#### Dependencies

The charts labeled HTTP, SQL, AZUREBLOB show the response times and counts of calls to dependencies: that is, external services that your application uses.



#### Performance counters

Click any performance counter chart to change what it shows. Or you can add a new chart. 
 
#### Exceptions

![Click through the server exceptions chart](./media/appinsights/appinsights-039-1exceptions.png)

You can drill down to specific exceptions (from the last seven days) and get stack traces and context data.


### No telemetry?

  * Use your site, to generate some data.
  * Wait a few minutes to let the data arrive, then click Refresh.
  * Open Status Monitor and select your application on left pane. Check if there are any diagnostics messages for this application in the "Configuration notifications" section:
  
  ![](.\media\app-insights-monitor-performance-live-website-now\appinsights-status-monitor-diagnostics-message.png)

  * Make sure your server firewall allows outgoing traffic on port 443 to dc.services.visualstudio.com. 
  * If on the server you see a message about "insufficient permissions":
  * In IIS Manager, select your application pool, open Advanced Settings, and under Process Model note the Identity.
  * In Computer management control panel, add this identity to the Performance Monitor Users group.
  * See [Troubleshooting][qna].

## System Requirements

OS support for Application Insights Status Monitor on Server: 

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows server 2012 R2

with latest SP and .NET Framework 4.0 and 4.5

On the client side Windows 7, 8 and 8.1, again with .NET Framework 4.0 and 4.5

IIS support is: IIS 7, 7.5, 8, 8.5
(IIS is required)

## <a name="next"></a>Next steps

* [Create web tests][availability] to make sure your site stays live.
* [Search events and logs][diagnostic] to help diagnose problems.
* [Add web client telemetry][usage] to see exceptions from web page code and to let you insert trace calls.
* [Add Application Insights SDK to your web service code][greenbrown] so that you can insert trace and log calls in the server code.

## Video

[AZURE.VIDEO app-insights-performance-monitoring]

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



