---
title: Scénarios Blazor de sécurité supplémentaires pour l’ASP.net Core webassembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: cd1433d5716b9b595270209fa874a8cb93fdf699
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138428"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="f9b97-102">Scénarios de sécurité supplémentaires ASP.NET Core éblouissant webassembly</span><span class="sxs-lookup"><span data-stu-id="f9b97-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="f9b97-103">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f9b97-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="f9b97-104">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f9b97-104">Request additional access tokens</span></span>

<span data-ttu-id="f9b97-105">La plupart des applications ont uniquement besoin d’un jeton d’accès pour interagir avec les ressources protégées qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="f9b97-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="f9b97-106">Dans certains scénarios, une application peut nécessiter plusieurs jetons pour interagir avec au moins deux ressources.</span><span class="sxs-lookup"><span data-stu-id="f9b97-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="f9b97-107">Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="f9b97-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="f9b97-108">Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application`Program.Main`cliente (, *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="f9b97-109">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9b97-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="f9b97-110">`TryGetToken`Cette</span><span class="sxs-lookup"><span data-stu-id="f9b97-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="f9b97-111">`true`avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="f9b97-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="f9b97-112">`false`Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="f9b97-112">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="f9b97-113">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="f9b97-113">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="f9b97-114">Le `AuthorizationMessageHandler` service peut être utilisé avec `HttpClient` pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="f9b97-114">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="f9b97-115">Les jetons sont acquis à l’aide `IAccessTokenProvider` du service existant.</span><span class="sxs-lookup"><span data-stu-id="f9b97-115">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="f9b97-116">Si un jeton ne peut pas être acquis `AccessTokenNotAvailableException` , une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="f9b97-116">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="f9b97-117">`AccessTokenNotAvailableException`dispose d' `Redirect` une méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="f9b97-117">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="f9b97-118">`AuthorizationMessageHandler` Peut être configuré avec les URL, les portées et l’URL de retour autorisées à `ConfigureHandler` l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="f9b97-118">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="f9b97-119">Dans l’exemple suivant, `AuthorizationMessageHandler` configure un `HttpClient` en `Program.Main` (*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-119">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="f9b97-120">Pour plus de commodité `BaseAddressAuthorizationMessageHandler` , une est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="f9b97-120">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="f9b97-121">Les modèles de webassembly éblouissants compatibles avec l’authentification utilisent désormais [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) pour `HttpClient` configurer un `BaseAddressAuthorizationMessageHandler`avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f9b97-121">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="f9b97-122">Dans le cas où le client `CreateClient` est créé avec dans l’exemple `HttpClient` précédent, est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-122">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f9b97-123">Le configuré `HttpClient` est ensuite utilisé pour effectuer des demandes autorisées à l' `try-catch` aide d’un modèle simple.</span><span class="sxs-lookup"><span data-stu-id="f9b97-123">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="f9b97-124">Le composant `FetchData` suivant demande des données de prévisions météorologiques :</span><span class="sxs-lookup"><span data-stu-id="f9b97-124">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="f9b97-125">Vous pouvez également définir un client typé qui gère tous les problèmes d’acquisition de jeton et HTTP au sein d’une même classe :</span><span class="sxs-lookup"><span data-stu-id="f9b97-125">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="f9b97-126">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9b97-126">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="f9b97-127">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9b97-127">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="f9b97-128">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f9b97-128">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="f9b97-129">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="f9b97-129">Handle token request errors</span></span>

