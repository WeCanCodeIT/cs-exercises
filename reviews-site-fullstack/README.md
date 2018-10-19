# Reviews Site, Full Stack

## Overview

Return to your reviews site from the [previous exercise](../reviews-site) (or create another).


## Creating a new repository from an existing one

If you are continuing with your reviews site from the previous exercise, create a new GitHub repo based on a clone of your old project repo. Here's a simple way to go about that:

1. Create another clone with a different name. The `git clone` command will accept an additional argument specifying a folder name, so you can do something like: `git clone http://github.com/myid/reviews-site reviews-site-full-stack`.
1. The `.git` folder is what establishes the folder in which it resides as a git repository. Remove the `.git` folder from this newly cloned repo. You can do this from Windows Explorer or by running `rm -rf .git` from Git Bash. Now it's no longer a git repository.
1. Treat it now just like any other new project: `git init`, etc.

We're going to add categories to reviews. Each category will have one or more reviews (a one to many relationship). Each review will be assigned to one category (a many to one relationship).

Also, we're going to add Entity Framework to your site so that it writes/reads from an SQL Server database.


## Tasks

Feel free to use appropriate class names other than `Review` and `Category`, but these instructions will refer to `Review` and `Category`.

- Add the following dependencies to the project using Nuget
    - [ ] Microsoft.EntityFrameworkCore
    - [ ] Microsoft.EntityFrameworkCore.Tools
    - [ ] Microsoft.EntityFrameworkCore.SqlServer
- Create a `Category` class that:
    - [ ] it is an EF entity
    - [ ] contains a property referencing the `Review`s it is associated with.
- Update the `Review` class such that:
    - [ ] it is an EF entity
    - [ ] configures a relationship to its associated category.
- Use seed data to create some `Review`s and `Category`s.
    - [ ] `override` `OnModelConfiguring`
    - [ ] Use the `ModelBuilder` to indicate which entities have which data.
    - [ ] Create a migration and apply the seed data to your database.
- Update your view (templates/html/css) such that:
    - [ ] there is a page that lists review categories, each of which links to the (details) page for a specific category.
    - [ ] there is a page that lists the reviews for a chosen category, each of which links to the (details) page for a specific review.
    - [ ] each review detail page has a link to the page for its category.

### Stretch Tasks

#### Tags

- [ ] Create a `Tag` entity and a `TagReview` entity.
- [ ] Update `Review` so that it can have tags associated with it. A review can have many tags, a tag can be assigned to many reviews.
- [ ] Display tags on the review details page.
- [ ] Create a page that displays links to all of the reviews associated with a given tag.

##### Stretchier

- [ ] Style your tags list template as a *tag cloud*, making tags which appear more often larger and/or bolder and those that appear less often smaller and/or lower weight.
- [ ] Allow creation and deletion of tags from a review using `<form>` and `<button>` elements along with the appropriate controller method(s).

## Grading

Find the rubric [here](./rubric.md).

### Tips

#### Start with the known specifics

Start with mapping and displaying your `Review`s. Add `Category`s to them after you've gotten that working.  Don't try to boil the ocean (do everything at one) - get small pieces working.

#### Mapping out URLs, Model attributes, view template names, etc

It is good practice to map things out and think them through, using plural and singular names appropriately, or you'll likely be well confused.

Your names, etc will be different, but hopefully this helps with some of the confusion I've seen around naming and what is in the model for a specific view. I'll append model and view to names to help clarify, though we usually wouldn't do this in the wild. Create your *own* table that maps these things out:

|Page Intent    |URL                                                |Description of model attribute                                 |Retrieved via  |View will display       |View Template name
|-----------    |--------------------------------                   |------------------------------                                 |-------------  |------------------      |------------------
|A List of Categories |/category/index                              |an `IEnumerable` of all `Category` objects                     |repo `GetAll`  | the list of categories |"Views/Category/Index.cshtml"
|Details for a Category, including the list of Reviews it applies to|/Category/Details/42|the `Category` object associated with `id`|repo `GetById` |category detail and list of reviews for that category, each of which links to a review|"Views/Category/Details.cshtml"
|Review details       |/review/details/86                           |the `Review` object associated with `id`                       |repo `GetById` |review details     |"Views/Review/Details.cshtml
