# Understanding how we'll use the Google Places API

We have no certain way to link an airport in our downloaded CSV file to a specific Google Place. However, one of the API endpoints available allow us to search for a place near a specific coordinates (i.e. the latitude and longitude). We already have the name of the airport as well as its coordinates, so using the [Nearby Search Request](https://developers.google.com/places/web-service/search#PlaceSearchRequests) endpoint, we can search for places matching a search term (in this case the airport name) within a certain radius of the coordinates.

The **Nearby Search Request** endpoint will 