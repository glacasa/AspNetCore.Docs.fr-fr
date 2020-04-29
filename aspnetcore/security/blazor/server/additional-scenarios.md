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
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206388"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Scénarios Blazor de sécurité supplémentaires du serveur ASP.net Core

Par [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passer des jetons à une Blazor application serveur

Les jetons disponibles en dehors des composants Razor dans une Blazor application serveur peuvent être passés aux composants avec l’approche décrite dans cette section. Pour obtenir un exemple de code, `Startup.ConfigureServices` y compris un exemple complet, consultez la page [transmission de jetons Blazor à une application côté serveur](https://github.com/javiercn/blazor-server-aad-sample).

Authentifiez Blazor l’application serveur comme vous le feriez avec une Razor pages ou une application MVC standard. Approvisionnez et enregistrez les jetons dans le cookie d’authentification. Par exemple :

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

Définissez une classe à passer à l’état initial de l’application avec les jetons d’accès et d’actualisation :

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Définissez un service de fournisseur de jetons **délimités** qui peut être Blazor utilisé dans l’application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans `Startup.ConfigureServices`, ajoutez des services pour :

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Dans le fichier *_Host. cshtml* , créez et instanciez `InitialApplicationState` et transmettez-le en tant que paramètre à l’application :

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

Dans le `App` composant (*app. Razor*), résolvez le service et initialisez-le avec les données du paramètre :

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

Dans le service qui effectue une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour appeler l’API :

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
