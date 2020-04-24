<span data-ttu-id="66902-101">La page produite par le `Authentication` composant (*pages/Authentication. Razor*) définit les itinéraires requis pour la gestion des différentes étapes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="66902-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="66902-102">Le `RemoteAuthenticatorView` composant :</span><span class="sxs-lookup"><span data-stu-id="66902-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="66902-103">Est fourni par le package [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="66902-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="66902-104">Gère l’exécution des actions appropriées à chaque étape de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="66902-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
