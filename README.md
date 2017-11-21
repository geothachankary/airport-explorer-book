# Introduction

There are many books and courses that will teach you ASP.NET Core. Most of them seem to be centred around developing some sort of CRUD (Create, read, update and delete) application. In other words they will show you to create an application which displays a list of items on a page, add new items to the list, view existing items, as well as update and delete items. Typically these applications are also backed by some sort of database in which aforemetioned items will be stored.

But that is **NOT** what you will learn in this book.

## What you will build

This book is about developing an application which is a little bit more fun than displaying a list of items on the screen and then editing those items. 

It is about learning techniques which falls outside the boundaries of your typical CRUD application.

On a functional level this book will teach you how to build an application which will display a list of Starbucks branches on a map. 

![]()

A user can zoom and pan the map, or navigate to a particular city using the search box 

![]()

Finally, a user can also select any Starbucks branch and view more information about that branch.

![]()

Sounds simple (and it is), but there is a lot happening under the hood, and you will almost certainly be introduced to new techniques which you can apply in your own applications.

## What you will learn

Let me start off by saying that this book is **NOT** for people just starting off with programming in ASP.NET Core and C#.

This book assumes that you already have a basic understanding of ASP.NET Core and C#. It also assumes that you can build a basic application using either Visual Studio, or the .NET Core command line tools along with a code editor such as Visual Studio Code.

**So what will you learn?**

* Building a very simple, single page ASP.NET Razor Pages application.
* Using AJAX requests in an ASP.NET Razor Pages application.
* Using MapBox (https://www.mapbox.com/) to display a map and add markers to the map
* Handling a large amount of markers on a map using clusters.
* Reading information from a CSV file.
* Using GeoJSON to represent geographical information.
* Using 3rd party APIs such as Foursquare to augment information.
* Using Algolia Places (https://community.algolia.com/places/) to add a location search autocomplete texbox to the map, and zooming the user to a selected location.
* Determining the current location of a user from their IP address.
* Using the Tailwind CSS framework (https://tailwindcss.com/) to style HTML.
* Probaly some more cool things I cannot think of right now... 😉

Does this sound like fun to you? A bit more exciting than your typical "_let's display a list of items in a grid_" tutorial?

If so, then let's dig in!
