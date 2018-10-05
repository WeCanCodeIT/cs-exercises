# Reviews Site

## Overview

Create a reviews site that categorizes reviews. Create reviews about anything you like: tech, travel, toys, they're all good.

Create a ASP.NET MVC Core app that displays a list of reviews, allowing us to click on an individual review to see its details.

## Tasks

### Required Tasks

- [ ] Create/configure a ASP.NET MVC application.
- [ ] Create a `Review` class for the content of reviews. Its properties should include:
	- [ ] id (make this of type `int`) -- just use arbitrary, unique numbers for these ids
	- [ ] title
	- [ ] image url
	- [ ] review category
	- [ ] content
	- whatever other things you'd like to include. Some ideas:
		- date
		- description/synopsis
		- **Stretch Task:** tags (this should probably be a `List`) 
- [ ] Create a `ReviewRepository` class
	- [ ] This class should be configured so that it will be injected into `ReviewsController`.
	- [ ] It should have a constructor that creates your `Review` instances and populates a `Dictionary` that stores your reviews, using each review's id as the key.
	- [ ] It should have a method to get all reviews
	- [ ] It should have a method to find one review by id
- [ ] Create a `ReviewsController` class.
	- [ ] It should have a method mapped to a url that puts all of your reviews into the model, and returns a "reviews" `View`.
	- [ ] It should have a method mapped to a url including an id parameter that puts one of your reviews into the model, forwarding to a "review" `View`. This method should expect an "id" query parameter in order to select a specific review.

- [ ] Place images you use into your `wwwroot/images` folder and link to them there. Example: if your image is in `wwwroot/images/foo.jpg`, you would reference it in your application as `/images/foo.jpg`.

- [ ] Use your html/css-fu to style to your pages consistently and to make them appealing.

*Tip: Use [Creative Commons Image Search](https://ccsearch.creativecommons.org/) to find images that are free for use.*

## Grading

Find the rubric [here](./rubric.md).

#### Iteration

We use `@foreach()` to iterate over collections in Razor. If I had a collection called `Widgets` in my model, each of these having a `Name` attribute, I would do the following to generate a `<div>` for each of these. `Widget` is the name of my iteration variable, much like the for each loops you're familar with:

```HTML
@foreach (var widget in Model.Widgets)
{
    <div>@widget.Name</div>
}
```

This would result in something like the following, given the names "fee", "fie", and "foe":

```HTML
<div>fee</div>
<div>fie</div>
<div>foe</div>
```

#### Link URLs

Link urls will require a bit of string interpolation to get the review's Id into the `href` attribute.  If my `Model` had an `Id` property, we could append it to the end of our `href` as follows:

```HTML
<a href="/doAThing/@Model.Id">link text</a>
```

This would result in the following being rendered, assuming that `Model.Id` returns 42:

```HTML
<a href="/doAThing/42">link text</a>
```

