# Solstice

Solstice is a modular open-source .NET framework designed to simplify the development of robust and scalable applications.

## 📋 Overview

Solstice adopts a modular architecture that separates concerns into specialized sub-projects, allowing developers to use only the components they need. This approach promotes code maintainability, testability, and flexibility.

## 🧩 Project Structure

The Solstice project is organized into several modules:
- Solstice.API: Provides components necessary for creating RESTful APIs and web services.
- Solstice.Domain: Contains domain entities, interfaces, and core business logic.
- Solstice.Infrastructure: Implements cross-cutting concerns such as logging, caching, and configuration management.
- Solstice.Applications: Contains application logic and use cases.
- Solstice.Database.MySql: Offers specific integration with MySQL for data persistence.
- Solstice.Database.PostgreSQL: Offers specific integration with PostgreSQL for data persistence.
- Solstice.Database.Conventions: Provides customizable naming conventions for database entities, tables, and columns.
- Solstice.Scheduled: Implements job scheduling capabilities for recurring tasks and background processes.

## 🚀 Installation

```bash
dotnet add package Solstice.API
dotnet add package Solstice.Domain
dotnet add package Solstice.Infrastructure
dotnet add package Solstice.Database.MySql
dotnet add package Solstice.Applications
```

You can install only the packages you need for your project. You can also find them in Nuget Package Manager. 


🔧 Configuration

```csharp
// Program.cs example
var builder = WebApplication.CreateBuilder(args);

// Scan for every package
builder.ScanAndLoadAssembliesByPrefix(`Name of your project`);

// If you want to use lowercase routes everytime
builder.Services.UseLowercaseRoutes(); 

// Get your configuration for your environment
string? env = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");

IConfigurationRoot configuration = builder.Configuration
    .AddJsonFile("appsettings.json", false, true)
    .AddJsonFile($"appsettings.{env}.json", true, true)
    .Build();

...
    
// Implements your way to get your connection string    
string? dbConfig = configuration.GetConnectionString("Database");

if (string.IsNullOrEmpty(dbConfig))
{
    throw new ConfigurationErrorsException("Connection string not found");
}

// Add your DbContext (
builder.Services.AddDatabaseContext<`DbContext to load`>(dbConfig, migrate: true);
...
    
// If you want to remove null value from your response (Not really needed if you use correctly DTO but in case..)    
builder.Services.AddControllers()
    .AddNewtonsoftJson(opt =>
    {
        opt.SerializerSettings.ReferenceLoopHandling = ReferenceLoopHandling.Ignore;
        opt.SerializerSettings.DateTimeZoneHandling = DateTimeZoneHandling.Local;
        opt.SerializerSettings.DefaultValueHandling = DefaultValueHandling.Include;
        opt.SerializerSettings.NullValueHandling = NullValueHandling.Ignore;
    })
    .AddControllersAsServices();    
...
    
// Add repositories and services
builder.Services.AddHttpContextAccessor();
builder.Services.AddRepositories<`DbContext previously loaded`>();
builder.Services.AddServices();

var app = builder.Build();

// If you want to use authentication and authorize your API
app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();

```

## 📝 Usage Example

```csharp
// Example using Solstice.Domain (Not needed you can use your own, it only add an Id attribute)
public class Product : CoreModel
{
    public string Name { get; set; }
    public decimal Price { get; set; }
    public string Description { get; set; }
}

// Example using Solstice.API
[ApiController]
[Route("api/[controller]")]
public class ProductsController : SolsticeController
{
private readonly IProductService _productService;

    public ProductsController(IProductService productService)
    {
        _productService = productService;
    }
    
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
    {
        // Don't do that in production, paginate every list you send /!\ It's just an example..
        // You can use a Paged<Product> and .GetPagedResult(page: page)
        return Ok(await _productService.GetAllAsync());
    }
}
```

## 📚 Documentation

Complete documentation will be available soon...

## 🤝 Contributing

Contributions are welcome! Please check our contribution guide for more information.

## 🙏 Acknowledgements

Thanks to all contributors who have participated in this project and to the .NET community for their continued support.

## 📄 License

Solstice is open-source software released under the [Apache License 2.0](https://choosealicense.com/licenses/apache-2.0/).