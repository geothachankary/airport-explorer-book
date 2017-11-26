# Understanding how we'll use the Google Places API

We have no certain way to link an airport in our downloaded CSV file to a specific Google Place. However, one of the API endpoints available allow us to search for a place near a specific coordinates (i.e. the latitude and longitude). We already have the name of the airport as well as its coordinates, so using the [Nearby Search Request](https://developers.google.com/places/web-service/search#PlaceSearchRequests) endpoint, we can search for places matching a search term (in this case the airport name) within a certain radius of the coordinates.

Let's demonstrate this by calling the endpoint and searching for **Suvarnabhumi Airport**. We can specify the coordinates as the `location`, the name of the airport as the `keyword`, specifying a `radius` of 1000 meters and set the `type` parameter to `airport` to ensure that we only search for airports:

```text
https://maps.googleapis.com/maps/api/place/nearbysearch/json?key=[YOUR API KEY]Q&location=13.681099891662598,100.74700164794922&radius=1000&keyword=Suvarnabhumi Airport&type=airport
```

This will return the following result:

```json
{
    "html_attributions": [],
    "results": [
        {
            "geometry": {
                "location": {
                    "lat": 13.6899991,
                    "lng": 100.7501124
                },
                "viewport": {
                    "northeast": {
                        "lat": 13.73075215,
                        "lng": 100.76245305
                    },
                    "southwest": {
                        "lat": 13.67641475,
                        "lng": 100.74599885
                    }
                }
            },
            "icon": "https://maps.gstatic.com/mapfiles/place_api/icons/airport-71.png",
            "id": "d29fc1de22664c36a10939872f74c0ee73a5f2d1",
            "name": "ท่าอากาศยานสุวรรณภูมิ",
            "photos": [
                {
                    "height": 1125,
                    "html_attributions": [
                        "<a href=\"https://maps.google.com/maps/contrib/102061212321633747417/photos\">Gerrit Holzhueter</a>"
                    ],
                    "photo_reference": "CmRaAAAA4UEdWYhlckR1sU1sifcZ2H9w8l7zHtNwhapak7X1-ifJPgp4FjeESb8uDAtFRWKvRK51Ac9kr52TmhBC5zHJ36oLrAElDxr7kdRpHhwh_DcM1VUTXVm-eITulLtHcTPPEhCQPhqexHamLP5b4ybTh_dlGhSPyGW1SHuS9JKyjgGj0lZyPHKZTQ",
                    "width": 2000
                }
            ],
            "place_id": "ChIJTydCFXdnHTERB3oVT1UZDRI",
            "rating": 4.2,
            "reference": "CmRRAAAAue7D06cxYkC3JJnZvs2qaCfAzlEJmkWP42DnJIWJ1JTnK1FLeF2sJutuDveybjP8ly1s7iXlLvL4oLCBcBw4Xfw3m0IN18Du-QZ47TGPJFTClS2y1iKJtkQkQckldHd9EhCiWS73Yjx95gIU48wepAqKGhTvzqmaUedJ2h21afG-HpHlJDcYNw",
            "scope": "GOOGLE",
            "types": [
                "airport",
                "point_of_interest",
                "establishment"
            ],
            "vicinity": "999 หมู่ 1, Nong Prue, Bang Phli"
        }
    ],
    "status": "OK"
}
```

As you can see there is a `results` array containing the list of places it could find. In our case there should only ever be one result (hopefully!). The result contains a `place_id` and also an array of photos containing a single element with a photo for the place. The important attribute we will need from there is the `photo_reference`.

## Getting the place detail

First thing we need to do is to get more detail for the place. For that we can use the [Place Details](https://developers.google.com/places/web-service/details) endpoint and pass along the `place_id` attribute we retrieved from the search result in the `placeid` parameter:

```text
https://maps.googleapis.com/maps/api/place/details/json?key=[YOUR API KEY]&placeid=ChIJTydCFXdnHTERB3oVT1UZDRI
```

This will return the full detail for the place:

```json
{
    "html_attributions": [],
    "result": {
        "address_components": [
            {
                "long_name": "999",
                "short_name": "999",
                "types": [
                    "street_number"
                ]
            },
            {
                "long_name": "หมู่ 1",
                "short_name": "หมู่ 1",
                "types": [
                    "route"
                ]
            },
            ...
        ],
        "adr_address": "<span class=\"street-address\">999 หมู่ 1</span> <span class=\"extended-address\">Nong Prue</span>, <span class=\"locality\">Amphoe Bang Phli</span>, <span class=\"region\">Chang Wat Samut Prakan</span> <span class=\"postal-code\">10540</span>, <span class=\"country-name\">Thailand</span>",
        "formatted_address": "999 หมู่ 1 Nong Prue, Amphoe Bang Phli, Chang Wat Samut Prakan 10540, Thailand",
        "formatted_phone_number": "02 132 1888",
        "geometry": {
            "location": {
                "lat": 13.6899991,
                "lng": 100.7501124
            },
            "viewport": {
                "northeast": {
                    "lat": 13.73075215,
                    "lng": 100.76245305
                },
                "southwest": {
                    "lat": 13.67641475,
                    "lng": 100.74599885
                }
            }
        },
        "icon": "https://maps.gstatic.com/mapfiles/place_api/icons/airport-71.png",
        "id": "d29fc1de22664c36a10939872f74c0ee73a5f2d1",
        "international_phone_number": "+66 2 132 1888",
        "name": "Suvarnabhumi Airport",
        "photos": [
            {
                "height": 2240,
                "html_attributions": [
                    "<a href=\"https://maps.google.com/maps/contrib/101445658970989030075/photos\">Leo Chang</a>"
                ],
                "photo_reference": "CmRaAAAAEQIJW5FglhPz9yXJeKg-hOIsQSK2D4N8iDDMrgjpiWlIJ8JDqHD91rVxlYrIlA-vSnTme5QCzbe-3yooMIH98HEQCLL53Kk2eVmi9d2YorlOppHaeypBGAi6BxOnD6b9EhD6QTT2pyMVAEucF510uAY9GhRN4SYASj56L0zpK7NyWWQqTfr01w",
                "width": 4000
            },
            ...
        ],
        "place_id": "ChIJTydCFXdnHTERB3oVT1UZDRI",
        "rating": 4.2,
        "reference": "CmRRAAAAoxf7LwDhAtHLjC21_p__Fl1zCrbLLribNsXcxJQ6K7dWMcP2MYRACh0qjVDdFg5VIOiDVjwCQOV5469-vLZxGE_GJp_CJ31deUJim4IFJLI56cFUyehPti9SPqOltPAVEhDAGZtJjwjChjHeFZ0X0x31GhR11gCnI7hhTMRhwdLFZ5q-bWkeBg",
        "reviews": [
            {
                "author_name": "Allan Ung",
                "author_url": "https://www.google.com/maps/contrib/109830434604960986698/reviews",
                "language": "en",
                "profile_photo_url": "https://lh5.googleusercontent.com/-RCDM8Rxm2xg/AAAAAAAAAAI/AAAAAAAAAAA/AFiYof3E22m20S9LBGHTQDK1xm0MuliU5g/s128-c0x00000000-cc-rp-mo-ba6/photo.jpg",
                "rating": 1,
                "relative_time_description": "2 weeks ago",
                "text": "I have been to Bangkok almost every year for the last 15 years. Unfortunately last year, I took a public taxi from Suvarnabhumi Airport and was charged 700 Baht to Siam Novotel. The usual cost was around 400 Baht including toll fee. Although the meter was turned on, the meter was continuously jumping. The driver kept touching the bottom of the meter and I knew something was not right. I made a police report but have never received an update or reply. Below is the taxi license number which you should take note of.  It was supposed to be a vacation but the entire experience was marred by this incident.",
                "time": 1510169491
            },
            ...
        ],
        "scope": "GOOGLE",
        "types": [
            "airport",
            "point_of_interest",
            "establishment"
        ],
        "url": "https://maps.google.com/?cid=1300723721569663495",
        "utc_offset": 420,
        "vicinity": "999 หมู่ 1, Nong Prue, Bang Phli",
        "website": "http://www.suvarnabhumiairport.com/"
    },
    "status": "OK"
}
```

I have redacted a lot of information from the sample response above to keep it concise, but you can notice a lot of information we can use. Fields such as the `formatted_address`, `international_phone_number` and `website`.

For can read more about the [Place Details Result](https://developers.google.com/places/web-service/details#PlaceDetailsResults) in the Google documentation.

## Getting the place photo

We also want to get an actual photo of the place. We can use the `photo_reference` we retrieved from the search result to call the [Photo Reference](https://developers.google.com/places/web-service/photos) endpoint, passing the value of the `photo_reference` as the `photoreference` parameter as well as a `maxwidth` of 400 pixels:

```text
https://maps.googleapis.com/maps/api/place/photo?key=[YOUR API KEY]&photoreference=CmRa...Q&maxwidth=400
```

This will return the actual photo in the response.