# Generating a Google Places API Key

We will need to create a project in the [Google Developer Console](https://console.developers.google.com) and create an API Key which we can use to call the Google Places API. Ensure that you have signed into your Google account and head over to https://console.developers.google.com/projectselector/apis/library. Click on the button to **Create** a project.

![](google-1.png)

Give the project a name of **Airport Explorer** and then click on **Create**:

![](google-2.png)

Once the project has been created, search for **google places**, and then click to select **Google Places API Web Service**:

![](google-3.png)

Next, click on the button to **Enable** this service:

![](google-4.png)

Once the service is enabled, click on **Create Credentials**:

![](google-5.png)

Click on **What credentials do I need?**:

![](google-6.png)

This will create an **API Key** for you. Copy that API key, as you will need it to call the Google Places API. Finally you can click on the **Done** button.

![](google-7.png)

## Storing Secrets

Since the Google API Key is secret, we should not be storing it in our source code, and also not in one of our configration files which may be checked into our source control system at a later stage. Thankfully ASP.NET Core gives us an easy way to manage secrets in our application and allow us to access them through the normal ASP.NET Core configuration service. 

In Visual Studio, right-click on the project in the **Solution Explorer** window, and then select **Manage User Secrets...**. 

![](manage-user-secrets.png)

This will open a `secrets.json` file which is strored in a secure location on your harddrive and will not ever be accessible to anyone else but you. We can follow a hierarchical structure like we did before when we stored the configuration information for the Mapbox access token.

Let's create a key named `Google` and a sub-key under that named `ApiKey` with the value of the API Key:

```json
{
  "Google": {
    "ApiKey": " AIzaSyAP1b08VzDZkhYoI9hdCEcyzE5PEjyoGC4"
  }
}
```

To access that value, we can use the exact same mechanism as before to inject an `IConfiguration` instance into our class constructor, and then use `configuration["Google:ApiKey"]` to access the value. Don't worry about doing that now though - we will get to that later.

{% hint style='tip' %}
**Learn more**

* [Safe storage of app secrets during development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?tabs=visual-studio)
* [Managing Secrets in .NET CORE 2.0 Apps](https://blogs.msdn.microsoft.com/mihansen/2017/09/10/managing-secrets-in-net-core-2-0-apps/)

{% endhint %}