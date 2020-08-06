## EntityFramework Core
## N-Tier Architecture
## MVC
## Database Context
## Multiple Environments in ASP.NET Core
### Multiple Environments Setting
Order of Precedence of the Configuration
1. appsettings.json
2. appsettings.{env.EnvironmentName}.json
3. Local User Secrets File
4. Environment Variables
5. Command Line Arguments
appsettings.{env.EnvironmentName}.json is loaded after appsettings.json, so it will override keys from appsettings.json.
Command Line Arguments are loaded last, and therefore, will always override keys from previous providers.
### Multiple Environments Runtime
* Order of Precedence of the Runtime Settings
Environment Variable < CLI Arguments < Launch Settings
1. OS' Environment Variable
run command `export ASPNETCORE_ENVIRONMENT=Development` to set the runtime env variable, and use command 
`echo $ASPNETCORE_ENVIRONMENT` to check the value.
2. CLI Arguments to Set the Environment Variable
First modify `Program.cs` to enable command line arguments:
```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(configHost =>
        {
            configHost.AddCommandLine(args);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```
Host builde rwill add ASPNETCORE_ prefix to the argument key provided. Now we can run 
`dotnet run --environment Development` to make the application set > ASPNETCORE_ENVIRONMENT to be > Development.
3. Using Lanuch Settings to Set the Environment Variable
Set through `launchSettings.json` file, this will override every other configuration. We can add the settings to the
profiles:
```
 "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
```
E.g. `launchSettings.json` can be like this, this is the preferred way to set multi environments for .Net Core
application.
```
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:34078",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true
    },
    "Development": {
      "commandName": "Project",
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "Production": {
      "commandName": "Project",
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Production"
      }
    }
  }
}
```
* Access the Environmnent in the Startup Class
There are two methods dependency injection and method conventions in the Startup class to access the env variables.
1. By Dependency Injection

Using the DI we supply an argument of type IHostEnvironment into the Configure method,
then we use a simple if-else structure to determine which page is displayed by using the IsEnvironment method. 
This method checks which environment we are using. If it is set to Development then we use the developer exception page. 
For any other environment, we use an exception handler pointing to the custom error page.
Change `Configure` method of `Startup.cs`
```
public void Configure(IApplicationBuilder app, IHostEnvironment env)
{
    if (env.IsEnvironment("Development"))
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }
    // more code below
}
```
2. By Startup Method Conventions
As our project gets more complicated our if-else structure will get bloated and difficult to maintain. 
To avoid this, we are going to use the Startup method conventions.

How does this convention work?

There are two key methods in the Startup class: Configure and ConfigureServices. However, in the Startup class, these methods support environment-specific versions. 
For example, in the development environment, ASP.NET Core would look for the ConfigureDevelopment and the ConfigureDevelopmentServices methods before the default Configure and ConfigureServices methods.

Let’s try to implement this convention by rewriting `Startup.cs`:
```
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }
 
    public IConfiguration Configuration { get; }
 
    public void SharedConfigureServices(IServiceCollection services){
        services.AddControllersWithViews();
    }
    public void ConfigureDevelopmentServices(IServiceCollection services)
    {
        SharedConfigureServices(services);
    }
    public void ConfigureServices(IServiceCollection services)
    {
        SharedConfigureServices(services);
    }
 
    public void SharedConfigure(IApplicationBuilder app)
    {
        app.UseHttpsRedirection();
        app.UseStaticFiles();
 
        app.UseRouting();
 
        app.UseAuthorization();
 
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
        });
    }
    public void ConfigureDevelopment(IApplicationBuilder app)
    {
        app.UseDeveloperExceptionPage();
        SharedConfigure(app);
    }
 
    public void Configure(IApplicationBuilder app)
    {
       app.UseExceptionHandler("/Home/Error");
       app.UseHsts();
       SharedConfigure(app);
    }
}
```
First, we define the SharedConfigureServices method. This method contains the common configuration related to the application’s services across all environments. 
The application calls the ConfigureDevelopmentServices method when we are in the development environment. In any other environment, the application calls the ConfigureServices method.

Next, we define the SharedConfigure method. This method contains the common configurations related to the application’s request pipeline across all environments. 
The application calls the ConfigureDevelopment method when it is set to development. In any other environment, the application calls the Configure method. 
The default Configure method configures the application with the custom error page. 
The ConfigureDevelopment method configures the application with the debugging page.
* Access the Environment with the Tag Helper
There’s one more way to use our environments. Using them in the Startup class is great for making project-wide changes.
But what if we only want to make small changes?
Now, we come to the tag helper.

This is a great way to take advantage of our environment to make small changes to our code. 
For example, we want to display a little extra information on the error page when the environment is set to Testing.
in `cshtml` file use this tag to show the part for `Testing` environment:
```
<environment names="Testing,">
<h3>Testing Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed applications.</strong>
    It can result in displaying sensitive information from exceptions to end-users.
    For local debugging, enable the <strong>Development</strong> environment by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to <strong>Development</strong>
    and restarting the app.
</p>
</environment>
```
The environment tag checks if the environment variable is set to one of the values in the names attribute. 
When we are in the testing environment all the content within the tag is going to be rendered.
