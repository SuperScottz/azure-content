<properties 
	pageTitle="Azure Notification Hubs Notify Users" 
	description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="wesmc"/>

#Azure Notification Hubs Notify Users

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

##Overview

Push notification support in Azure enables you to access an easy-to-use, multiplatform, and scaled-out push infrastructure, which greatly simplifies the implementation of push notifications for both consumer and enterprise applications for mobile platforms. This tutorial shows you how to use Azure Notification Hubs to send push notifications to a specific app user on a specific device. An ASP.NET WebAPI backend is used to authenticate clients. Using the authenticated client user, and tag will be automatically added by the backend to notification registration. This tag will be used to send by the backend to generate notifications for a specific user. For more information on registering for notifications using an app backend, see the guidance topic [Registering from your app backend](http://msdn.microsoft.com/library/dn743807.aspx). This tutorial builds on the notification hub and project that you created in the [Get started with Notification Hubs] tutorial.

This tutorial is also the prerequisite to the [Secure Push] tutorial. After you have completed the steps in this tutorial, you can proceed to the [Secure Push] tutorial, which shows how to modify the code in this tutorial to send a push notification securely. 


##Prerequisites 

Before you start this tutorial, you must have already completed these Mobile Services tutorials:

+ [Get started with Notification Hubs]<br/>You create your notification hub and reserve the app name and register to receive notifications in this tutorial. This tutorial assumes you have already completed these steps. If not, please follow the steps in [Getting Started with Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started.md); specifically, the sections [Register your app for the Windows Store](notification-hubs-windows-store-dotnet-get-started.md#register-your-app-for-the-windows-store) and [Configure your Notification Hub](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub). In particular, make sure that you have entered the **Package SID** and **Client Secret** values in the portal, in the **Configure** tab for your notification hub. This configuration procedure is described in the section [Configure your Notification Hub](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub). This is an important step: if the credentials on the portal do not match those specified for the app name you choose, the push notification will not succeed.




> [AZURE.NOTE] If you are using Mobile Services as your backend service, see the [Mobile Services version](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) of this tutorial.




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Update the code for the client project

In this section, you update the code in the project you completed for the [Get started with Notification Hubs] tutorial. The should already be associated with the store and configured for your notification hub. In this section, you will add code to call the new WebAPI backend and use it for registering and sending notifications.

1. In Visual Studio, open the the solution you created for the [Get started with Notification Hubs] tutorial.

2. In Solution Explorer, right-click the **(Windows 8.1)** project and then click **Manage NuGet Packages**.

3. On the left-hand side, click **Online**.

4. In the **Search** box, type **Http Client**.

5. In the results list, click **Microsoft HTTP Client Libraries**, and then click **Install**. Complete the installation.

6. Back in the NuGet **Search** box, type **Json.net**. Install the **Json.NET** package, and then close the NuGet Package Manager window.

7. Repeat the steps above for the **(
8. Windows Phone 8.1)** project to install the **JSON.NET** NuGet package for the Windows Phone project.


8. In Solution Explorer, in the **(Windows 8.1)** project, double-click **MainPage.xaml** to open it in the Visual Studio editor.

9. In the **MainPage.xaml** XML code, replace the `<Grid>` section with the following code:

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>

10. In Solution Explorer, in the **(Windows Phone 8.1)** project, open **MainPage.xaml** and replace the Windows Phone 8.1 `<Grid>` section with that same code above.


11. In Solution Explorer, open the **MainPage.xaml.cs** file for the **(Windows 8.1)** and **(Windows Phone 8.1)** projects. Add the following `using` statements at the top of both files:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;


12. Add the code below to the MainPage class in MainPage.xaml.cs for the **(Windows 8.1)** and **(Windows Phone 8.1)** projects. 
 
	The callback for **Log in and register** stores the basic authentication token in local storage (note that this represents any token your authentication scheme uses), then uses `RegisterClient` to register for notifications using the backend. The callback for **Send Push** calls the backend to trigger a secure notification to all devices of this user. 

	Be sure to replace `{backend endpoint}` with the backend endpoint obtained previously:

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the AuthenticationTestHandler
			// on the backend when the request enters the backend.
            // The tag passed here can be whatever other tags you may want to use.
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


11. In Solution Explorer, under the **Shared** project, open the **App.xaml.cs** file. Find the call to `InitNotificationsAsync()` in the `OnLaunched()` event handler. Comment out or delete the call to `InitNotificationsAsync()`. The button handlers added above will initialize notifications.


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


12. In Solution Explorer, right-click the **Shared** project, then click **Add**, and then click **Class**. Name the class **RegisterClient.cs**, then click **OK** to generate the class. 
	
	This class will wrap the REST calls required to contact the app backend, in order to register for push notifications. It also locally stores the *registrationIds* created by the Notification Hub as detailed in [Registering from your app backend](http://msdn.microsoft.com/library/dn743807.aspx). Note that it uses an authorization token stored in local storage when you click the **Log in and register** button.


13. Add the following `using` statements at the top of the RegisterClient.cs file:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using System.Threading.Tasks;
		using System.Linq;

14. Add the following code inside the `RegisterClient` class definition. Be sure to replace `{backend endpoint}` with the your backend endpoint obtained previously for your backend:

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

15. Save all your changes.
		

## Testing the Application

1. Launch the application on both Windows 8.1 and Windows Phone 8.1. For Windows Phone 8.1 you can run the instance in the emulator or an actual device.

2. On the Windows 8.1 instance of the app, enter a **Username** and **Password** similar to the screen below so that it will differ from the user name and password you enter on Windows Phone.

    ![][14]

3. Click **Log in and register** and verify a dialog shows that you have logged in. 

4. On the Windows Phone 8.1 instance, enter a **Username** and **Password** similar to the screen below so that it will differ from the user name and password you enteed on the Windows 8.1 instance of the app.

    ![][15]

5. Click **Log in and register** and verify a dialog shows that you have logged in. 

6. Click **Send Push** on the Windows 8.1 instance and notice that the Windows 8.1 instance receives a toast notification. The Windows Phone instance does not receive a notification because the username tag doesn't match the tag expression being sent.

7. Click **Send Push** on the Windows Phone instance and notice that the Windows Phone instance receives a toast notification. The Windows 8.1 instance does not receive a notification because the username tag doesn't match the tag expression being sent.

8. Change the **Username** and **Password** of either instance so that it matched the other instance. Then click **Login and register** to verify the updated registration.
9. Click **Send push** in either instance and notice both instance receive the toast notification because the tag registered match the tag expression being sent.




[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-instance-username.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
