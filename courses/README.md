# Using ASP.NET MVC Core to build an MVC Application

### Objectives
- Students will be introduced to the ASP.NET MVC Framework by creating a sample MVC application
- Students will `Model` a `CourseTopic` and setup a `CourseController` to handle GET requests to display many and one courses in a `View`

### Definitions
- **`Repository`**: A "Repository", originally defined by Domain-Driven Design (Evans, 2003) as "a mechanism for encapsulating storage, retrieval, and search behavior which emulates a collection of objects".
- **`Controller`**: Handles HTTP Requests
- **`Container`**: The container is responsible for injecting an instance of the requested resource into the application component when the component is initialized.
- **`Route Mappings`**: Route Mappings are used to map web requests onto specific handler classes (Controllers) and/or handler methods (Actions).


### Solution Set-up 
- Create an Empty ASP.NET Core Web Application
- Add an xUnit Test Project to your solution.
  - Add NSubstitute dependency with Nuget.
  - Add a reference to the main project from the Test project.
- Add the MVC Service inside `Startup.cs`.
- Configure the MVC service with a default route mapping in `Startup.cs`

### Classes to build
- **`Course`**: 
  - contains `int` property for `Id`.
  - contains `String` object properties for `Name`, and `Description`
  - contains appropriate constructor
- **`CourseRepository`**: 
	- Creates 3 courses and puts them into the `Dictionary<int, Course>` we can use:
		- `1`, `ASP.NET MVC`, `ASP.NET MVC is Microsoft's preferred web framework`
		- `2`, `C#`, `C# is a modern, general purpose programming language`
		- `3`, `Intro to Javascript`, `Javascript is the language of the web`
 	- contains the ability to access the entire map's collection of courses or one course in the map
	  - `GetAll()`
	  - `FindById(int id)`
- **`CourseController`**: The Controller class handles HTTP requests
	- Give user the ability to see all courses offered in the application
	- Give user the ability to navigate into one of the courses for the details of the course

# Stretch Tasks
- Add a 4th param (instructor) to the course and show this information in the front end of your application
