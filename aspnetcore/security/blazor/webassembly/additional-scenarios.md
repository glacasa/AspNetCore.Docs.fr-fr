---
<span data-ttu-id="25272-101">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-103">'Blazor'</span></span>
- <span data-ttu-id="25272-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-104">'Identity'</span></span>
- <span data-ttu-id="25272-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-106">'Razor'</span></span>
- <span data-ttu-id="25272-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="25272-108">Scénarios de sécurité supplémentaires pour l’ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="25272-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="25272-109">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="25272-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="25272-110">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="25272-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="25272-111">Le `AuthorizationMessageHandler` service peut être utilisé avec `HttpClient` pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="25272-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="25272-112">Les jetons sont acquis à l’aide du `IAccessTokenProvider` service existant.</span><span class="sxs-lookup"><span data-stu-id="25272-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="25272-113">Si un jeton ne peut pas être acquis, une `AccessTokenNotAvailableException` exception est levée.</span><span class="sxs-lookup"><span data-stu-id="25272-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="25272-114">`AccessTokenNotAvailableException`dispose d’une `Redirect` méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="25272-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="25272-115">`AuthorizationMessageHandler`Peut être configuré avec les URL, les portées et l’URL de retour autorisées à l’aide de la `ConfigureHandler` méthode.</span><span class="sxs-lookup"><span data-stu-id="25272-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="25272-116">Dans l’exemple suivant, `AuthorizationMessageHandler` configure un `HttpClient` en `Program.Main` (*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="25272-117">Pour plus de commodité, une `BaseAddressAuthorizationMessageHandler` est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="25272-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="25272-118">Les Blazor modèles Webassembly compatibles avec l’authentification utilisent désormais <xref:System.Net.Http.IHttpClientFactory> dans le projet d’API serveur pour configurer un <xref:System.Net.Http.HttpClient> avec les `BaseAddressAuthorizationMessageHandler` éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="25272-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="25272-119">Dans le cas où le client est créé avec `CreateClient` dans l’exemple précédent, <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="25272-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="25272-120">Le configuré <xref:System.Net.Http.HttpClient> est ensuite utilisé pour effectuer des demandes autorisées à l’aide d’un `try-catch` modèle simple.</span><span class="sxs-lookup"><span data-stu-id="25272-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="25272-121">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="25272-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="25272-122">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="25272-122">Typed HttpClient</span></span>

<span data-ttu-id="25272-123">Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="25272-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="25272-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="25272-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="25272-125">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="25272-126">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="25272-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="25272-127">Configurer le gestionnaire HttpClient</span><span class="sxs-lookup"><span data-stu-id="25272-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="25272-128">Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="25272-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="25272-129">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="25272-130">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="25272-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="25272-131">Si l' Blazor application Webassembly utilise généralement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="25272-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="25272-132">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="25272-133">L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="25272-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="25272-134">Un composant crée le à <xref:System.Net.Http.HttpClient> partir du <xref:System.Net.Http.IHttpClientFactory> pour effectuer des demandes non authentifiées ou non autorisées :</span><span class="sxs-lookup"><span data-stu-id="25272-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="25272-135">Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="25272-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="25272-136">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="25272-136">Request additional access tokens</span></span>

<span data-ttu-id="25272-137">Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="25272-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="25272-138">Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="25272-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="25272-139">Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="25272-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="25272-140">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="25272-141">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.</span><span class="sxs-lookup"><span data-stu-id="25272-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="25272-142">Dans un Razor composant :</span><span class="sxs-lookup"><span data-stu-id="25272-142">In a Razor component:</span></span>

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

