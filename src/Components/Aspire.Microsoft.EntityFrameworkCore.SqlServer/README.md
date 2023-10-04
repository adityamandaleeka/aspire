# Aspire.Microsoft.EntityFrameworkCore.SqlServer library

Registers [EntityFrameworkCore](https://learn.microsoft.com/en-us/ef/core/) [DbContext](https://learn.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext) service for connecting Azure SQL, MS SQL server database. Enables connection pooling, health check, logging and telemetry.

## Getting started

### Prerequisites

- Azure SQL or MS SQL server database and connection string for accessing the database.

### Install the package

Install the Aspire SQL Server EntityFrameworkCore SqlClient library with [NuGet][nuget]:

```dotnetcli
dotnet add package Aspire.Microsoft.EntityFrameworkCore.SqlServer
```

## Usage Example

In the `Program.cs` file of your project, call the `AddSqlServerDbContext` extension method to register a `DbContext` for use via the dependency injection container.

```cs
builder.AddSqlServerDbContext<MyDbContext>();
```

You can then retrieve the `MyDbContext` instance using dependency injection. For example, to retrieve the cache from a Web API controller:

```cs
private readonly MyDbContext _context;

public ProductsController(MyDbContext context)
{
    _context = context;
}
```

## Configuration

The Aspire SQL Server EntityFrameworkCore SqlClient component provides multiple options to configure the SQL connection based on the requirements and conventions of your project.

### Use a connection string

When using a connection string from the `ConnectionStrings` configuration section, you can provide the name of the connection string when calling `builder.AddSqlServerDbContext<TContext>()`:

```cs
builder.AddSqlServerDbContext<MyDbContext>("myConnection");
```

And then the connection string will be retrieved from the `ConnectionStrings` configuration section:

```json
{
  "ConnectionStrings": {
    "myConnection": "Data Source=myserver;Initial Catalog=master"
  }
}
```

See the [ConnectionString documentation](https://learn.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring#remarks) for more information on how to format this connection string.

### Use configuration providers

The Aspire SQL Server EntityFrameworkCore SqlClient component supports [Microsoft.Extensions.Configuration](https://learn.microsoft.com/dotnet/api/microsoft.extensions.configuration). It loads the `MicrosoftEntityFrameworkCoreSqlServerSettings` from configuration by using the `Aspire:Microsoft:EntityFrameworkCore:SqlServer` key. Example `appsettings.json` that configures some of the options:

```json
{
  "Aspire": {
    "Microsoft": {
      "EntityFrameworkCore": {
        "SqlServer": {
          "ConnectionString": "Data Source=myserver;Initial Catalog=master",
          "DbContextPooling": true,
          "HealthChecks": false,
          "Tracing": false,
          "Metrics": true
        }
      }
    }
  }
}
```

### Use inline delegates

Also you can pass the `Action<MicrosoftEntityFrameworkCoreSqlServerSettings> configureSettings` delegate to set up some or all the options inline, for example to use a connection string from code:

```cs
    builder.AddSqlServerDbContext<MyDbContext>(configureSettings: settings => settings.ConnectionString = "Data Source=myserver;Initial Catalog=master");
```

## DevHost Extensions

In your DevHost project, register a SqlServer container and consume the connection using the following methods:

```cs
var sql = builder.AddSqlServerContainer("sqldata");

var myService = builder.AddProject<YourApp.Projects.MyService>()
                       .WithSqlServer(sql, "master");
```

`.WithSqlServer` configures a connection in the `MyService` project named `sqldata`. In the `Program.cs` file of `MyService`, the sql connection can be consumed using:

```cs
builder.AddSqlServerDbContext<MyDbContext>("sqldata");
```

## Additional documentation

* https://learn.microsoft.com/ef/core/
* https://github.com/dotnet/astra/tree/main/src/Components/README.md

## Feedback & Contributing

https://github.com/dotnet/astra