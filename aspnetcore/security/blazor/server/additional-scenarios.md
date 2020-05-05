---
title: Scénarios Blazor de sécurité supplémentaires du serveur ASP.net Core
author: guardrex
description: Découvrez comment configurer Blazor le serveur pour d’autres scénarios de sécurité.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772802"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="ee2c4-103">Scénarios Blazor de sécurité supplémentaires du serveur ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ee2c4-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="ee2c4-104">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ee2c4-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="ee2c4-105">Passer des jetons à une Blazor application serveur</span><span class="sxs-lookup"><span data-stu-id="ee2c4-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="ee2c4-106">Les jetons disponibles en dehors des Razor composants d’une Blazor application serveur peuvent être passés aux composants avec l’approche décrite dans cette section.</span><span class="sxs-lookup"><span data-stu-id="ee2c4-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="ee2c4-107">Pour obtenir un exemple de code, `Startup.ConfigureServices` y compris un exemple complet, consultez la page [transmission de jetons Blazor à une application côté serveur](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="ee2c4-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="ee2c4-108">Authentifiez Blazor l’application serveur comme vous le feriez Razor avec une application de pages ou MVC standard.</span><span class="sxs-lookup"><span data-stu-id="ee2c4-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="ee2c4-109">Approvisionnez et enregistrez les jetons dans le cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ee2c4-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="ee2c4-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="ee2c4-111">Définissez une classe à passer à l’état initial de l’application avec les jetons d’accès et d’actualisation :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="ee2c4-112">Définissez un service de fournisseur de jetons **délimités** qui peut être Blazor utilisé dans l’application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="ee2c4-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="ee2c4-113">Dans `Startup.ConfigureServices`, ajoutez des services pour :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="ee2c4-114">Dans le fichier *_Host. cshtml* , créez et instanciez `InitialApplicationState` et transmettez-le en tant que paramètre à l’application :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="ee2c4-115">Dans le `App` composant (*app. Razor*), résolvez le service et initialisez-le avec les données du paramètre :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="ee2c4-116">Dans le service qui effectue une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour appeler l’API :</span><span class="sxs-lookup"><span data-stu-id="ee2c4-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```
