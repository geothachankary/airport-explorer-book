# Using GeoIP location

One way to determine a user’s location is by using the [HTML Geolocation API](https://www.w3schools.com/html/html5_geolocation.asp). The problem with this is that it will prompt the user for permission:

![](permissions.png)

If the user does not give permission, we can not determine their location.

There is also another way to do this which does not require the user to give permission, and that is to do a Geolocation lookup based on the user's IP address. It is not as accurate as the HTML Geolocation API, but it is perfect for our needs as we can determine a user's current city and that is all we require. As long as we can zoom and center the map on their city, it will make the user experience much better.

## Getting a database of IP Addresses

If you want to implement this functionality, the first thing you need to do is to find a database or some sort of service which will do a Geolocation lookup from an IP Address. There are various of these available, but the one I am going to focus on is [MaxMind](https://www.maxmind.com/). They have both a downloadable database and a web service you can use.

We'll be using their [Geolite2 database](https://www.maxmind.com/en/geolite2-developer-package) which is free, along with their [MaxMind.GeoIP2 NuGet package](https://www.nuget.org/packages/MaxMind.GeoIP2).

Let's start off by installing the NuGet package:

```text
Install-Package MaxMind.GeoIP2
```

Next go their website at http://dev.maxmind.com/geoip/geoip2/geolite2/ and download the **GeoLite2 City** binary database:

![](download.png)

That file will be gzipped, so extract it, and then extract the `.tar` file. Copy the `Geolite2-City.mmdb` file to your `wwwroot` directory:

![](solution-explorer.png)

## Retrieve the locations

Next, we'll write some code which will open a `DatabaseReader` instance, and then call the `City()` method, passing the IP Address you want to lookup. In an ASP.NET Core application, the IP Address can be determined by using `HttpContext.Connection.RemoteIpAddress`.

If we are able to retrieve the location we'll store the user's location in `InitialLatitude` and `InitialLongitude` fields, and also set an initial zoom to be zoomed into that location.

If for whatever reason the look fails, we will just fail silently.

```csharp
public class IndexModel : PageModel
{
    private readonly IHostingEnvironment _hostingEnvironment;
    public string MapboxAccessToken { get; }
    public string GoogleApiKey { get; }

    public double InitialLatitude { get; set; } = 0;
    public double InitialLongitude { get; set; } = 0;
    public int InitialZoom { get; set; } = 1;

    public IndexModel(IConfiguration configuration, IHostingEnvironment hostingEnvironment)
    {
        _hostingEnvironment = hostingEnvironment;

        MapboxAccessToken = configuration["Mapbox:AccessToken"];
        GoogleApiKey = configuration["google:ApiKey"];
    }

    public void OnGet()
    {
        try
        {
            using (var reader = new DatabaseReader(_hostingEnvironment.WebRootPath + "\\GeoLite2-City.mmdb"))
            {
                // Determine the IP Address of the request
                var ipAddress = HttpContext.Connection.RemoteIpAddress;
                // Get the city from the IP Address
                var city = reader.City(ipAddress);

                if (city?.Location?.Latitude != null && city?.Location?.Longitude != null)
                {
                    InitialLatitude = city.Location.Latitude.Value;
                    InitialLongitude = city.Location.Longitude.Value;
                    InitialZoom = 9;
                }
            }
        }
        catch (Exception e)
        {
            // Just suppress errors. If we could not retrieve the location for whatever reason
            // there is not reason to notify the user. We'll just simply not know their current
            // location and won't be able to center the map on it
        }
    }

    // Some code omitted for brevity
}
```

We will also need to update the map constructor to pass in a `center` option with the coordinates (notice that you pass the Longitude first, and then the Latitude!) and also specify initial `zoom`:

```html
<script>
    mapboxgl.accessToken = '@Model.MapBoxToken';
    var map = new mapboxgl.Map({
        container: 'map', // container id
        style: 'mapbox://styles/mapbox/dark-v9', // stylesheet location
        center: [@Model.InitialLongitude, @Model.InitialLatitude], // starting position [lng, lat]
        zoom: @Model.InitialZoom // starting zoom
    });

    // Some code omitted for brevity...
</script>
```

## Testing it out

If you test this out on your local machine nothing will happen. The reason for this is because your IP Address will resolve to the loopback address (i.e. `127.0.0.1` or `::1`). A handy way in which we can fool ASP.NET Core in thinking the request is coming from somewhere else is by using the [ForwardedHeadersMiddleware](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersmiddleware?view=aspnetcore-2.0) and passing along a [X-Forwarded-For](https://en.wikipedia.org/wiki/X-Forwarded-For) header with each request.

First we'll register the `ForwardedHeadersMiddleware` when running in Development mode by calling the `UseForwardedHeaders` extension method. We pass along an instance of `ForwardedHeadersOptions` and set the `ForwardedHeaders` to look only for `X-Forwarded-For`.

When running on IIS, we need to set the `ForwardLimit` to **2**. By default this is set to 1, but IIS already acts as a reverse proxy and will add a `X-Forwarded-For` header to all requests. If the `ForwardLimit` is set to 1, then the middleware will only pick up the value which was set by IIS, and not the value we are passing in. So be sure to set `ForwardLimit` to 2.

```csharp
// Startup.cs

public class Startup
{
    // Some code omitted for brevity...

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseForwardedHeaders(new ForwardedHeadersOptions
            {
                ForwardedHeaders = ForwardedHeaders.XForwardedFor,

                // IIS is also tagging a X-Forwarded-For header on, so we need to increase this limit, 
                // otherwise the X-Forwarded-For we are passing along from the browser will be ignored
                ForwardLimit = 2
            });

            app.UseDeveloperExceptionPage();
            app.UseBrowserLink();
        }

        app.UseStaticFiles();

        app.UseMvc(routes =>
        {
            routes.MapRoute(
                name: "default",
                template: "{controller}/{action=Index}/{id?}");
        });
    }
}
```

We will need to be able to add the `X-Forwarded-For` header to our requests. I am using Firefox and have installed the [Modify Header Value extension](https://addons.mozilla.org/en-US/firefox/addon/modify-header-value/). If you are using Chrome you can install the [ModHeader extension](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj) which will allow you to modify the HTTP headers of requests.

We will need a IP Address to test with. The easiest way is to type "what is my ip address" in your search engine and it will tell you. I am using DuckDuckGo, but Google will do the same for you:

![](ip-address.png)

Now use the **Modify Header Value** or **Modheader** extension to set the `X-Forwarded-For` header. In the screenshot below you can see that I set the header for the `http://localhost:50158/` site, which is the address the application is running on:

![](header.png)

Run the application again, and sure enough the map is zoomed to Bangkok:

![](zoomed.png)