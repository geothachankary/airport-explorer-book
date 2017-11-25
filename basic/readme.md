# Getting Started

## Prerequisites

As stated before, this book assumes a basic understanding of C# and building ASP.NET Core applications using either Visual Studio or the .NET Core command line tools along with a code editor such as Visual Studio Code.

As such, you will need to have Visual Studio 2017 installed which can be downloaded from https://www.visualstudio.com/downloads/. Alternatively you can download the .NET Core SDK from https://www.microsoft.com/net/download/ and Visual Studio Code from https://code.visualstudio.com/.

If you are unfamiliar with using these tools and buiding apps with ASP.NET Core, then I suggest you read [The Little ASP.NET Core Book](https://www.recaffeinate.co/book/) by Nate Barnettini first. Once you have a familiarity with those, then you can return to this book.

Please note that for the purpose of this book I will demonstrate using Visual Studio 2017. If you want to follow along with the .NET Core CLI and your own code editor you are welcome, but I will assume that you are proficient enough to know how to use those tools to create a project, add files, run your project, etc.

## ASP.NET Razor Pages

We will be using [ASP.NET Razor Pages](https://docs.microsoft.com/en-us/aspnet/core/mvc/razor-pages/) to build the application. Razor Pages was added in ASP.NET Core 2.0 with the aim of simplifying developing simple web pages with some code behind them. 

With ASP.NET MVC you would create a _Controller_ with _Actions_, and then create _Views_ which can be returned but those actions. These files would live across multiple folders. 

With Razor Pages, however, your _HTML markup_ for a page and a _C# "code-behind" file_ for that page live together in a directory next to each other. Many developers believe that this makes things a bit more organized and easier to find your way around a project.

Because of this simpler programming model and because our application will consist of a single page, I will be using Razor pages. There are also a few tricks I want to highlight in terms of Razor pages, such as defining multiple _handlers_ and _custom routing_ for a page.

I will not be going into too much of the technical details of Razor Pages, so I suggest reading [Introduction to Razor Pages in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/mvc/razor-pages/) and perhaps work through the [Getting started with Razor Pages in ASP.NET Core tutorial](https://docs.microsoft.com/en-us/aspnet/core/tutorials/razor-pages/razor-pages-start) if you are unfamiliar with it.

