# Album App

## Objectives
- Students will be introduced to Entity Framework by creating a console application
- Students will `Model` an `Album` and setup a `AlbumsService` to handle requests to Create, Read, Update, and Delete a list of music albums that are stored in SQL Server.
- Students will learn to use migrations to keep their code and database in sync.

## Definitions
- **`Repository`**: A "Repository", originally defined by Domain-Driven Design (Evans, 2005) as "a mechanism for encapsulating storage, retrieval, and search behavior which emulates a collection of objects".
- **`Service`**: Service is a bit of an overloaded term, but in this context we're using it to indicate that our AlbumService class is an object that knows how to do things with Albums.  It's a pretty generic term.
- **`Migration`**: Migrations are pieces of code that can move our database design forward or backward in time.

## Solution Set-up 
- Create an ASP.NET Core Console Application
- Add an xUnit Test Project to your solution.
  - Add NSubstitute dependency with Nuget.
  - Add a reference to the main project from the Test project.

## Classes to build
- **`Album`**: 
    - contains `int` property for `Id`.
    - contains `string` object properties for `Name`, and `Artist`
- **`AlbumContext`**:
    - Inherits from `DbContext`.
    - Contains our `DbSet`.
    - Defines the connection string to our database.
- **`InitialMigration`**:
    - Once you have your model and context, you'll need to generate and apply a migration.
- **`Repository`**:
    - You can use the code that we wrote previously for this class.
- **`AlbumRepository`**: 
	- Inherits from our generic `Repository` class.
- **`AlbumService`**:
    - Test driven.
    - Is responsible for handling user facing actions with `Albums`.
    - Requires an instance of the `AlbumRepository` class to be constructed.
    - Has the following public methods (test driven!)
        - `Index()` returns a `string` representation of all `Album`s in the database.
        - `Delete(int id)` deletes the specified `Album` in the database, and returns a `string` message to that effect.
        - `Details(int id)` returns a `string` representation of the specified `Album`
        - `Update(int id, string name, string artist)` updates the specified `Album` in the database.
        - `Create(string album, string artist)` creates a new `Album` in the database, and returns a `string`message to that effect.
- **`Program`**:
    - Contains the entry point for the application.
    - Instantiates all necessary classes.
    - Handles all IO with the user by dispatching to the controller.
    - Handles basic flow of the application (looping)
    - Keep this class slim so that we can confidently avoid unit testing it (IO is tough to test!).

## Other 

## Stretch Tasks
- Add the `ReleaseYear` to the `Album` model, migrate your database, and display it where appropriate.
