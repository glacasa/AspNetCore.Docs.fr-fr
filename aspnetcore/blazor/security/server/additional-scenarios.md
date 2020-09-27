---
title: ASP.NET Core Blazor Server des scénarios de sécurité supplémentaires
author: guardrex
description: Découvrez comment configurer Blazor Server pour d’autres scénarios de sécurité.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393663"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="4d49f-103">ASP.NET Core Blazor Server des scénarios de sécurité supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4d49f-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="4d49f-104">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="4d49f-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="4d49f-105">Passer des jetons à une Blazor Server application</span><span class="sxs-lookup"><span data-stu-id="4d49f-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="4d49f-106">Les jetons disponibles en dehors des Razor composants d’une Blazor Server application peuvent être passés aux composants avec l’approche décrite dans cette section.</span><span class="sxs-lookup"><span data-stu-id="4d49f-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="4d49f-107">Pour obtenir un exemple de code, y compris un `Startup.ConfigureServices` exemple complet, consultez la page [transmission de jetons à une Blazor application côté serveur](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="4d49f-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="4d49f-108">Authentifiez l' Blazor Server application comme vous le feriez avec une Razor application de pages ou MVC standard.</span><span class="sxs-lookup"><span data-stu-id="4d49f-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="4d49f-109">Approvisionnez et enregistrez les jetons dans l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="4d49f-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="4d49f-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4d49f-110">For example:</span></span>

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

<span data-ttu-id="4d49f-111">Définissez une classe à passer à l’état initial de l’application avec les jetons d’accès et d’actualisation :</span><span class="sxs-lookup"><span data-stu-id="4d49f-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4d49f-112">Définissez un service de fournisseur de jetons **délimités** qui peut être utilisé dans l' Blazor application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="4d49f-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4d49f-113">Dans `Startup.ConfigureServices` , ajoutez des services pour :</span><span class="sxs-lookup"><span data-stu-id="4d49f-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4d49f-114">Dans le `_Host.cshtml` fichier, créez l’instance et, `InitialApplicationState` puis transmettez-la en tant que paramètre à l’application :</span><span class="sxs-lookup"><span data-stu-id="4d49f-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="4d49f-115">Dans le `App` composant ( `App.razor` ), résolvez le service et initialisez-le avec les données du paramètre :</span><span class="sxs-lookup"><span data-stu-id="4d49f-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="4d49f-116">Ajoutez une référence de package à l’application pour le package NuGet [Microsoft. Aspnet. WebApi. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .</span><span class="sxs-lookup"><span data-stu-id="4d49f-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="4d49f-117">Dans le service qui effectue une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour appeler l’API :</span><span class="sxs-lookup"><span data-stu-id="4d49f-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="4d49f-118">Définir le schéma d’authentification</span><span class="sxs-lookup"><span data-stu-id="4d49f-118">Set the authentication scheme</span></span>

<span data-ttu-id="4d49f-119">Pour une application qui utilise plusieurs intergiciels (middleware) d’authentification et qui possède donc plusieurs schémas d’authentification, le schéma qui Blazor utilise peut être défini explicitement dans la configuration de point de terminaison de `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4d49f-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="4d49f-120">L’exemple suivant définit le schéma de Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="4d49f-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="4d49f-121">Utiliser des points de terminaison OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="4d49f-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="4d49f-122">La bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="4d49f-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="4d49f-123">Pour utiliser un point de terminaison v 2.0, configurez l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option dans le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4d49f-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="4d49f-124">Le paramètre peut également être défini dans le fichier de paramètres de l’application ( `appsettings.json` ) :</span><span class="sxs-lookup"><span data-stu-id="4d49f-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="4d49f-125">Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement.</span><span class="sxs-lookup"><span data-stu-id="4d49f-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="4d49f-126">Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ou dans le fichier de paramètres de l’application avec la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> clé.</span><span class="sxs-lookup"><span data-stu-id="4d49f-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="4d49f-127">Modifications du code</span><span class="sxs-lookup"><span data-stu-id="4d49f-127">Code changes</span></span>

* <span data-ttu-id="4d49f-128">La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0.</span><span class="sxs-lookup"><span data-stu-id="4d49f-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="4d49f-129">Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="4d49f-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="4d49f-130">dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="4d49f-130">in the Azure documentation.</span></span>
* <span data-ttu-id="4d49f-131">Étant donné que les ressources sont spécifiées dans les URI de portée pour les points de terminaison v 2.0, supprimez le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> paramètre de propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4d49f-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="4d49f-132">Pour plus d’informations, consultez [étendues, et non pas les ressources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="4d49f-132">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="4d49f-133">URI ID de l’application</span><span class="sxs-lookup"><span data-stu-id="4d49f-133">App ID URI</span></span>

* <span data-ttu-id="4d49f-134">Lors de l’utilisation de points de terminaison v 2.0, les API définissent un *`App ID URI`* , qui est destiné à représenter un identificateur unique pour l’API.</span><span class="sxs-lookup"><span data-stu-id="4d49f-134">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="4d49f-135">Toutes les étendues incluent l’URI ID d’application en tant que préfixe, et les points de terminaison v 2.0 émettent des jetons d’accès avec l’URI ID d’application en tant que public.</span><span class="sxs-lookup"><span data-stu-id="4d49f-135">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="4d49f-136">Lors de l’utilisation de points de terminaison V 2.0, l’ID client configuré dans l’API du serveur passe de l’ID d’application API (ID client) à l’URI ID d’application.</span><span class="sxs-lookup"><span data-stu-id="4d49f-136">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="4d49f-137">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4d49f-137">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="4d49f-138">Vous pouvez trouver l’URI ID d’application à utiliser dans la description de l’inscription de l’application du fournisseur OIDC.</span><span class="sxs-lookup"><span data-stu-id="4d49f-138">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
