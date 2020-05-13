---
title: Scénarios de sécurité supplémentaires pour l’ASP.NET Core Blazor Webassembly
author: guardrex
description: Découvrez comment configurer Blazor Webassembly pour des scénarios de sécurité supplémentaires.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153474"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ad1cd-103">Scénarios de sécurité supplémentaires ASP.NET Core éblouissant webassembly</span><span class="sxs-lookup"><span data-stu-id="ad1cd-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ad1cd-104">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ad1cd-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="ad1cd-105">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="ad1cd-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="ad1cd-106">Le `AuthorizationMessageHandler` service peut être utilisé avec `HttpClient` pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="ad1cd-107">Les jetons sont acquis à l’aide du `IAccessTokenProvider` service existant.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="ad1cd-108">Si un jeton ne peut pas être acquis, une `AccessTokenNotAvailableException` exception est levée.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="ad1cd-109">`AccessTokenNotAvailableException`dispose d’une `Redirect` méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="ad1cd-110">`AuthorizationMessageHandler`Peut être configuré avec les URL, les portées et l’URL de retour autorisées à l’aide de la `ConfigureHandler` méthode.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="ad1cd-111">Dans l’exemple suivant, `AuthorizationMessageHandler` configure un `HttpClient` en `Program.Main` (*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

<span data-ttu-id="ad1cd-112">Pour plus de commodité, une `BaseAddressAuthorizationMessageHandler` est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="ad1cd-113">Les modèles de webassembly éblouissants compatibles avec l’authentification utilisent désormais <xref:System.Net.Http.IHttpClientFactory> dans le projet d’API serveur pour configurer un <xref:System.Net.Http.HttpClient> avec les `BaseAddressAuthorizationMessageHandler` éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="ad1cd-114">Dans le cas où le client est créé avec `CreateClient` dans l’exemple précédent, <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ad1cd-115">Le configuré <xref:System.Net.Http.HttpClient> est ensuite utilisé pour effectuer des demandes autorisées à l’aide d’un `try-catch` modèle simple.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="ad1cd-116">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-116">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="ad1cd-117">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="ad1cd-117">Typed HttpClient</span></span>

<span data-ttu-id="ad1cd-118">Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-118">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="ad1cd-119">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ad1cd-119">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="ad1cd-120">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-120">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="ad1cd-121">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="ad1cd-122">Configurer le gestionnaire HttpClient</span><span class="sxs-lookup"><span data-stu-id="ad1cd-122">Configure the HttpClient handler</span></span>

<span data-ttu-id="ad1cd-123">Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-123">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="ad1cd-124">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-124">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="ad1cd-125">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="ad1cd-125">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="ad1cd-126">Si l’application de webassembly éblouissant utilise habituellement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-126">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="ad1cd-127">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-127">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ad1cd-128">L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-128">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="ad1cd-129">Un composant crée le à <xref:System.Net.Http.HttpClient> partir du <xref:System.Net.Http.IHttpClientFactory> pour effectuer des demandes non authentifiées ou non autorisées :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-129">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="ad1cd-130">Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-130">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ad1cd-131">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ad1cd-131">Request additional access tokens</span></span>

<span data-ttu-id="ad1cd-132">Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-132">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="ad1cd-133">Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-133">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ad1cd-134">Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-134">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="ad1cd-135">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-135">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ad1cd-136">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-136">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="ad1cd-137">Dans un composant Razor :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-137">In a Razor component:</span></span>

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

<span data-ttu-id="ad1cd-138">`TryGetToken`Cette</span><span class="sxs-lookup"><span data-stu-id="ad1cd-138">`TryGetToken` returns:</span></span>

