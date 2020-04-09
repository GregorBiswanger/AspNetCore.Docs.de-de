## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="75433-101">UserManager und SignInManager</span><span class="sxs-lookup"><span data-stu-id="75433-101">UserManager and SignInManager</span></span>

<span data-ttu-id="75433-102">Legen Sie den Anspruchstyp der Benutzerkennung fest, wenn eine Server-App Folgendes erfordert:</span><span class="sxs-lookup"><span data-stu-id="75433-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="75433-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601>oder <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in einem API-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="75433-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="75433-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>Details, z. B. den Namen, die E-Mail-Adresse oder die Sperrendzeit des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="75433-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="75433-105">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="75433-105">In `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="75433-106">Im `WeatherForecastController` Folgenden <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> wird `Get` protokolliert, wann die Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="75433-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

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
