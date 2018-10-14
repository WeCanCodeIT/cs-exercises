# Using Migrations with EF Core

Migrations allow us to keep our database in sync with our code.  We can write classes in C#, generate a migration, and then we'll have a database that's ready to store the data contained in instances of our classes.

Entity Framework is an Object Relational Mapper.  That means it's able to map (convert) instances of objects into what's called "relational data".  Relational databases are a very popular type of database right now.  We'll be using SQL Server, which is a relational database that's created by Microsoft and pairs really well with C# (also from Microsoft).

## What's a Migration?

Migrations are a class with that implement two methods: `Up` and `Down`.  

The `Up` method is responsible for transitioning the database from it's prior state, to the new state of the database.  The `Down` method is responsible for "undoing" the changes made by the `Up` method.

Imagine if we had three migrations like this:

1. CreateSongs
1. AddLyricsToSong
1. AddArtist

The `Up` method for the `CreateSongs` migration would be responsible for creating the table to store instances of our `Song`.  The `Down` method for `CreateSongs` would delete the table that stores instances of our `Song`.

The `Up` method for the `AddLyricsToSong` would add a column to the `Song` table.  The `Down` method would remove the column.

The `Up` method for the `AddArtist` migration would create a new table that stores Artists.  The `Down` method would delete the new Artists table.

If that's the migrations we have, then we could go from an empty database to the current design by running the `Up` methods on each migration, in order.

## Add-Migration <MigrationName>

To migrate our database, the first thing we'll need to be able to do is create a new Migration.

Migrations are automatically generated for us, which is awesome.  We don't need to write all the code that creates our database - we get it for free!  All we need to do is create our Model class, add it to a `DbSet` on our `DbContext` and then run the following command in the Package Manager Console: `Add-Migration MyNameInPascalCase`.

When we run that command, EF compares our current `DbContext` and all of our models against how they looked the last time `Add-Migration` ran.  It then figures out what's different between the two, and generates all the code to move the database forward so it matches our current `DbContext` and models.

It's a good practice to take a peek at the generated Migration file and make sure it's what you expected.  If you added a model, you'd expect to see some code that looks like it's adding a table for that model.  Removed a property?  Something should be getting removed.

## Remove-Migration

Migration isn't quite what you expected?  No worries, you can use the `Remove-Migration` command to remove the last created migration.  As long as you haven't updated the database yet, you'll be fine to remove it.  

If you have updated the database, it's usually easier to just fix-forward.  Update your `DbContext` and models as needed and add a new migration.  Migrations are inexpensive.

## Update-Database

So you've got that shiny new migration all ready to go, and now you need to apply those migrations to your database.  We can do this using the `Update-Database` command.

When we run `Update-Database` it will apply any pending migrations to the database.  If your database is already updated, it won't do anything.  But if you either created a new migration yourself, or `git pull`ed someone else's code that added a new one, it will apply them all to the database.

## How to Reset

Occasionally, things will get in an ugly state and you won't be able to get out of it.  When this happens, you can usually recover by running the following command: `Update-Database 0`.  This will apply the `Down` methods from every migration, in the correct order, to bring you back to a completely empty database.  If that succeeds, you can run `Update-Database` again to time travel your database back to the present.

If you're not able to do that, your final option is to connect to the database with SSMS and manually drop the database.  We'll cover that in more detail when we're getting into Databases.  In the meantime, feel free to google it or grab an instructor.

## Migrations are Immutable once Shared

Once you've commit and pushed a migration, you should never delete it.  Chances are someone else ran that migration, and if you suddenly delete the migration from the source code they won't be able to synchronize their code with their database.

Similarly, you can't modify a migration that's already been shared with other people (or applied to your own database, for that matter).  You should consider them immutable once applied or shared.