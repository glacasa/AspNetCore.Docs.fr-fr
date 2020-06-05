---
title: Scénarios de sécurité supplémentaires pour l’ASP.NET Core Blazor Webassembly
author: guardrex
description: Découvrez comment configurer Blazor Webassembly pour des scénarios de sécurité supplémentaires.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: de752eb180767bbb269107ebc478a4422448f968
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272032"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ce083-103">Scénarios de sécurité supplémentaires pour l’ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="ce083-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ce083-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce083-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ce083-105">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="ce083-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ce083-106">Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service peut être utilisé avec <xref:System.Net.Http.HttpClient> pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="ce083-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ce083-107">Les jetons sont acquis à l’aide du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service existant.</span><span class="sxs-lookup"><span data-stu-id="ce083-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="ce083-108">Si un jeton ne peut pas être acquis, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> exception est levée.</span><span class="sxs-lookup"><span data-stu-id="ce083-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="ce083-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>dispose d’une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="ce083-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ce083-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Peut être configuré avec les URL, les portées et l’URL de retour autorisées à l’aide de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="ce083-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="ce083-111">Utilisez l’une des approches suivantes pour configurer un gestionnaire de messages pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="ce083-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="ce083-112">[Classe AuthorizationMessageHandler personnalisée](#custom-authorizationmessagehandler-class) (*recommandée*)</span><span class="sxs-lookup"><span data-stu-id="ce083-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="ce083-113">Configurer AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ce083-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="ce083-114">Classe AuthorizationMessageHandler personnalisée</span><span class="sxs-lookup"><span data-stu-id="ce083-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="ce083-115">Dans l’exemple suivant, une classe personnalisée étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> qui peut être utilisée pour configurer un <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ce083-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="ce083-116">Dans `Program.Main` (*Program.cs*), un <xref:System.Net.Http.HttpClient> est configuré avec le gestionnaire de messages d’autorisation personnalisé :</span><span class="sxs-lookup"><span data-stu-id="ce083-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="ce083-117">Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du modèle [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="ce083-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="ce083-118">Lorsque le client est créé avec <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (package[Microsoft. extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), le <xref:System.Net.Http.HttpClient> est fourni par des instances qui incluent des jetons d’accès lors de l’exécution de requêtes à l’API serveur :</span><span class="sxs-lookup"><span data-stu-id="ce083-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="ce083-119">Configurer AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="ce083-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="ce083-120">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configure un <xref:System.Net.Http.HttpClient> en `Program.Main` (*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="ce083-121">Pour plus de commodité, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="ce083-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ce083-122">Les Blazor modèles Webassembly compatibles avec l’authentification utilisent désormais <xref:System.Net.Http.IHttpClientFactory> (package[Microsoft. extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) dans le projet d’API serveur pour configurer un <xref:System.Net.Http.HttpClient> avec les <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ce083-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="ce083-123">Dans le cas où le client est créé avec <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> dans l’exemple précédent, <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="ce083-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ce083-124">Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du modèle [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="ce083-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="ce083-125">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="ce083-125">Typed HttpClient</span></span>

<span data-ttu-id="ce083-126">Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="ce083-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ce083-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ce083-127">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="ce083-128">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-128">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ce083-129">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-129">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ce083-130">Configurer le gestionnaire HttpClient</span><span class="sxs-lookup"><span data-stu-id="ce083-130">Configure the HttpClient handler</span></span>

<span data-ttu-id="ce083-131">Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="ce083-131">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ce083-132">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ce083-133">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="ce083-133">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ce083-134">Si l' Blazor application Webassembly utilise généralement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ce083-134">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ce083-135">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-135">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ce083-136">L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ce083-136">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ce083-137">Un composant crée le <xref:System.Net.Http.HttpClient> à partir du <xref:System.Net.Http.IHttpClientFactory> package ([Microsoft. extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) pour effectuer des demandes non authentifiées ou non autorisées :</span><span class="sxs-lookup"><span data-stu-id="ce083-137">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="ce083-138">Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="ce083-138">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ce083-139">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ce083-139">Request additional access tokens</span></span>

<span data-ttu-id="ce083-140">Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="ce083-140">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="ce083-141">Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="ce083-141">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ce083-142">Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="ce083-142">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="ce083-143">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-143">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ce083-144">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.</span><span class="sxs-lookup"><span data-stu-id="ce083-144">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ce083-145">Dans un Razor composant :</span><span class="sxs-lookup"><span data-stu-id="ce083-145">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

<span data-ttu-id="ce083-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Cette</span><span class="sxs-lookup"><span data-stu-id="ce083-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="ce083-147">`true`avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="ce083-147">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ce083-148">`false`Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="ce083-148">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ce083-149">HttpClient et HttpRequestMessage avec les options de demande d’API Fetch</span><span class="sxs-lookup"><span data-stu-id="ce083-149">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ce083-150">Lors de l’exécution sur webassembly dans une Blazor application Webassembly, [httpclient](xref:fundamentals/http-requests) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes.</span><span class="sxs-lookup"><span data-stu-id="ce083-150">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="ce083-151">Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="ce083-151">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="ce083-152">Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="ce083-152">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="ce083-153">L’implémentation de .NET webassembly de <xref:System.Net.Http.HttpClient> utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="ce083-153">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="ce083-154">L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ce083-154">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="ce083-155">Les options de requête HTTP FETCH peuvent être configurées avec <xref:System.Net.Http.HttpRequestMessage> les méthodes d’extension indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ce083-155">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="ce083-156">Méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="ce083-156">Extension method</span></span> | <span data-ttu-id="ce083-157">Propriété de requête Fetch</span><span class="sxs-lookup"><span data-stu-id="ce083-157">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="ce083-158">credentials</span><span class="sxs-lookup"><span data-stu-id="ce083-158">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="ce083-159">en</span><span class="sxs-lookup"><span data-stu-id="ce083-159">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="ce083-160">mode</span><span class="sxs-lookup"><span data-stu-id="ce083-160">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="ce083-161">garantis</span><span class="sxs-lookup"><span data-stu-id="ce083-161">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="ce083-162">Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="ce083-162">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="ce083-163">La réponse HTTP est généralement mise en mémoire tampon dans une Blazor application Webassembly pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="ce083-163">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="ce083-164">Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> méthode d’extension sur la demande.</span><span class="sxs-lookup"><span data-stu-id="ce083-164">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="ce083-165">Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="ce083-165">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="ce083-166">Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ce083-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ce083-167">Lors de l’envoi d’informations d’identification (cookies/en-têtes d’autorisation) sur les demandes CORS, l' `Authorization` en-tête doit être autorisé par la stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="ce083-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ce083-168">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="ce083-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ce083-169">Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="ce083-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ce083-170">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="ce083-170">Any method (verb).</span></span>
* <span data-ttu-id="ce083-171">`Content-Type`et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="ce083-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ce083-172">Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="ce083-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ce083-173">Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).</span><span class="sxs-lookup"><span data-stu-id="ce083-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ce083-174">Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application (*composants/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ce083-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ce083-175">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="ce083-175">Handle token request errors</span></span>

<span data-ttu-id="ce083-176">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le Identity fournisseur (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="ce083-176">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ce083-177">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="ce083-177">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ce083-178">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="ce083-178">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ce083-179">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ce083-179">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ce083-180">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ce083-180">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ce083-181">Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="ce083-181">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ce083-182">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="ce083-182">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ce083-183">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="ce083-183">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ce083-184">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="ce083-184">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ce083-185">Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="ce083-185">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ce083-186">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ce083-186">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ce083-187">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="ce083-187">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ce083-188">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ce083-188">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ce083-189">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="ce083-189">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ce083-190">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="ce083-190">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ce083-191">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="ce083-191">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ce083-192">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="ce083-192">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ce083-193">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="ce083-193">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ce083-194">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="ce083-194">Store the current page state in session storage.</span></span> <span data-ttu-id="ce083-195">Pendant l' [événement de cycle de vie OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="ce083-195">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ce083-196">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="ce083-196">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ce083-197">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="ce083-197">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ce083-198">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="ce083-198">The following example shows how to:</span></span>

* <span data-ttu-id="ce083-199">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="ce083-199">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ce083-200">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="ce083-200">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ce083-201">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="ce083-201">Save app state before an authentication operation</span></span>

<span data-ttu-id="ce083-202">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ce083-202">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ce083-203">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="ce083-203">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ce083-204">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="ce083-204">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ce083-205">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-205">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="ce083-206">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="ce083-206">Customize app routes</span></span>

<span data-ttu-id="ce083-207">Par défaut, la bibliothèque [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="ce083-207">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ce083-208">Routage</span><span class="sxs-lookup"><span data-stu-id="ce083-208">Route</span></span>                            | <span data-ttu-id="ce083-209">Objectif</span><span class="sxs-lookup"><span data-stu-id="ce083-209">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ce083-210">Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="ce083-210">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ce083-211">Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="ce083-211">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ce083-212">Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="ce083-212">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ce083-213">Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="ce083-213">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ce083-214">Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="ce083-214">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ce083-215">Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="ce083-215">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ce083-216">Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="ce083-216">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ce083-217">Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ce083-217">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ce083-218">Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ce083-218">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ce083-219">Les itinéraires indiqués dans le tableau précédent sont configurables via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce083-219">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ce083-220">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="ce083-220">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ce083-221">Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .</span><span class="sxs-lookup"><span data-stu-id="ce083-221">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ce083-222">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ce083-223">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-223">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ce083-224">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="ce083-224">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ce083-225">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="ce083-225">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ce083-226">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="ce083-226">Customize the authentication user interface</span></span>

<span data-ttu-id="ce083-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ce083-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ce083-228">Chaque État peut être personnalisé en passant un personnalisé <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="ce083-228">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="ce083-229">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comme suit.</span><span class="sxs-lookup"><span data-stu-id="ce083-229">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="ce083-230">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-230">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ce083-231">Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ce083-231">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ce083-232">Routage</span><span class="sxs-lookup"><span data-stu-id="ce083-232">Route</span></span>                            | <span data-ttu-id="ce083-233">Fragment</span><span class="sxs-lookup"><span data-stu-id="ce083-233">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="ce083-234">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="ce083-234">Customize the user</span></span>

<span data-ttu-id="ce083-235">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="ce083-235">Users bound to the app can be customized.</span></span> <span data-ttu-id="ce083-236">Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ce083-236">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ce083-237">Créez une classe qui étend la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe :</span><span class="sxs-lookup"><span data-stu-id="ce083-237">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ce083-238">Créez une fabrique qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="ce083-238">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="ce083-239">Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="ce083-239">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ce083-240">Les inscriptions suivantes sont valides :</span><span class="sxs-lookup"><span data-stu-id="ce083-240">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="ce083-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ce083-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="ce083-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="ce083-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="ce083-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="ce083-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ce083-244">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="ce083-244">Support prerendering with authentication</span></span>

<span data-ttu-id="ce083-245">Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application Webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="ce083-245">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ce083-246">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="ce083-246">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ce083-247">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="ce083-247">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ce083-248">Dans la classe de l’application cliente `Program` (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :</span><span class="sxs-lookup"><span data-stu-id="ce083-248">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

<span data-ttu-id="ce083-249">Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="ce083-249">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="ce083-250">Dans la méthode de l’application serveur `Startup.Configure` , remplacez les [points de terminaison. MapFallbackToFile (« index. html »)](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) avec des [points de terminaison. MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="ce083-250">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ce083-251">Dans l’application serveur, créez un dossier *pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="ce083-251">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ce083-252">Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="ce083-252">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ce083-253">Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ce083-253">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ce083-254">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="ce083-254">Update the file's contents:</span></span>

* <span data-ttu-id="ce083-255">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="ce083-255">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ce083-256">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="ce083-256">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ce083-257">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="ce083-257">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ce083-258">Lors de l’authentification et de l’autorisation d’une Blazor application Webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ce083-258">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ce083-259">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="ce083-259">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ce083-260">Pour plus d’informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ce083-260">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ce083-261">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="ce083-261">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ce083-262">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="ce083-262">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ce083-263">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="ce083-263">In this scenario:</span></span>

* <span data-ttu-id="ce083-264">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="ce083-264">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ce083-265">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="ce083-265">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ce083-266">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="ce083-266">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ce083-267">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="ce083-267">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ce083-268">Configurez Identity avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="ce083-268">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ce083-269">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="ce083-269">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ce083-270">Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ce083-270">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ce083-271">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="ce083-271">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ce083-272">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="ce083-272">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ce083-273">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="ce083-273">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ce083-274">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.</span><span class="sxs-lookup"><span data-stu-id="ce083-274">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ce083-275">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="ce083-275">We don't recommend this approach.</span></span> <span data-ttu-id="ce083-276">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="ce083-276">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ce083-277">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="ce083-277">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ce083-278">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="ce083-278">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ce083-279">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="ce083-279">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ce083-280">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="ce083-280">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ce083-281">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="ce083-281">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ce083-282">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="ce083-282">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ce083-283">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ce083-283">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ce083-284">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="ce083-284">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ce083-285">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="ce083-285">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ce083-286">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="ce083-286">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="ce083-287">Utiliser des points de terminaison OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="ce083-287">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="ce083-288">La bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="ce083-288">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="ce083-289">Pour utiliser un point de terminaison v 2.0, configurez l’option de support JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce083-289">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="ce083-290">Dans l’exemple suivant, AAD est configuré pour v 2.0 en ajoutant un `v2.0` segment à la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propriété :</span><span class="sxs-lookup"><span data-stu-id="ce083-290">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="ce083-291">Le paramètre peut également être défini dans le fichier de paramètres de l’application (*appSettings. JSON*) :</span><span class="sxs-lookup"><span data-stu-id="ce083-291">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="ce083-292">Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement.</span><span class="sxs-lookup"><span data-stu-id="ce083-292">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="ce083-293">Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou dans le fichier de paramètres de l’application (*appSettings. JSON*) avec la `Authority` clé.</span><span class="sxs-lookup"><span data-stu-id="ce083-293">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="ce083-294">La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0.</span><span class="sxs-lookup"><span data-stu-id="ce083-294">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="ce083-295">Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="ce083-295">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
