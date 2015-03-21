<properties 
	pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" 
	description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Add authentication to your Mobile Services app

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

This topic shows you how to authenticate users in Azure Mobile Services from your universal Windows app. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Mobile Services. After being successfully authenticated and authorized by Mobile Services, the user ID value is displayed.

This tutorial walks you through these basic steps to enable authentication in your app:

1. [Register your app for authentication and configure Mobile Services]
2. [Restrict table permissions to authenticated users]
3. [Add authentication to the app]
5. [Store authentication tokens on the client]

This tutorial is based on the Mobile Services quickstart. You must also first complete the tutorial [Get started with Mobile Services]. 

>[AZURE.NOTE]This tutorial shows you how to authenticate users in Windows Store and Windows Phone Store 8.1 apps. For a Windows Phone 8.0 or Windows Phone Silverlight 8.1 app, see this version of [Get started with authentication in Mobile Services](mobile-services-windows-phone-get-started-users.md).

>This tutorial demonstrates the authentication flow managed by Mobile Services using a variety of identity providers. This method is easy to configure and supports multiple providers. To instead use Live Connect with client-managed authentication and provide a single sign-on experience in your Windows Phone app, see the topic [Single sign-on for Windows Store apps by using Live Connect]. By using client-managed authentication, your app has access to additional user data maintained by the identity provider. You can get the same user data in your mobile service by by calling the **user.getIdentities()** function in server scripts. For more information, see [this post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Register your app for authentication and configure Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Optional) Complete the steps in <a href="mobile-services-how-to-register-store-app-package-microsoft-authentication.md">Register your Windows Store app package for Microsoft authentication</a>.</p>
<p>Note that this step is optional because it only applies to the Microsoft Account login provider. When you register your Windows Store app package information with Mobile Services, the client is able to re-use Microsoft Account login credentials for a single sign-on experience. If you do not do this, your Microsoft Account login users will be presented with a login prompt every time that the login method is called. Complete this step when you plan to use the Microsoft Account identity provider.</p>
</li>
</ol>

##<a name="permissions"></a> Restrict permissions to authenticated users

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio, right-click the Windows Store project for the TodoList app and click <strong>Set as StartUp Project</strong>.</p></li>
<li><p>In the shared project, open the App.xaml.cs project file, locate the definition for the <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, and make sure that it is configured to connect to the mobile service running in Azure.</p>
<p>Note that When you use Visual Studio tools to connect your app to a Mobile Service, the tool generate two sets of <strong>MobileServiceClient</strong> definitions, one for each client platform. This is a good time to simplify the generated code by unifying the <code>#if...#endif</code> wrapped <strong>MobileServiceClient</strong> definitions into a single unwrapped definition used by both versions of the app. You won't need to do this when you downloaded the quickstart app from the Azure Management portal.</p>
</li> 
<li><p>Press the F5 key to run the Windows store app, and verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts.</p>
   
   	<p>This happens because the app attempts to access Mobile Services as an unauthenticated user, but the <em>TodoItem</em> table now requires authentication.</p></li>
</ol>

Next, you will update the app to authenticate users before requesting resources from the mobile service.

##<a name="add-authentication"></a> Add authentication to the app

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Store the authorization token on the client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in the [Mobile Services .NET How-to Conceptual Reference]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: /documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