<span data-ttu-id="25272-143">`TryGetToken`Cette</span><span class="sxs-lookup"><span data-stu-id="25272-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="25272-144">`true`avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="25272-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="25272-145">`false`Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="25272-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="25272-146">HttpClient et HttpRequestMessage avec les options de demande d’API Fetch</span><span class="sxs-lookup"><span data-stu-id="25272-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="25272-147">Lors de l’exécution sur webassembly dans une Blazor application Webassembly, [httpclient](xref:fundamentals/http-requests) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes.</span><span class="sxs-lookup"><span data-stu-id="25272-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="25272-148">Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="25272-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="25272-149">Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="25272-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="25272-150">L’implémentation de .NET webassembly de `HttpClient` utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="25272-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="25272-151">L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="25272-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="25272-152">Les options de requête HTTP FETCH peuvent être configurées avec `HttpRequestMessage` les méthodes d’extension indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="25272-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="25272-153">`HttpRequestMessage`méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="25272-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="25272-154">Propriété de requête Fetch</span><span class="sxs-lookup"><span data-stu-id="25272-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="25272-155">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-156">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-157">'Blazor'</span></span>
- <span data-ttu-id="25272-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-158">'Identity'</span></span>
- <span data-ttu-id="25272-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-160">'Razor'</span></span>
- <span data-ttu-id="25272-161">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-162">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-163">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-164">'Blazor'</span></span>
- <span data-ttu-id="25272-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-165">'Identity'</span></span>
- <span data-ttu-id="25272-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-167">'Razor'</span></span>
- <span data-ttu-id="25272-168">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-169">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-170">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-171">'Blazor'</span></span>
- <span data-ttu-id="25272-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-172">'Identity'</span></span>
- <span data-ttu-id="25272-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-174">'Razor'</span></span>
- <span data-ttu-id="25272-175">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-176">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-177">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-178">'Blazor'</span></span>
- <span data-ttu-id="25272-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-179">'Identity'</span></span>
- <span data-ttu-id="25272-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-181">'Razor'</span></span>
- <span data-ttu-id="25272-182">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-183">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-184">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-185">'Blazor'</span></span>
- <span data-ttu-id="25272-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-186">'Identity'</span></span>
- <span data-ttu-id="25272-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-188">'Razor'</span></span>
- <span data-ttu-id="25272-189">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-190">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-191">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-192">'Blazor'</span></span>
- <span data-ttu-id="25272-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-193">'Identity'</span></span>
- <span data-ttu-id="25272-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-195">'Razor'</span></span>
- <span data-ttu-id="25272-196">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-197">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-198">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-199">'Blazor'</span></span>
- <span data-ttu-id="25272-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-200">'Identity'</span></span>
- <span data-ttu-id="25272-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-202">'Razor'</span></span>
- <span data-ttu-id="25272-203">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-204">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-205">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-206">'Blazor'</span></span>
- <span data-ttu-id="25272-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-207">'Identity'</span></span>
- <span data-ttu-id="25272-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-209">'Razor'</span></span>
- <span data-ttu-id="25272-210">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-211">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-212">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-213">'Blazor'</span></span>
- <span data-ttu-id="25272-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-214">'Identity'</span></span>
- <span data-ttu-id="25272-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-216">'Razor'</span></span>
- <span data-ttu-id="25272-217">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-218">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-219">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-220">'Blazor'</span></span>
- <span data-ttu-id="25272-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-221">'Identity'</span></span>
- <span data-ttu-id="25272-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-223">'Razor'</span></span>
- <span data-ttu-id="25272-224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-225">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-226">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-227">'Blazor'</span></span>
- <span data-ttu-id="25272-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-228">'Identity'</span></span>
- <span data-ttu-id="25272-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-230">'Razor'</span></span>
- <span data-ttu-id="25272-231">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-232">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-233">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-234">'Blazor'</span></span>
- <span data-ttu-id="25272-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-235">'Identity'</span></span>
- <span data-ttu-id="25272-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-237">'Razor'</span></span>
- <span data-ttu-id="25272-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-239">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-240">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-241">'Blazor'</span></span>
- <span data-ttu-id="25272-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-242">'Identity'</span></span>
- <span data-ttu-id="25272-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-244">'Razor'</span></span>
- <span data-ttu-id="25272-245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-246">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-247">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-248">'Blazor'</span></span>
- <span data-ttu-id="25272-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-249">'Identity'</span></span>
- <span data-ttu-id="25272-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-251">'Razor'</span></span>
- <span data-ttu-id="25272-252">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-253">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-254">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-255">'Blazor'</span></span>
- <span data-ttu-id="25272-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-256">'Identity'</span></span>
- <span data-ttu-id="25272-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-258">'Razor'</span></span>
- <span data-ttu-id="25272-259">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-260">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-261">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-262">'Blazor'</span></span>
- <span data-ttu-id="25272-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-263">'Identity'</span></span>
- <span data-ttu-id="25272-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-265">'Razor'</span></span>
- <span data-ttu-id="25272-266">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-266">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-267">------------------- | titre de la--- : 'ASP.NET Core Blazor les scénarios de sécurité supplémentaires du Webassembly’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-268">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-269">'Blazor'</span></span>
- <span data-ttu-id="25272-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-270">'Identity'</span></span>
- <span data-ttu-id="25272-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-272">'Razor'</span></span>
- <span data-ttu-id="25272-273">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-274">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-275">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-276">'Blazor'</span></span>
- <span data-ttu-id="25272-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-277">'Identity'</span></span>
- <span data-ttu-id="25272-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-279">'Razor'</span></span>
- <span data-ttu-id="25272-280">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-281">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-282">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-283">'Blazor'</span></span>
- <span data-ttu-id="25272-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-284">'Identity'</span></span>
- <span data-ttu-id="25272-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-286">'Razor'</span></span>
- <span data-ttu-id="25272-287">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-288">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-289">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-290">'Blazor'</span></span>
- <span data-ttu-id="25272-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-291">'Identity'</span></span>
- <span data-ttu-id="25272-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-293">'Razor'</span></span>
- <span data-ttu-id="25272-294">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-295">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-296">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-297">'Blazor'</span></span>
- <span data-ttu-id="25272-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-298">'Identity'</span></span>
- <span data-ttu-id="25272-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-300">'Razor'</span></span>
- <span data-ttu-id="25272-301">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-302">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-303">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-304">'Blazor'</span></span>
- <span data-ttu-id="25272-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-305">'Identity'</span></span>
- <span data-ttu-id="25272-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-307">'Razor'</span></span>
- <span data-ttu-id="25272-308">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-309">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-310">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-311">'Blazor'</span></span>
- <span data-ttu-id="25272-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-312">'Identity'</span></span>
- <span data-ttu-id="25272-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-314">'Razor'</span></span>
- <span data-ttu-id="25272-315">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-316">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-317">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-318">'Blazor'</span></span>
- <span data-ttu-id="25272-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-319">'Identity'</span></span>
- <span data-ttu-id="25272-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-321">'Razor'</span></span>
- <span data-ttu-id="25272-322">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-323">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-324">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-325">'Blazor'</span></span>
- <span data-ttu-id="25272-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-326">'Identity'</span></span>
- <span data-ttu-id="25272-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-328">'Razor'</span></span>
- <span data-ttu-id="25272-329">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-329">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-330">----------- | | `SetBrowserRequestCredentials`         |  [informations d’identification](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`               |  [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [intégrité](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="25272-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="25272-331">Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique `SetBrowserRequestOption` .</span><span class="sxs-lookup"><span data-stu-id="25272-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="25272-332">La réponse HTTP est généralement mise en mémoire tampon dans une Blazor application Webassembly pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="25272-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="25272-333">Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la `SetBrowserResponseStreamingEnabled` méthode d’extension sur la demande.</span><span class="sxs-lookup"><span data-stu-id="25272-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="25272-334">Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la `SetBrowserRequestCredentials` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="25272-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="25272-335">Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="25272-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="25272-336">Lors de l’envoi d’informations d’identification (cookies/en-têtes d’autorisation) sur les demandes CORS, l' `Authorization` en-tête doit être autorisé par la stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="25272-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="25272-337">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="25272-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="25272-338">Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="25272-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="25272-339">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="25272-339">Any method (verb).</span></span>
* <span data-ttu-id="25272-340">`Content-Type`et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="25272-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="25272-341">Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="25272-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="25272-342">Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).</span><span class="sxs-lookup"><span data-stu-id="25272-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="25272-343">Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application (*composants/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="25272-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="25272-344">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="25272-344">Handle token request errors</span></span>

<span data-ttu-id="25272-345">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le Identity fournisseur (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="25272-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="25272-346">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="25272-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="25272-347">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="25272-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="25272-348">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="25272-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="25272-349">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="25272-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="25272-350">Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="25272-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="25272-351">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="25272-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="25272-352">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="25272-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="25272-353">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="25272-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="25272-354">Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="25272-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="25272-355">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="25272-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="25272-356">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="25272-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="25272-357">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25272-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="25272-358">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="25272-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="25272-359">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="25272-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="25272-360">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="25272-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="25272-361">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="25272-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="25272-362">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="25272-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="25272-363">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="25272-363">Store the current page state in session storage.</span></span> <span data-ttu-id="25272-364">Pendant `OnInitializeAsync` , vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="25272-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="25272-365">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="25272-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="25272-366">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="25272-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="25272-367">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="25272-367">The following example shows how to:</span></span>

* <span data-ttu-id="25272-368">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="25272-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="25272-369">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="25272-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="25272-370">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="25272-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="25272-371">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="25272-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="25272-372">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="25272-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="25272-373">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="25272-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="25272-374">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="25272-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="25272-375">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="25272-375">Customize app routes</span></span>

<span data-ttu-id="25272-376">Par défaut, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="25272-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="25272-377">Routage</span><span class="sxs-lookup"><span data-stu-id="25272-377">Route</span></span>                            | <span data-ttu-id="25272-378">Objectif</span><span class="sxs-lookup"><span data-stu-id="25272-378">Purpose</span></span> |
| ---
<span data-ttu-id="25272-379">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-380">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-381">'Blazor'</span></span>
- <span data-ttu-id="25272-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-382">'Identity'</span></span>
- <span data-ttu-id="25272-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-384">'Razor'</span></span>
- <span data-ttu-id="25272-385">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-386">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-387">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-388">'Blazor'</span></span>
- <span data-ttu-id="25272-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-389">'Identity'</span></span>
- <span data-ttu-id="25272-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-391">'Razor'</span></span>
- <span data-ttu-id="25272-392">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-393">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-394">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-395">'Blazor'</span></span>
- <span data-ttu-id="25272-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-396">'Identity'</span></span>
- <span data-ttu-id="25272-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-398">'Razor'</span></span>
- <span data-ttu-id="25272-399">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-400">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-401">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-402">'Blazor'</span></span>
- <span data-ttu-id="25272-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-403">'Identity'</span></span>
- <span data-ttu-id="25272-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-405">'Razor'</span></span>
- <span data-ttu-id="25272-406">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-407">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-408">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-409">'Blazor'</span></span>
- <span data-ttu-id="25272-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-410">'Identity'</span></span>
- <span data-ttu-id="25272-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-412">'Razor'</span></span>
- <span data-ttu-id="25272-413">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-414">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-415">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-416">'Blazor'</span></span>
- <span data-ttu-id="25272-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-417">'Identity'</span></span>
- <span data-ttu-id="25272-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-419">'Razor'</span></span>
- <span data-ttu-id="25272-420">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-421">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-422">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-423">'Blazor'</span></span>
- <span data-ttu-id="25272-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-424">'Identity'</span></span>
- <span data-ttu-id="25272-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-426">'Razor'</span></span>
- <span data-ttu-id="25272-427">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-428">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-429">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-430">'Blazor'</span></span>
- <span data-ttu-id="25272-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-431">'Identity'</span></span>
- <span data-ttu-id="25272-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-433">'Razor'</span></span>
- <span data-ttu-id="25272-434">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-435">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-436">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-437">'Blazor'</span></span>
- <span data-ttu-id="25272-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-438">'Identity'</span></span>
- <span data-ttu-id="25272-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-440">'Razor'</span></span>
- <span data-ttu-id="25272-441">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-442">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-443">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-444">'Blazor'</span></span>
- <span data-ttu-id="25272-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-445">'Identity'</span></span>
- <span data-ttu-id="25272-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-447">'Razor'</span></span>
- <span data-ttu-id="25272-448">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-449">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-450">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-451">'Blazor'</span></span>
- <span data-ttu-id="25272-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-452">'Identity'</span></span>
- <span data-ttu-id="25272-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-454">'Razor'</span></span>
- <span data-ttu-id="25272-455">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-456">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-457">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-458">'Blazor'</span></span>
- <span data-ttu-id="25272-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-459">'Identity'</span></span>
- <span data-ttu-id="25272-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-461">'Razor'</span></span>
- <span data-ttu-id="25272-462">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-463">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-464">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-465">'Blazor'</span></span>
- <span data-ttu-id="25272-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-466">'Identity'</span></span>
- <span data-ttu-id="25272-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-468">'Razor'</span></span>
- <span data-ttu-id="25272-469">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-470">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-471">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-472">'Blazor'</span></span>
- <span data-ttu-id="25272-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-473">'Identity'</span></span>
- <span data-ttu-id="25272-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-475">'Razor'</span></span>
- <span data-ttu-id="25272-476">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-476">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-477">---------------- | titre de la--- : 'ASP.NET Core Blazor les scénarios de sécurité supplémentaires du Webassembly’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-478">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-479">'Blazor'</span></span>
- <span data-ttu-id="25272-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-480">'Identity'</span></span>
- <span data-ttu-id="25272-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-482">'Razor'</span></span>
- <span data-ttu-id="25272-483">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-483">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-484">---- | | `authentication/login`           | Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="25272-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="25272-485">| | `authentication/login-callback`  | Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="25272-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="25272-486">| | `authentication/login-failed`    | Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="25272-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="25272-487">| | `authentication/logout`          | Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="25272-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="25272-488">| | `authentication/logout-callback` | Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="25272-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="25272-489">| | `authentication/logout-failed`   | Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="25272-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="25272-490">| | `authentication/logged-out`      | Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="25272-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="25272-491">| | `authentication/profile`         | Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25272-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="25272-492">| | `authentication/register`        | Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25272-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="25272-493">Les itinéraires indiqués dans le tableau précédent sont configurables via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="25272-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="25272-494">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="25272-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="25272-495">Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .</span><span class="sxs-lookup"><span data-stu-id="25272-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="25272-496">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="25272-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="25272-497">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="25272-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="25272-498">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le `RemoteAuthenticatorView` avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="25272-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="25272-499">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="25272-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="25272-500">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="25272-500">Customize the authentication user interface</span></span>

<span data-ttu-id="25272-501">`RemoteAuthenticatorView`comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="25272-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="25272-502">Chaque État peut être personnalisé en passant un personnalisé `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="25272-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="25272-503">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le `RemoteAuthenticatorView` comme suit.</span><span class="sxs-lookup"><span data-stu-id="25272-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="25272-504">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="25272-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="25272-505">Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="25272-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="25272-506">Routage</span><span class="sxs-lookup"><span data-stu-id="25272-506">Route</span></span>                            | <span data-ttu-id="25272-507">Fragment</span><span class="sxs-lookup"><span data-stu-id="25272-507">Fragment</span></span>                |
| ---
<span data-ttu-id="25272-508">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-509">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-510">'Blazor'</span></span>
- <span data-ttu-id="25272-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-511">'Identity'</span></span>
- <span data-ttu-id="25272-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-513">'Razor'</span></span>
- <span data-ttu-id="25272-514">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-515">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-516">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-517">'Blazor'</span></span>
- <span data-ttu-id="25272-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-518">'Identity'</span></span>
- <span data-ttu-id="25272-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-520">'Razor'</span></span>
- <span data-ttu-id="25272-521">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-522">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-523">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-524">'Blazor'</span></span>
- <span data-ttu-id="25272-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-525">'Identity'</span></span>
- <span data-ttu-id="25272-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-527">'Razor'</span></span>
- <span data-ttu-id="25272-528">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-529">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-530">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-531">'Blazor'</span></span>
- <span data-ttu-id="25272-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-532">'Identity'</span></span>
- <span data-ttu-id="25272-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-534">'Razor'</span></span>
- <span data-ttu-id="25272-535">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-536">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-537">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-538">'Blazor'</span></span>
- <span data-ttu-id="25272-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-539">'Identity'</span></span>
- <span data-ttu-id="25272-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-541">'Razor'</span></span>
- <span data-ttu-id="25272-542">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-543">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-544">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-545">'Blazor'</span></span>
- <span data-ttu-id="25272-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-546">'Identity'</span></span>
- <span data-ttu-id="25272-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-548">'Razor'</span></span>
- <span data-ttu-id="25272-549">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-550">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-551">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-552">'Blazor'</span></span>
- <span data-ttu-id="25272-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-553">'Identity'</span></span>
- <span data-ttu-id="25272-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-555">'Razor'</span></span>
- <span data-ttu-id="25272-556">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-557">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-558">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-559">'Blazor'</span></span>
- <span data-ttu-id="25272-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-560">'Identity'</span></span>
- <span data-ttu-id="25272-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-562">'Razor'</span></span>
- <span data-ttu-id="25272-563">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-564">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-565">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-566">'Blazor'</span></span>
- <span data-ttu-id="25272-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-567">'Identity'</span></span>
- <span data-ttu-id="25272-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-569">'Razor'</span></span>
- <span data-ttu-id="25272-570">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-571">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-572">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-573">'Blazor'</span></span>
- <span data-ttu-id="25272-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-574">'Identity'</span></span>
- <span data-ttu-id="25272-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-576">'Razor'</span></span>
- <span data-ttu-id="25272-577">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-578">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-579">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-580">'Blazor'</span></span>
- <span data-ttu-id="25272-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-581">'Identity'</span></span>
- <span data-ttu-id="25272-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-583">'Razor'</span></span>
- <span data-ttu-id="25272-584">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-585">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-586">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-587">'Blazor'</span></span>
- <span data-ttu-id="25272-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-588">'Identity'</span></span>
- <span data-ttu-id="25272-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-590">'Razor'</span></span>
- <span data-ttu-id="25272-591">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-592">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-593">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-594">'Blazor'</span></span>
- <span data-ttu-id="25272-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-595">'Identity'</span></span>
- <span data-ttu-id="25272-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-597">'Razor'</span></span>
- <span data-ttu-id="25272-598">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-599">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-600">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-601">'Blazor'</span></span>
- <span data-ttu-id="25272-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-602">'Identity'</span></span>
- <span data-ttu-id="25272-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-604">'Razor'</span></span>
- <span data-ttu-id="25272-605">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-605">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-606">---------------- | titre de la--- : 'ASP.NET Core Blazor les scénarios de sécurité supplémentaires du Webassembly’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-607">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-608">'Blazor'</span></span>
- <span data-ttu-id="25272-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-609">'Identity'</span></span>
- <span data-ttu-id="25272-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-611">'Razor'</span></span>
- <span data-ttu-id="25272-612">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-613">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-614">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-615">'Blazor'</span></span>
- <span data-ttu-id="25272-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-616">'Identity'</span></span>
- <span data-ttu-id="25272-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-618">'Razor'</span></span>
- <span data-ttu-id="25272-619">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-620">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-621">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-622">'Blazor'</span></span>
- <span data-ttu-id="25272-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-623">'Identity'</span></span>
- <span data-ttu-id="25272-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-625">'Razor'</span></span>
- <span data-ttu-id="25272-626">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-627">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-628">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-629">'Blazor'</span></span>
- <span data-ttu-id="25272-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-630">'Identity'</span></span>
- <span data-ttu-id="25272-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-632">'Razor'</span></span>
- <span data-ttu-id="25272-633">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-634">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-635">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-636">'Blazor'</span></span>
- <span data-ttu-id="25272-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-637">'Identity'</span></span>
- <span data-ttu-id="25272-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-639">'Razor'</span></span>
- <span data-ttu-id="25272-640">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-641">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-642">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-643">'Blazor'</span></span>
- <span data-ttu-id="25272-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-644">'Identity'</span></span>
- <span data-ttu-id="25272-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-646">'Razor'</span></span>
- <span data-ttu-id="25272-647">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-648">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-649">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-650">'Blazor'</span></span>
- <span data-ttu-id="25272-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-651">'Identity'</span></span>
- <span data-ttu-id="25272-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-653">'Razor'</span></span>
- <span data-ttu-id="25272-654">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-655">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-656">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-657">'Blazor'</span></span>
- <span data-ttu-id="25272-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-658">'Identity'</span></span>
- <span data-ttu-id="25272-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-660">'Razor'</span></span>
- <span data-ttu-id="25272-661">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25272-662">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="25272-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="25272-663">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="25272-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25272-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25272-664">'Blazor'</span></span>
- <span data-ttu-id="25272-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25272-665">'Identity'</span></span>
- <span data-ttu-id="25272-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25272-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="25272-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25272-667">'Razor'</span></span>
- <span data-ttu-id="25272-668">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="25272-668">'SignalR' uid:</span></span> 

<span data-ttu-id="25272-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="25272-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="25272-670">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="25272-670">Customize the user</span></span>

<span data-ttu-id="25272-671">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="25272-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="25272-672">Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25272-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="25272-673">Créez une classe qui étend la `RemoteUserAccount` classe :</span><span class="sxs-lookup"><span data-stu-id="25272-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="25272-674">Créez une fabrique qui étend `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="25272-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="25272-675">Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="25272-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="25272-676">Les inscriptions suivantes sont valides :</span><span class="sxs-lookup"><span data-stu-id="25272-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="25272-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="25272-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="25272-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="25272-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="25272-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="25272-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="25272-680">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="25272-680">Support prerendering with authentication</span></span>

<span data-ttu-id="25272-681">Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application Webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="25272-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="25272-682">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="25272-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="25272-683">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="25272-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="25272-684">Dans la classe de l’application cliente `Program` (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :</span><span class="sxs-lookup"><span data-stu-id="25272-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="25272-685">Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="25272-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="25272-686">Dans la méthode de l’application serveur `Startup.Configure` , remplacez `endpoints.MapFallbackToFile("index.html")` par `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="25272-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="25272-687">Dans l’application serveur, créez un dossier *pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="25272-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="25272-688">Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="25272-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="25272-689">Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="25272-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="25272-690">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="25272-690">Update the file's contents:</span></span>

* <span data-ttu-id="25272-691">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="25272-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="25272-692">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="25272-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="25272-693">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="25272-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="25272-694">Lors de l’authentification et de l’autorisation d’une Blazor application Webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25272-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="25272-695">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="25272-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="25272-696">Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="25272-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="25272-697">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="25272-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="25272-698">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="25272-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="25272-699">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="25272-699">In this scenario:</span></span>

* <span data-ttu-id="25272-700">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="25272-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="25272-701">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="25272-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="25272-702">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="25272-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="25272-703">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="25272-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="25272-704">Configurez Identity avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="25272-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="25272-705">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="25272-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="25272-706">Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="25272-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="25272-707">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="25272-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="25272-708">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="25272-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="25272-709">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="25272-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="25272-710">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.</span><span class="sxs-lookup"><span data-stu-id="25272-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="25272-711">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="25272-711">We don't recommend this approach.</span></span> <span data-ttu-id="25272-712">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="25272-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="25272-713">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="25272-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="25272-714">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="25272-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="25272-715">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="25272-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="25272-716">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="25272-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="25272-717">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="25272-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="25272-718">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="25272-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="25272-719">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="25272-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="25272-720">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="25272-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="25272-721">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="25272-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="25272-722">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="25272-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="25272-723">Utiliser des points de terminaison OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="25272-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="25272-724">La bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="25272-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="25272-725">Pour utiliser un point de terminaison v 2.0, configurez l’option de support JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="25272-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="25272-726">Dans l’exemple suivant, AAD est configuré pour v 2.0 en ajoutant un `v2.0` segment à la `Authority` propriété :</span><span class="sxs-lookup"><span data-stu-id="25272-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="25272-727">Le paramètre peut également être défini dans le fichier de paramètres de l’application (*appSettings. JSON*) :</span><span class="sxs-lookup"><span data-stu-id="25272-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="25272-728">Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la `Authority` propriété directement.</span><span class="sxs-lookup"><span data-stu-id="25272-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="25272-729">Définissez la propriété dans `JwtBearerOptions` ou dans le fichier de paramètres de l’application avec la `Authority` clé.</span><span class="sxs-lookup"><span data-stu-id="25272-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="25272-730">La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0.</span><span class="sxs-lookup"><span data-stu-id="25272-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="25272-731">Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="25272-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
