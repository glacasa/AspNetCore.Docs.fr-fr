## <a name="usermanager-and-signinmanager"></a>UserManager et SignInManager

Définissez le type de réclamation d’identification de l’utilisateur lorsqu’une application Server nécessite :

* <xref:Microsoft.AspNetCore.Identity.UserManager%601>ou <xref:Microsoft.AspNetCore.Identity.SignInManager%601> dans un critère d’évaluation de l’API.
* <xref:Microsoft.AspNetCore.Identity.IdentityUser>détails, tels que le nom de l’utilisateur, son adresse e-mail ou l’heure de fin de lock-out.

Dans `Startup.ConfigureServices` :

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

Ce `WeatherForecastController` qui suit <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> enregistre `Get` le moment où la méthode est appelée :

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using BlazorAppIdentityServer.Server.Models;
using BlazorAppIdentityServer.Shared;

namespace BlazorAppIdentityServer.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
