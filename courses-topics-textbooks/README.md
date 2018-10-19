# EF enabled courses-topics-textbooks

## Objectives
- Wire up an Entity Framework enabled web application that will control entities for courses, topics and textbooks.
- Use Migrations to manage your database's structure.
- Modeling one-to-many relationships.

## Setup

- Create an Empty ASP.NET Core Web Application
- Add an xUnit Test Project to your solution.
  - Add NSubstitute dependency with Nuget.
  - Add a reference to the main project from the Test project.
- Add the MVC Service inside `Startup.cs`.
- Configure the MVC service with a default route mapping in `Startup.cs`
- Get a HomeController up and running that's returning a default Index view.

## The Project Explanation

A course can be a complicated POCO when we start to bring in object relationships. In this scenario, we are going to think about a course that has a name and a particular Topic or topics that it can fall under. Also, a textbook, along with it's title, will have a particular course that it is being utilized in. While there are many different ways we can begin to visual these object relationships, the tables below will certainly help to keep us all on the same page for this project. 

### The `Topic` Table

The simplest of the entities we will create, the `Topic` will contain a name.

|topic|
|----|
|C#|
|Entity Framework Core|
|TDD|

### The `Textbook` Table

A `Textbook` has a name and and can be utilized in a number of different `Course`s. 

|title|`Course`|
|----|--------|
|Head First C#|csharp101|
|Head First Design Patterns|csharp102|
|Clean Code|csharp102|
|Intro to Entity Framework Core|csharp102|


### The `Course` Table

A `Course` can belong to many different topics. 

|code|name|description|`Topic`|
|----|----|-----------|-------|
|csharp101|"Intro to C#"|"description"|c#|
|csharp102|"Advanced Software Design"|"description"|c#, tdd|

 

### The tests to build out this application 
We will write the following unit tests to drive the creation of this project

- save and load a topic
- generate a topic id
- save and load a course
- save a textbook to a particular course
- establish course to topics relationship
- establish topic to courses relationship
- find courses for a topic
- find courses for a particular topic id