<span data-ttu-id="f9b97-130">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le fournisseur d’identité (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="f9b97-130">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="f9b97-131">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="f9b97-131">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="f9b97-132">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="f9b97-132">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="f9b97-133">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="f9b97-133">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="f9b97-134">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="f9b97-134">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="f9b97-135">Ce scénario se produit si un utilisateur `https://login.microsoftonline.com` visite et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="f9b97-135">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="f9b97-136">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="f9b97-136">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="f9b97-137">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="f9b97-137">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="f9b97-138">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="f9b97-138">They are part of the nature of SPAs.</span></span> <span data-ttu-id="f9b97-139">Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="f9b97-139">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="f9b97-140">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="f9b97-140">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="f9b97-141">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="f9b97-141">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="f9b97-142">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-142">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="f9b97-143">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="f9b97-143">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="f9b97-144">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="f9b97-144">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="f9b97-145">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="f9b97-145">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="f9b97-146">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="f9b97-146">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="f9b97-147">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="f9b97-147">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="f9b97-148">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="f9b97-148">Store the current page state in session storage.</span></span> <span data-ttu-id="f9b97-149">Pendant `OnInitializeAsync`, vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="f9b97-149">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="f9b97-150">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="f9b97-150">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="f9b97-151">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="f9b97-151">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="f9b97-152">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="f9b97-152">The following example shows how to:</span></span>

* <span data-ttu-id="f9b97-153">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="f9b97-153">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="f9b97-154">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="f9b97-154">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="f9b97-155">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="f9b97-155">Save app state before an authentication operation</span></span>

<span data-ttu-id="f9b97-156">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="f9b97-156">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="f9b97-157">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="f9b97-157">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="f9b97-158">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="f9b97-158">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="f9b97-159">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-159">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="f9b97-160">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="f9b97-160">Customize app routes</span></span>

<span data-ttu-id="f9b97-161">Par défaut, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="f9b97-161">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="f9b97-162">Routage</span><span class="sxs-lookup"><span data-stu-id="f9b97-162">Route</span></span>                            | <span data-ttu-id="f9b97-163">Objectif</span><span class="sxs-lookup"><span data-stu-id="f9b97-163">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="f9b97-164">Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="f9b97-164">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="f9b97-165">Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="f9b97-165">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="f9b97-166">Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="f9b97-166">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="f9b97-167">Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="f9b97-167">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="f9b97-168">Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="f9b97-168">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="f9b97-169">Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="f9b97-169">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="f9b97-170">Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="f9b97-170">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="f9b97-171">Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-171">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="f9b97-172">Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-172">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="f9b97-173">Les itinéraires indiqués dans le tableau précédent sont configurables via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span><span class="sxs-lookup"><span data-stu-id="f9b97-173">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="f9b97-174">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="f9b97-174">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="f9b97-175">Dans l’exemple suivant, tous les chemins d’accès ont pour `/security`préfixe.</span><span class="sxs-lookup"><span data-stu-id="f9b97-175">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="f9b97-176">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-176">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f9b97-177">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-177">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="f9b97-178">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et `RemoteAuthenticatorView` Affichez le avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="f9b97-178">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="f9b97-179">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="f9b97-179">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="f9b97-180">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="f9b97-180">Customize the authentication user interface</span></span>

<span data-ttu-id="f9b97-181">`RemoteAuthenticatorView`comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="f9b97-181">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="f9b97-182">Chaque État peut être personnalisé en passant un personnalisé `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="f9b97-182">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="f9b97-183">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier `RemoteAuthenticatorView` le comme suit.</span><span class="sxs-lookup"><span data-stu-id="f9b97-183">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="f9b97-184">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-184">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f9b97-185">Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f9b97-185">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="f9b97-186">Routage</span><span class="sxs-lookup"><span data-stu-id="f9b97-186">Route</span></span>                            | <span data-ttu-id="f9b97-187">Fragment</span><span class="sxs-lookup"><span data-stu-id="f9b97-187">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="f9b97-188">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f9b97-188">Customize the user</span></span>

<span data-ttu-id="f9b97-189">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="f9b97-189">Users bound to the app can be customized.</span></span> <span data-ttu-id="f9b97-190">Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent `amr` une revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-190">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="f9b97-191">Créez une classe qui étend la `RemoteUserAccount` classe :</span><span class="sxs-lookup"><span data-stu-id="f9b97-191">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="f9b97-192">Créez une fabrique qui étend `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="f9b97-192">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public AccountClaimsPrincipalFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="f9b97-193">Inscrivez les services pour utiliser `CustomAccountFactory`le :</span><span class="sxs-lookup"><span data-stu-id="f9b97-193">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="f9b97-194">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="f9b97-194">Support prerendering with authentication</span></span>

<span data-ttu-id="f9b97-195">Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="f9b97-195">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="f9b97-196">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="f9b97-196">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="f9b97-197">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="f9b97-197">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="f9b97-198">Dans la classe de `Program` l’application cliente (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte `ConfigureCommonServices`(par exemple,) :</span><span class="sxs-lookup"><span data-stu-id="f9b97-198">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="f9b97-199">Dans l’application serveur `Startup.ConfigureServices`, inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="f9b97-199">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="f9b97-200">Dans la méthode de `Startup.Configure` l’application serveur, `endpoints.MapFallbackToFile("index.html")` remplacez `endpoints.MapFallbackToPage("/_Host")`par :</span><span class="sxs-lookup"><span data-stu-id="f9b97-200">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="f9b97-201">Dans l’application serveur, créez un dossier *pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="f9b97-201">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="f9b97-202">Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-202">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="f9b97-203">Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f9b97-203">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="f9b97-204">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="f9b97-204">Update the file's contents:</span></span>

* <span data-ttu-id="f9b97-205">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="f9b97-205">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="f9b97-206">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="f9b97-206">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="f9b97-207">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="f9b97-207">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="f9b97-208">Lors de l’authentification et de l' Blazor autorisation d’une application webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-208">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="f9b97-209">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="f9b97-209">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="f9b97-210">Pour plus d’informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f9b97-210">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="f9b97-211">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="f9b97-211">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="f9b97-212">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="f9b97-212">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="f9b97-213">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="f9b97-213">In this scenario:</span></span>

* <span data-ttu-id="f9b97-214">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="f9b97-214">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="f9b97-215">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="f9b97-215">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="f9b97-216">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="f9b97-216">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="f9b97-217">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="f9b97-217">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="f9b97-218">Configurez l’identité avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="f9b97-218">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="f9b97-219">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="f9b97-219">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="f9b97-220">Lorsqu’un utilisateur se connecte, l’identité collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="f9b97-220">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="f9b97-221">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="f9b97-221">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="f9b97-222">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="f9b97-222">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="f9b97-223">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-223">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="f9b97-224">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de l’identité sur le client.</span><span class="sxs-lookup"><span data-stu-id="f9b97-224">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="f9b97-225">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="f9b97-225">We don't recommend this approach.</span></span> <span data-ttu-id="f9b97-226">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="f9b97-226">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="f9b97-227">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="f9b97-227">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="f9b97-228">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="f9b97-228">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="f9b97-229">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="f9b97-229">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="f9b97-230">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="f9b97-230">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="f9b97-231">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="f9b97-231">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="f9b97-232">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="f9b97-232">Make an API call from the client to the server API.</span></span> <span data-ttu-id="f9b97-233">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="f9b97-233">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="f9b97-234">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="f9b97-234">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="f9b97-235">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="f9b97-235">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="f9b97-236">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="f9b97-236">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
