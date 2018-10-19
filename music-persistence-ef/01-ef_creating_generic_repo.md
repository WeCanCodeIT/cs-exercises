# Setting up Entity Framework Core

## Creating the Solution & Projects

For this tutorial, we'll create a repository that's able to Create, Read, Update and Delete records container in a SQL Server Database.  Let's get started.

### Creating the Project

We'll use a Class Library for this tutorial, but the process is largely the same if you're creating an ASP.NET Core Web Application or even a Console Application.

1. Go to `File -> New Project`.
1. Select `Class Library (.NET Core)`.
1. Name the solution `EFCoreTutorial`.
1. Click OK.

### Creating the Unit Test Project

Of course, we're going to be using unit tests to drive a lot of this behavior.

1. Right click the Solution, `Add -> New Project`.
1. Select `xUnit Test Project (.NET Core)`.
1. Name the project `EFCoreTutorial.Tests`.
1. Add a reference from the `EFCoreTutorial.Tests` project to the `EFCoreTutorial` project.

## Configuring EntityFrameworkCore

We need to do a bit of configuration to prepare our project for using Entity Framework.

In the `EFCoreTutorial` project, complete the following steps:
1. Use Nuget to add the `Microsoft.EntityFrameworkCore` dependency.
1. Use Nuget to add the `Microsoft.EntityFrameworkCore.SqlServer` dependency.
1. Use Nuget to add the `Microsoft.EntityFrameworkCore.Tools` dependency.
1. Use Nuget to add the `Microsoft.EntityFrameworkCore.Proxies` dependency.
1. Add a folder named `Models`.
1. Add a class called `Song` to the `Models` folder.
1. Add the following properties to the `Song` class.
    - `public int Id { get; set; }`
    - `public string Title { get; set; }`

### Adding the DataContext

Next, we'll need to add a `DataContext` class.  It is the `DataContext`'s responsibility to manage our interaction with our database.  Don't fret, we'll get most of this functionality for free by inheriting from a class that's built into EF Core.

1. Add a class to the root of the `EFCoreTutorial` project called `MusicContext`.
1. The `MusicContext` should inherit from `Microsoft.EntityFrameworkCore.DbContext`.
1. Add a property to your `MusicContext` class as shown:

```csharp
public class MusicContext : DbContext
{
    public DbSet<Song> Songs { get; set; }
}
```

The `DbSet<Song> Songs` property represents a set of `Song`s in the database.  It's an `IEnumerable`, which means we can treat it like a `List<Song>`s (for the most part).

### Configuring the Database Connection

If our `MusicContext` is going to manage all our interactions with the database, we probably need to tell it some details about our database.  We do this using what's called a connection string.  A connection string is, you guessed it, a string that describes our connection to the database.

Here's a sample:
`var connectionString = "Server=(localdb)\\mssqllocaldb;Database=MusicTesting;Trusted_Connection=True;";`

Connection strings can get pretty complex, but ours isn't too bad.  It's a `;` delimited list of Key Value Pairs.  Our connection string defines the Server (where our SQL Server instance is running), the name of the our Database, and finally that we're using a trusted connection.  For most of the work we'll be doing you'll only need to change the Database name, but in production applications you'll probably need to study up a bit more on the topic.

So, we've got our connection string defined, but now we need to tell our `MusicContext` about it.  We'll do this by overriding the `OnConfiguring` method that's defined in our `DbContext` base class.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    var connectionString = "Server=(localdb)\\mssqllocaldb;Database=MusicTesting;Trusted_Connection=True;";

    optionsBuilder.UseSqlServer(connectionString)
                  .UseLazyLoadingProxies();
    
    base.OnConfiguring(optionsBuilder);
}
```

You'll need to add a few `using` statements to get this code to work.

## Test Driving the Repository

### Red

In the `Tests` project, create a test fixture for our `SongRepository` and run a sample test to make sure you can see it fail.

```csharp
public class SongRepositoryTests
{
    [Fact]
    public void TODO()
    {
        Assert.True(false);
    }
}
```

By seeing that test run and Fail, we've proven to ourselves that our test runner is working. 

We can delete that test now.

Let's write a more valuable test.  We need to ask ourselves: which test should we write next?

We want to select a test that's going to be a pretty basic test since we're going to need to create the class.  What's a simple thing our repository could do?

#### Testing the Count

Let's test that it knows how many songs are in the database.  That's some pretty basic functionality that will let us get some momentum.

We have to write a test that uses an instance of our SongRepository to get the number of songs.  What could that look like?  We are designing...

```csharp
    [Fact]
    public void Count_Starts_At_Zero()
    {
        var db = new MusicContext();
        var underTest = new SongRepository(db);

        var count = underTest.Count();

        Assert.Equal(0, count);
    }
