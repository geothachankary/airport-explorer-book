# Storing Secrets

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