# Test Driving a .NET Core Web Application from Scratch

## Creating the Project

1. In Visual Studio, select `File -> New`
1. Expand the `Web` project type
1. Select `.NET Core Web Application`
1. Select `Empty` project starter.

## Adding the Unit Testing Project

We will use xUnit to test our project.  It's very
similar to NUnit, which you have previously used,
with a couple small changes.

xUnit is particularly well suited for testing Core apps, so it makes sense to use it here.

1. Right click your Solution and select `Add -> New Project`.
1. Expand the `.NET Core` project type.
1. Select `xUnit Test Project (.NET Core)`.
1. Name the project exactly the same as your first project, but add ".Tests" to the end of the name. 

## Running the App

Hit `F5` to run the application.  You should see "Hello World" printed out on a web page.

Our web server is running at the `http://localhost:xxxx/` url.  Right now, it's just hard coded to return that Hello World string.

Let's make it use MVC and set up the basics.

## Adding MVC

Open the `Startup.cs` file.  

The first thing we'll need to do is add the MVC Service to our app.

Edit your Services method to include the following:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
}
```

Next, let's reconfigure our application so that it actually uses the MVC service.  Update your `Configure` method so that it looks like this:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

We've made two important changes here.  

First, we enabled "Static Files" support.  That will allow us to host things like .css and .js files on our server.

Finally, we've enabled MVC and defined some route information.  We'll get into routes more later, but for now just know that routes are what associate a url to a method on a controller.

Examples:
"http://localhost:xxxx/Home/Index" => `HomeController.Index()`
"http://localhost:xxxx/Foo/Create" => `FooController.Create()`

Hit `F5` to run the app again, and bask in the beautiful `404` error message.  This is progress.

## Adding a Controller

We're getting a `404` error message because we don't have a Controller and a view defined.

Take another peek at the route we defined:
```csharp
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
```

By default we're looking for a `HomeController` with an `Index` action.

In your `Tests` project, create a new test:
`HomeControllerTests.cs`
```csharp
    public class HomeControllerTests
    {
        [Fact]
        public void Index_Returns_A_View()
        {
            var underTest = new HomeController();

            var result = underTest.Index();

            Assert.IsType<ViewResult>(result);
        }
    }
```
Watch it fail, and then we'll make it pass by creating the `HomeController` with the Index method.

All of our controllers will live within a `Controllers` folder inside the Web Application.

Create the `Controllers` directory by right clicking on your web project and selecting `Add -> New Folder`.

Right click your newly created Controllers folder and select `Add -> New Class`.

Name the controller `HomeController.cs`.  Note that *ALL controllers must end with Controller*!

Open up the new `HomeController` class.  All controllers need to inherit from the MVC base class called `Controller`.  This adds some behaviour that's needed by other parts of the MVC Framework to our class, without us having to implement it.  This is a great example of inheritance in action!

```csharp
using Microsoft.AspNetCore.Mvc;  // Add this line.
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication3.Controllers
{
    public class HomeController : Controller // Modify this line.
    {
    }
}
```

Finally, we need to define the `Index` action.  Actions are just methods on Controllers:
```csharp
    public class HomeController : Controller
    {
        public string Index()
        {
            return "Hello World!!";
        }
    }
```

Run the app again, and check out the result.

At this point, we have a functional MVC app, but notice that we are only returning a `string`.  Wouldn't it be awesome if we could return HTML?

## Returning a View

Similar to how our Controllers all live inside a folder named `Controllers`, all of our views must live inside a Views folder.

Within the Views folder, we will create a folder for each of our Controllers to house the views used by that Controller.

Right click on your web project and add a folder called `Views` as a sibling to the `Controllers` directory.

Within the `Views` folder, create a directory called `Home` that will hold the views for our `HomeController`.

Let's add our home View.  Right click on the `Views\Home` folder and select `Add View`.  
* Name the View `Index`.
* Deselect the "Use a layout page" checkbox.

After a moment, you should have an `Index.cshtml` file that looks like this:
```html

@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>
</head>
<body>
</body>
</html>

```

Let's add a bit of custom HTML to the body:
```html
<body>
    <h1>This is html!</h1>
    <p>Here's a paragraph.</p>
</body>
```

Run the app again and view the home page.

Well, that was a let down.  We created this HTML, but our controller is still just returning a `string`.  We need to write the code that tells it to use our fancy view.

## Returning a View from a Controller

Open up the `HomeController`, and modify the `Index` Action (method) so that it looks like so:

```csharp
    public ViewResult Index()  
    {
        return View();
    }
```

We modified the return type to be a `ViewResult`, and we're no longer returning a `string` literal...we're running the `View()` method and returning the result.

The `View` method looks for a .cshtml file in the following location by default: `Views\{Controller}\{Action Name}.cshtml`

Since we are in the `HomeController`, and more specifically the `Index` Action, it will looks for the following file:  `Views\Home\Index.cshtml`. That's right where we created the view.

Run the unit test again and it should pass.

Run the app and you should see your HTML page being rendered.
