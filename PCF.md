# Dell.SiteMap Changes:
## Spring Cloud Config Server Setup
Moving configuration away from Bootstrap.cs/Startup.cs and using these files only for service registration.
Configuration is done in Program.cs along with Spring Cloud setup using HostBuilder (for Console Applications and WebHostBuilder for Web Applications)

Taking **Dell.SiteMap.DataProvider** as example:

### Before: (Old Implementation)
In Program.cs, we're building the ServiceProvider:
```
var pathToContentRoot = Directory.GetCurrentDirectory();
var serviceCollection = Bootstrap.Start(pathToContentRoot, new ServiceCollection());
var serviceProvider = serviceCollection.BuildServiceProvider();
var sitemapService = serviceProvider.GetService<IService>();
...
...
sitemapService.Start();
```
In Bootstrap.cs, services are registered:
```
serviceCollection.AddSingleton(Log.Logger);
serviceCollection.Configure<CassandraOptions>(options => configuration.GetSection("Cassandra").Bind(options));
...
...
...
serviceCollection.AddSingleton<IService, SitemapDataGeneratorService>();
```

### After: (New Implementation)
In Program.cs, using HostBuilder we provide all the configuration along with the Config Server details:
```
var envName = GetEnvironment();
var clientSettings = new ConfigServerClientSettings { Name = "sitemap.dataprovider", Environment = envName };
var hostBuilder = new HostBuilder().UseConsoleLifetime().UseEnvironment(envName)
    .ConfigureHostConfiguration(
        builder =>
            {
               builder.SetBasePath(Directory.GetCurrentDirectory())
                   .AddJsonFile("appsettings.json", false)
                   .AddEnvironmentVariables()
                   .AddConfigServer(new LoggerFactory())
                   .Build();
             }).ConfigureAppConfiguration(
        (context, builder) => { builder.AddCloudFoundryUserSecrets().AddConfigServer(clientSettings); })
     .ConfigureServices(Bootstrap.ConfigureServices);
...
...
...
private static string GetEnvironment()
{
    var envName = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
    if (!string.IsNullOrWhiteSpace(envName))
    {
        return envName;
    }

    envName = Environment.GetEnvironmentVariable("ENVIRONMENT");
    return string.IsNullOrWhiteSpace(envName) ? "Production" : envName;
}
...
...
...
// BUILD & START THE APPLICATION LIKE THIS
var host = hostBuilder.Build();
var program = (IService)host.Services.GetService(typeof(IService));
program.Start();
```
In Bootstrap.cs/Startup.cs (wherever service registration is done), register a new function **ConfigureServices** which calls Start method. ConfigureServices method is caled from Program.cs
```
public static class Bootstrap
{
    public static IServiceCollection Start(HostBuilderContext context, IServiceCollection serviceCollection)
    {
        var configuration = context.Configuration;

        Log.Logger = new LoggerConfiguration()
            .ReadFrom.Configuration(configuration)
            .Enrich.FromLogContext()
            .CreateLogger();

        serviceCollection.AddSingleton(Log.Logger);
        ...
        ...
        return serviceCollection;
    }

    public static void ConfigureServices(HostBuilderContext context, IServiceCollection serviceCollection)
    {
        Start(context, serviceCollection).BuildServiceProvider();
    }
}
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTMwNzk1OTE2OV19
-->