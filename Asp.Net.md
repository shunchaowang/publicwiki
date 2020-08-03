## EntityFramework Core
## N-Tier Architecture
## MVC
## Database Context
## Multiple Environments in ASP.NET Core
### Multiple Environments Setting
* Order of Precedence of the Configuration
1. appsettings.json
2. appsettings.{env.EnvironmentName}.json
3. Local User Secrets File
4. Environment Variables
5. Command Line Arguments
appsettings.{env.EnvironmentName}.json is loaded after appsettings.json, so it will override keys from appsettings.json.
Command Line Arguments are loaded last, and therefore, will always override keys from previous providers.
### Multiple Environments Runtime
