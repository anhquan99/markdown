# Nuget package
- `Microsoft.EntityFrameworkCore`: is the main package for using EntityFramework
- `Microsoft.EntityFrameworkCore.Design`: for updating database through Package Manager Console
- `Microsoft.EntityFrameworkCore.SqlServer` for `UseSqlServer()`
- `Microsoft.EntityFrameworkCore.Tools` for Package Manager Console
- `Microsoft.EntityFramework.Configuration` for getting `appsettings.json`
# DbContext
- When using Package Manager Console for updating and adding migration, DbContext use paramterless constructor therefore to inject `DbContextOptions` like connection string, ... we must create a class implement from `IDesignTimeDbContextFactory` to inection options.
- **Seed data:**
	- Seeding data by using `OnModelCreating` the datatype of parameter modelBuilder must be `ModelBuilder`. The default datatype is DbModelBuilder which does not have `HasData()` methods. Advantages of this method are data will be created when updating database
	- Seeding data by passing DbContext into a function and call it when needed. We must check for duplication.