* <span data-ttu-id="ad1cd-139">`true`avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-139">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ad1cd-140">`false`Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-140">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ad1cd-141">HttpClient et HttpRequestMessage avec les options de demande d’API Fetch</span><span class="sxs-lookup"><span data-stu-id="ad1cd-141">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ad1cd-142">Lors de l’exécution sur webassembly dans une application de webassembly éblouissante, [httpclient](xref:fundamentals/http-requests) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-142">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="ad1cd-143">Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-143">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="ad1cd-144">Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-144">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="ad1cd-145">L’implémentation de .NET webassembly de `HttpClient` utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-145">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="ad1cd-146">L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-146">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="ad1cd-147">Les options de requête HTTP FETCH peuvent être configurées avec `HttpRequestMessage` les méthodes d’extension indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-147">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="ad1cd-148">`HttpRequestMessage`méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="ad1cd-148">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="ad1cd-149">Propriété de requête Fetch</span><span class="sxs-lookup"><span data-stu-id="ad1cd-149">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="ad1cd-150">informations d’identification</span><span class="sxs-lookup"><span data-stu-id="ad1cd-150">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="ad1cd-151">en</span><span class="sxs-lookup"><span data-stu-id="ad1cd-151">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="ad1cd-152">mode</span><span class="sxs-lookup"><span data-stu-id="ad1cd-152">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="ad1cd-153">garantis</span><span class="sxs-lookup"><span data-stu-id="ad1cd-153">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="ad1cd-154">Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique `SetBrowserRequestOption` .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-154">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="ad1cd-155">La réponse HTTP est généralement mise en mémoire tampon dans une application de webassembly éblouissante pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-155">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="ad1cd-156">Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la `SetBrowserResponseStreamingEnabled` méthode d’extension sur la demande.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-156">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="ad1cd-157">Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la `SetBrowserRequestCredentials` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-157">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="ad1cd-158">Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-158">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ad1cd-159">Lors de l’envoi d’informations d’identification (cookies/en-têtes d’autorisation) sur les demandes CORS, l' `Authorization` en-tête doit être autorisé par la stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-159">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ad1cd-160">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-160">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ad1cd-161">Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-161">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ad1cd-162">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-162">Any method (verb).</span></span>
* <span data-ttu-id="ad1cd-163">`Content-Type`et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-163">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ad1cd-164">Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-164">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ad1cd-165">Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-165">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ad1cd-166">Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application (*composants/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ad1cd-166">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ad1cd-167">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="ad1cd-167">Handle token request errors</span></span>

<span data-ttu-id="ad1cd-168">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le fournisseur d’identité (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-168">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ad1cd-169">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-169">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ad1cd-170">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-170">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ad1cd-171">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-171">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ad1cd-172">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-172">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ad1cd-173">Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-173">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ad1cd-174">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-174">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ad1cd-175">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-175">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ad1cd-176">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-176">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ad1cd-177">Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-177">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ad1cd-178">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-178">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ad1cd-179">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-179">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ad1cd-180">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-180">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ad1cd-181">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-181">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ad1cd-182">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-182">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ad1cd-183">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-183">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ad1cd-184">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-184">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ad1cd-185">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-185">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ad1cd-186">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-186">Store the current page state in session storage.</span></span> <span data-ttu-id="ad1cd-187">Pendant `OnInitializeAsync` , vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-187">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ad1cd-188">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-188">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ad1cd-189">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-189">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ad1cd-190">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-190">The following example shows how to:</span></span>

* <span data-ttu-id="ad1cd-191">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-191">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ad1cd-192">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-192">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ad1cd-193">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="ad1cd-193">Save app state before an authentication operation</span></span>

<span data-ttu-id="ad1cd-194">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-194">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ad1cd-195">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-195">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ad1cd-196">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-196">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ad1cd-197">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-197">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="ad1cd-198">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="ad1cd-198">Customize app routes</span></span>

<span data-ttu-id="ad1cd-199">Par défaut, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-199">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ad1cd-200">Routage</span><span class="sxs-lookup"><span data-stu-id="ad1cd-200">Route</span></span>                            | <span data-ttu-id="ad1cd-201">Objectif</span><span class="sxs-lookup"><span data-stu-id="ad1cd-201">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ad1cd-202">Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-202">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ad1cd-203">Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-203">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ad1cd-204">Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-204">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ad1cd-205">Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-205">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ad1cd-206">Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-206">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ad1cd-207">Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-207">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ad1cd-208">Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-208">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ad1cd-209">Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-209">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ad1cd-210">Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-210">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ad1cd-211">Les itinéraires indiqués dans le tableau précédent sont configurables via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-211">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="ad1cd-212">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-212">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ad1cd-213">Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-213">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ad1cd-214">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-214">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ad1cd-215">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-215">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ad1cd-216">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le `RemoteAuthenticatorView` avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-216">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ad1cd-217">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-217">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ad1cd-218">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="ad1cd-218">Customize the authentication user interface</span></span>

<span data-ttu-id="ad1cd-219">`RemoteAuthenticatorView`comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-219">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ad1cd-220">Chaque État peut être personnalisé en passant un personnalisé `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-220">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="ad1cd-221">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le `RemoteAuthenticatorView` comme suit.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-221">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="ad1cd-222">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ad1cd-223">Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-223">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ad1cd-224">Routage</span><span class="sxs-lookup"><span data-stu-id="ad1cd-224">Route</span></span>                            | <span data-ttu-id="ad1cd-225">Fragment</span><span class="sxs-lookup"><span data-stu-id="ad1cd-225">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="ad1cd-226">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="ad1cd-226">Customize the user</span></span>

<span data-ttu-id="ad1cd-227">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-227">Users bound to the app can be customized.</span></span> <span data-ttu-id="ad1cd-228">Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-228">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="ad1cd-229">Créez une classe qui étend la `RemoteUserAccount` classe :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-229">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="ad1cd-230">Créez une fabrique qui étend `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-230">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="ad1cd-231">Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-231">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="ad1cd-232">Les inscriptions suivantes sont valides :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-232">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="ad1cd-233">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ad1cd-233">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="ad1cd-234">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ad1cd-234">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="ad1cd-235">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="ad1cd-235">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="ad1cd-236">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="ad1cd-236">Support prerendering with authentication</span></span>

<span data-ttu-id="ad1cd-237">Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application Webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-237">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="ad1cd-238">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-238">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="ad1cd-239">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-239">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="ad1cd-240">Dans la classe de l’application cliente `Program` (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-240">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="ad1cd-241">Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-241">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="ad1cd-242">Dans la méthode de l’application serveur `Startup.Configure` , remplacez `endpoints.MapFallbackToFile("index.html")` par `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-242">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="ad1cd-243">Dans l’application serveur, créez un dossier *pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-243">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="ad1cd-244">Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-244">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="ad1cd-245">Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ad1cd-245">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="ad1cd-246">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-246">Update the file's contents:</span></span>

* <span data-ttu-id="ad1cd-247">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-247">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="ad1cd-248">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-248">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="ad1cd-249">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="ad1cd-249">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="ad1cd-250">Lors de l’authentification et de l’autorisation d’une Blazor application Webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-250">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="ad1cd-251">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-251">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="ad1cd-252">Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-252">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="ad1cd-253">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="ad1cd-253">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="ad1cd-254">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-254">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="ad1cd-255">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-255">In this scenario:</span></span>

* <span data-ttu-id="ad1cd-256">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-256">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="ad1cd-257">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-257">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="ad1cd-258">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-258">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="ad1cd-259">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="ad1cd-259">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="ad1cd-260">Configurez Identity avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-260">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="ad1cd-261">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-261">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="ad1cd-262">Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-262">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="ad1cd-263">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-263">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="ad1cd-264">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="ad1cd-264">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="ad1cd-265">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-265">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="ad1cd-266">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-266">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="ad1cd-267">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-267">We don't recommend this approach.</span></span> <span data-ttu-id="ad1cd-268">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-268">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="ad1cd-269">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-269">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="ad1cd-270">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-270">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="ad1cd-271">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-271">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="ad1cd-272">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-272">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="ad1cd-273">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="ad1cd-273">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="ad1cd-274">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-274">Make an API call from the client to the server API.</span></span> <span data-ttu-id="ad1cd-275">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-275">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="ad1cd-276">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="ad1cd-276">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="ad1cd-277">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-277">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="ad1cd-278">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="ad1cd-278">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