```

That looks pretty reasonable.  First we create an instance of our `DbContext`.  Remember that this represents our database.  It makes sense that, if our `Repository` is supposed to save, retrieve, update and delete `Songs` it would need the database to do that.

Our `SongRepository` class collaborates with the `db` instance to do it's job. 

Next, we run the `Count()` method and make sure it tells us `0`.

Now that we have designed our `SongRepository` (by writing the test code that uses it!) we need to continue our journey towards Failing for the Right reason.  Right now we have build errors that are between us and running the test.  


### Fix Compiler Errors

We need to fix all these compiler errors so that we're able to run the test and see it fail.

Creating the class (in your main project) and adding a using statement should fix the compiler error when we try to create an instance of the `SongRepository`.

Add the `Count()` method and `return -1;` for now.  That way we should see the test fail for the right reason.  It should say something like `expected 0 but received -1` in the test failure.  Once you see that, you're officially Failing for the Right reason.

### Green

Let's make that test pass.

Remember how the `MusicContext` has a set of `Songs` that represents what's in the database?  We also said that they were `IEnumerable` just like `List`s are.  

How can we return the number of items in a `List`?

```csharp
    public class SongRepository
    {
        private MusicContext db;

        public SongRepository(MusicContext db)
        {
            this.db = db;
        }

        public int Count()
        {
            return db.Songs.Count();
        }
    }
```

Running the test will reveal a new error message that hints at our Database not being there.  Since this test is an integration test, it's actually relying on the database to do it's job.  We need to create the Database.

#### Create the Initial Migration

We're going to use something called database migrations to create our database, and keep it synchronized with our code.  We'll go over Migrations in detail later, for now let's just create the database and keep moving.

1. Open the Package Manager Console.
1. Run the following command to create a migration: `Add-Migration FirstMigration`.
1. Run the following command to apply the migration to the Database: `Update-Database`.

_Run the test again and you should be green._

It may not look like much, but that code is actually connecting to a database and counting the number of records in a database.  Pretty cool.

### Refactor

I can't see anything that would make this code any cleaner, so we've finished our first TDD cycle (Red -> Green -> Refactor).

## The Next Test

It's time to select our next test.  We need to select a test that will nudge our repository in the right direction without overwhelming us or making us take too large of a step.

Here's what I propose: 

```csharp
    [Fact]
    public void Create_Increases_Count()
    {
        var db = new MusicContext();
        var underTest = new SongRepository(db);

        underTest.Create(new Song() { Title = "Foo" });

        var count = underTest.Count();
        Assert.Equal(1, count);
    }
```

We're telling our repository to create an instance, and then checking that the count increases.

Write just enough code to get the test to compile:
```csharp
    public void Create(Song song)
    {
    }
```
Obviously, we aren't actually adding anything yet.  We should see our test fail, since we aren't adding anything.  Run it and see the correct error message.

```csharp
    public void Create(Song song)
    {
        db.Songs.Add(song);
        db.SaveChanges();
    }
```

Saving something for the first time in the database is pretty straight forward.  We add it to the DbSet, and then we tell the context to save the changes.

If you run the tests right now, you might see two green tests.  Or maybe you'll only see one.

Run them a second time and they'll both fail.

## What gives?

Normally, unit tests are entirely self contained.  Everything that makes them pass or fail is defined inside the unit test, so they're always consistent.

These tests, however, aren't Unit Tests.  These tests rely on a piece of external state - _the database_.

Every time that `Create` test runs, it's creating a new record in our database.  Keep running your tests and check out the failure message on your `Create_Increases_Count` test.  It should increase by 1 every time you run the tests.

### How to fix it

We're going to use something called a transaction to prevent this from happening.

Add the following code to your test class:

```csharp
    public class SongRepositoryTests : IDisposable
    {
        private MusicContext db;

        public SongRepositoryTests()
        {
            db = new MusicContext();
            db.Database.BeginTransaction();
        }

        public void Dispose()
        {
            db.Database.RollbackTransaction();
        }

        // Hiding the tests for brevity, but you'll need
        // to refactor them to use the db field instead of
        // the local variable.

    }
