# Retrieving the information

Now that we understand how to go about retrieving the information from Google Places, let's write some code to actually do this.

Unfortunately Google does not provide an official C# library for using their Google Places API, but there is a 3rd party NuGet package available called [GoogleApi](https://www.nuget.org/packages/googleapi). Go ahead and install that:

```text
Install-Package GoogleApi
```

Next, let's create a class for the data we'll be returning to the front-end:

```csharp
public class AirportDetail
{
    public string FormattedAddress { get; set; }
    public string PhoneNumber { get; set; }
    public string Photo { get; set; }
    public string PhotoCredit { get; set; }
    public string Website { get; set; }
}
```

In the code-behind file (`Index.cshtml.cs`), import the namespaces for all the classes we'll be using from the **GoogleApi** package:

```csharp
using GoogleApi;
using GoogleApi.Entities.Common;
using GoogleApi.Entities.Common.Enums;
using GoogleApi.Entities.Places.Details.Request;
using GoogleApi.Entities.Places.Photos.Request;
using GoogleApi.Entities.Places.Search.NearBy.Request;
```

We will need the Google API Key which we stored as a secret before. Remember that we can use the normal configuration services to access it, so let's go ahead and save it in a local property called `GoogleApiKey`:
 
```csharp
public class IndexModel : PageModel
{
    private readonly IHostingEnvironment _hostingEnvironment;
    public string MapboxAccessToken { get; }
    public string GoogleApiKey { get; }

    public IndexModel(IConfiguration configuration, IHostingEnvironment hostingEnvironment)
    {
        _hostingEnvironment = hostingEnvironment;

        MapboxAccessToken = configuration["Mapbox:AccessToken"];
        GoogleApiKey = configuration["google:ApiKey"];
    }

    // Some code omitted for brevity...
}
```

And now we can write the code for the custom handler. I am including the all the code below, and then we can walk through it to understand what it does:

```csharp
public class IndexModel : PageModel
{
    // Some code omitted for brevity...

    public async Task<IActionResult> OnGetAirportDetail(string name, double latitude, double longitude)
    {
        var airportDetail = new AirportDetail();

        // Execute the search request
        var searchResponse = await GooglePlaces.NearBySearch.QueryAsync(new PlacesNearBySearchRequest
        {
            Key = GoogleApiKey,
            Name = name,
            Location = new Location(latitude, longitude),
            Radius = 1000
        });

        // If we did not get a good response, or the list of results are empty then get out of here
        if (!searchResponse.Status.HasValue || searchResponse.Status.Value != Status.Ok || !searchResponse.Results.Any())
            return new BadRequestResult();

        // Get the first result
        var nearbyResult = searchResponse.Results.FirstOrDefault();
        string placeId = nearbyResult.PlaceId;
        string photoReference = nearbyResult.Photos?.FirstOrDefault()?.PhotoReference;
        string photoCredit = nearbyResult.Photos?.FirstOrDefault()?.HtmlAttributions.FirstOrDefault();

        // Execute the details request
        var detailsResonse = await GooglePlaces.Details.QueryAsync(new PlacesDetailsRequest
        {
            Key = GoogleApiKey,
            PlaceId = placeId
        });

        // If we did not get a good response then get out of here
        if (!detailsResonse.Status.HasValue || detailsResonse.Status.Value != Status.Ok)
            return new BadRequestResult();

        // Set the details
        var detailsResult = detailsResonse.Result;
        airportDetail.FormattedAddress = detailsResult.FormattedAddress;
        airportDetail.PhoneNumber = detailsResult.InternationalPhoneNumber;
        airportDetail.Website = detailsResult.Website;

        if (photoReference != null)
        {
            // Execute the photo request
            var photosResponse = await GooglePlaces.Photos.QueryAsync(new PlacesPhotosRequest
            {
                Key = GoogleApiKey,
                PhotoReference = photoReference,
                MaxWidth = 400
            });

            if (photosResponse.PhotoBuffer != null)
            {
                airportDetail.Photo = Convert.ToBase64String(photosResponse.PhotoBuffer);
                airportDetail.PhotoCredit = photoCredit;
            }
        }

        return new JsonResult(airportDetail);
    }
}
```

First of all, or custom hanlder named `OnGetAirportDetail` will take three parameters named `name`, `latitude` and `longitude` which will be the **Name**, **Latitude** and **Longitude** of the airport. 

We will be doing a nearby places search looking for an airport within a 1000 meter radius of those coordinates. We check the status of the response to ensure that everything is OK, and then also to ensure that the `Results` property of the response actually contains any records. If any of these conditions fail, we simply return a `BadRequestResult` - in other words HTTP Status Code 400.

Since we now know that we have a result, we grab the first one from the list (there should only be one in any case), and retrieve the `PlaceId` and photo reference.

Next, we will use the `PlaceId` to do a Place Detail query to get all the details of the airport. Once again we check the of the response. If there are issues, we return a `BadRequestResult`. Otherwise we retrieve the `FormattedAddress`, `InternationalPhoneNumber` and `Website`.

Next we check if the airport had a photo reference, If so we do a Photo query to retreive the actual photo. If we are able to retrieve a photo we save it as a base-64 encoded string.

Finally we return the result as JSON. 