```

The constructor will be run before each test, and the `Dispose` method will be called after each test completes (whether it's successful or not).

By starting and rolling back a transaction, we'll "Undo" all the changes that each test makes inside the database.

Run your tests a few times now and confirm that the actual `Count` isn't increasing any more.

### Resetting the Database

Having tests that rely on a database is not ideal, but it's our best bet to ensure that we're actually interacting with the database the way we think we are.

Occasionally, you'll hose your database with tests like this.  We need to reset things

1. Open the `Package Manager Console`.
1. Run this command to reset the database to being empty: `Update-Database 0`
1. Run this command to apply all your migrations again: `Update-Database`

Run the tests and they should turn green, and stay that way.

### All the Rest

At this point, you've configured your project and test drove your way through the initial set up of a new repository.  Depending on the needs of your application, you may need the ability to `Update`, `Delete`, `Find` and maybe even `GetAll`.  You can test drive your way through the rest, but here's a more complete example that may be helpful to reference.

```csharp
// Unit Tests
    public class SongRepositoryTests : IDisposable
    {
        private MusicContext db;
        private SongRepository underTest;

        public SongRepositoryTests()
        {
            db = new MusicContext();
            db.Database.BeginTransaction();

            underTest = new SongRepository(db);
        }

        public void Dispose()
        {
            db.Database.RollbackTransaction();
        }

        [Fact]
        public void Count_Starts_At_Zero()
        {
            var count = underTest.Count();

            Assert.Equal(0, count);
        }

        [Fact]
        public void Create_Increases_Count()
        {
            underTest.Create(new Song() { Title = "Foo" });

            var count = underTest.Count();
            Assert.Equal(1, count);
        }

        [Fact]
        public void GetById_Returns_Created_Item()
        {
            var expectedSong = new Song() { Title = "Baby Shark" };
            underTest.Create(expectedSong);

            var result = underTest.GetById(expectedSong.Id);  // The Id was set by EF when we call Create above.

            Assert.Equal(expectedSong.Title, result.Title);
        }

        [Fact]
        public void Delete_Reduces_Count()
        {
            var song = new Song() { Title = "Baby Shark" };
            underTest.Create(song);

            underTest.Delete(song);
            var count = underTest.Count();

            Assert.Equal(0, count);
        }

        [Fact]
        public void GetAll_Returns_All()
        {
            underTest.Create(new Song() { Title = "Baby Shark" });
            underTest.Create(new Song() { Title = "Never gonna give you up" });

            var all = underTest.GetAll();

            Assert.Equal(2, all.Count());
        }

        // Save or Update?
    }
```

```csharp
// Application Code
    public class SongRepository
    {
        private MusicContext db;

        public SongRepository(MusicContext db)
        {
            this.db = db;
        }

        public int Count()
        {
            return db.Songs.Count();
        }

        public void Create(Song song)
        {
            db.Songs.Add(song);
            db.SaveChanges();
        }

        public Song GetById(int id)
        {
            return db.Songs.Single(s => s.Id == id);
        }

        public void Delete(Song song)
        {
            db.Songs.Remove(song);
            db.SaveChanges();
        }

        public void Save()
        {
            // Save will update all song instances that have been modified in the DB.
            // Theres no great way to test this, and really we would be testing Microsoft's
            // code and not ours.
            db.SaveChanges();
        }

        public IEnumerable<Song> GetAll()
        {
            return db.Songs.ToList();
        }
    }
```

## Generic Repository

That's a ton of code, and we'll need to write a repository like this for every single Model - yikes!

Imagine if we were to add an `Album` model to our project.  We would need to create an `AlbumRepository` that looks almost the same as our `SongRepository` but you'd basically have to find and replace `Song` with `Album`.

Said another way, the new repository would be the same aside from the `Type` of the model we are dealing with.  Sound familiar?

There is only one `List` class, but we can create a `List<Song>` and a `List<Album>` or a `List<Whatever>`.  That's a programming language feature called generics.

Here's what our repository might look like once it's genericised:

```csharp
    public class Repository<T>
        where T : class
    {
        private DbContext db;

        public Repository(DbContext db)
        {
            this.db = db;
        }

        public int Count()
        {
            return db.Set<T>().Count();
        }

        public void Create(T entity)
        {
            db.Set<T>().Add(entity);
            db.SaveChanges();
        }

        public T GetById(int id)
        {
            return db.Set<T>().Find(id);
        }

        public void Delete(T entity)
        {
            db.Set<T>().Remove(entity);
            db.SaveChanges();
        }

        public void Save()
        {
            // Save will persist all modifications to entities to the database.
            // Theres no great way to test this, and really we would be testing Microsoft's
            // code and not ours.  Untested code is the exception, not the norm.
            db.SaveChanges();
        }

        public IEnumerable<T> GetAll()
        {
            return db.Set<T>().ToList();
        }
    }
```

And here's how we would use it to create our `SongRepository` class:

```csharp
    public class SongRepository : Repository<Song>
    {
        public SongRepository(MusicContext context) : base(context)
        {
            // This ensures our base constructor gets run.
        }
    }
```

And here's what using the SongRepository class looks like:

```csharp
    var myRepo = new Repository<Song>(db);
    var song = myRepo.GetById(1);
    song.Title = "Hello World";
    myRepo.Save();
```

As you can see, it's pretty easy to use by the end of it.  Keep this code handy, we'll be using it in future projects also.
