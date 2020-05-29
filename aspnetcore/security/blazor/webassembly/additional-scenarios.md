---
<span data-ttu-id="8065f-101">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-103">'Blazor'</span></span>
- <span data-ttu-id="8065f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-104">'Identity'</span></span>
- <span data-ttu-id="8065f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-106">'Razor'</span></span>
- <span data-ttu-id="8065f-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="8065f-108">Scénarios de sécurité supplémentaires pour l’ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="8065f-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="8065f-109">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="8065f-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="8065f-110">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="8065f-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="8065f-111">Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service peut être utilisé avec <xref:System.Net.Http.HttpClient> pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="8065f-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="8065f-112">Les jetons sont acquis à l’aide du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service existant.</span><span class="sxs-lookup"><span data-stu-id="8065f-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="8065f-113">Si un jeton ne peut pas être acquis, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> exception est levée.</span><span class="sxs-lookup"><span data-stu-id="8065f-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="8065f-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>dispose d’une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="8065f-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="8065f-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Peut être configuré avec les URL, les portées et l’URL de retour autorisées à l’aide de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="8065f-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="8065f-116">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configure un <xref:System.Net.Http.HttpClient> en `Program.Main` (*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="8065f-117">Pour plus de commodité, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="8065f-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="8065f-118">Les Blazor modèles Webassembly compatibles avec l’authentification utilisent désormais <xref:System.Net.Http.IHttpClientFactory> dans le projet d’API serveur pour configurer un <xref:System.Net.Http.HttpClient> avec les <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="8065f-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="8065f-119">Dans le cas où le client est créé avec <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> dans l’exemple précédent, <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.</span><span class="sxs-lookup"><span data-stu-id="8065f-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="8065f-120">Le configuré <xref:System.Net.Http.HttpClient> est ensuite utilisé pour effectuer des demandes autorisées à l’aide d’un modèle [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) simple.</span><span class="sxs-lookup"><span data-stu-id="8065f-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="8065f-121">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="8065f-122">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="8065f-122">Typed HttpClient</span></span>

<span data-ttu-id="8065f-123">Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="8065f-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="8065f-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="8065f-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="8065f-125">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="8065f-126">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="8065f-127">Configurer le gestionnaire HttpClient</span><span class="sxs-lookup"><span data-stu-id="8065f-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="8065f-128">Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="8065f-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="8065f-129">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="8065f-130">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="8065f-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="8065f-131">Si l' Blazor application Webassembly utilise généralement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="8065f-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="8065f-132">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="8065f-133">L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="8065f-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="8065f-134">Un composant crée le à <xref:System.Net.Http.HttpClient> partir du <xref:System.Net.Http.IHttpClientFactory> pour effectuer des demandes non authentifiées ou non autorisées :</span><span class="sxs-lookup"><span data-stu-id="8065f-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="8065f-135">Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="8065f-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="8065f-136">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8065f-136">Request additional access tokens</span></span>

<span data-ttu-id="8065f-137">Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="8065f-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="8065f-138">Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="8065f-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="8065f-139">Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="8065f-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="8065f-140">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="8065f-141">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.</span><span class="sxs-lookup"><span data-stu-id="8065f-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="8065f-142">Dans un Razor composant :</span><span class="sxs-lookup"><span data-stu-id="8065f-142">In a Razor component:</span></span>

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

<span data-ttu-id="8065f-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Cette</span><span class="sxs-lookup"><span data-stu-id="8065f-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="8065f-144">`true`avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="8065f-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="8065f-145">`false`Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="8065f-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="8065f-146">HttpClient et HttpRequestMessage avec les options de demande d’API Fetch</span><span class="sxs-lookup"><span data-stu-id="8065f-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="8065f-147">Lors de l’exécution sur webassembly dans une Blazor application Webassembly, [httpclient](xref:fundamentals/http-requests) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes.</span><span class="sxs-lookup"><span data-stu-id="8065f-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="8065f-148">Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="8065f-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="8065f-149">Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="8065f-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="8065f-150">L’implémentation de .NET webassembly de <xref:System.Net.Http.HttpClient> utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="8065f-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="8065f-151">L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8065f-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="8065f-152">Les options de requête HTTP FETCH peuvent être configurées avec <xref:System.Net.Http.HttpRequestMessage> les méthodes d’extension indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="8065f-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="8065f-153">Méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="8065f-153">Extension method</span></span> | <span data-ttu-id="8065f-154">Propriété de requête Fetch</span><span class="sxs-lookup"><span data-stu-id="8065f-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="8065f-155">informations d’identification</span><span class="sxs-lookup"><span data-stu-id="8065f-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="8065f-156">en</span><span class="sxs-lookup"><span data-stu-id="8065f-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="8065f-157">mode</span><span class="sxs-lookup"><span data-stu-id="8065f-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="8065f-158">garantis</span><span class="sxs-lookup"><span data-stu-id="8065f-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="8065f-159">Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="8065f-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="8065f-160">La réponse HTTP est généralement mise en mémoire tampon dans une Blazor application Webassembly pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="8065f-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="8065f-161">Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> méthode d’extension sur la demande.</span><span class="sxs-lookup"><span data-stu-id="8065f-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="8065f-162">Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="8065f-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="8065f-163">Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8065f-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="8065f-164">Lors de l’envoi d’informations d’identification (cookies/en-têtes d’autorisation) sur les demandes CORS, l' `Authorization` en-tête doit être autorisé par la stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="8065f-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="8065f-165">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="8065f-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="8065f-166">Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="8065f-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="8065f-167">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="8065f-167">Any method (verb).</span></span>
* <span data-ttu-id="8065f-168">`Content-Type`et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="8065f-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="8065f-169">Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="8065f-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="8065f-170">Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).</span><span class="sxs-lookup"><span data-stu-id="8065f-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="8065f-171">Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application (*composants/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="8065f-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="8065f-172">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="8065f-172">Handle token request errors</span></span>

<span data-ttu-id="8065f-173">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le Identity fournisseur (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="8065f-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="8065f-174">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="8065f-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="8065f-175">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="8065f-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="8065f-176">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="8065f-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="8065f-177">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="8065f-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="8065f-178">Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="8065f-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="8065f-179">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="8065f-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="8065f-180">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="8065f-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="8065f-181">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="8065f-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="8065f-182">Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.</span><span class="sxs-lookup"><span data-stu-id="8065f-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="8065f-183">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="8065f-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="8065f-184">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="8065f-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="8065f-185">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8065f-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="8065f-186">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="8065f-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="8065f-187">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="8065f-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="8065f-188">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="8065f-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="8065f-189">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="8065f-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="8065f-190">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="8065f-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="8065f-191">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="8065f-191">Store the current page state in session storage.</span></span> <span data-ttu-id="8065f-192">Pendant l' [événement de cycle de vie OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="8065f-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="8065f-193">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="8065f-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="8065f-194">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="8065f-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="8065f-195">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="8065f-195">The following example shows how to:</span></span>

* <span data-ttu-id="8065f-196">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="8065f-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="8065f-197">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="8065f-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="8065f-198">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="8065f-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="8065f-199">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="8065f-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="8065f-200">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="8065f-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="8065f-201">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="8065f-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="8065f-202">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="8065f-203">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="8065f-203">Customize app routes</span></span>

<span data-ttu-id="8065f-204">Par défaut, la bibliothèque [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="8065f-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="8065f-205">Routage</span><span class="sxs-lookup"><span data-stu-id="8065f-205">Route</span></span>                            | <span data-ttu-id="8065f-206">Objectif</span><span class="sxs-lookup"><span data-stu-id="8065f-206">Purpose</span></span> |
| ---
<span data-ttu-id="8065f-207">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-208">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-209">'Blazor'</span></span>
- <span data-ttu-id="8065f-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-210">'Identity'</span></span>
- <span data-ttu-id="8065f-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-212">'Razor'</span></span>
- <span data-ttu-id="8065f-213">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-214">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-215">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-216">'Blazor'</span></span>
- <span data-ttu-id="8065f-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-217">'Identity'</span></span>
- <span data-ttu-id="8065f-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-219">'Razor'</span></span>
- <span data-ttu-id="8065f-220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-221">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-222">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-223">'Blazor'</span></span>
- <span data-ttu-id="8065f-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-224">'Identity'</span></span>
- <span data-ttu-id="8065f-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-226">'Razor'</span></span>
- <span data-ttu-id="8065f-227">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-228">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-229">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-230">'Blazor'</span></span>
- <span data-ttu-id="8065f-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-231">'Identity'</span></span>
- <span data-ttu-id="8065f-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-233">'Razor'</span></span>
- <span data-ttu-id="8065f-234">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-235">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-236">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-237">'Blazor'</span></span>
- <span data-ttu-id="8065f-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-238">'Identity'</span></span>
- <span data-ttu-id="8065f-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-240">'Razor'</span></span>
- <span data-ttu-id="8065f-241">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-242">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-243">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-244">'Blazor'</span></span>
- <span data-ttu-id="8065f-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-245">'Identity'</span></span>
- <span data-ttu-id="8065f-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-247">'Razor'</span></span>
- <span data-ttu-id="8065f-248">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-249">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-250">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-251">'Blazor'</span></span>
- <span data-ttu-id="8065f-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-252">'Identity'</span></span>
- <span data-ttu-id="8065f-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-254">'Razor'</span></span>
- <span data-ttu-id="8065f-255">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-256">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-257">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-258">'Blazor'</span></span>
- <span data-ttu-id="8065f-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-259">'Identity'</span></span>
- <span data-ttu-id="8065f-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-261">'Razor'</span></span>
- <span data-ttu-id="8065f-262">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-263">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-264">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-265">'Blazor'</span></span>
- <span data-ttu-id="8065f-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-266">'Identity'</span></span>
- <span data-ttu-id="8065f-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-268">'Razor'</span></span>
- <span data-ttu-id="8065f-269">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-270">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-271">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-272">'Blazor'</span></span>
- <span data-ttu-id="8065f-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-273">'Identity'</span></span>
- <span data-ttu-id="8065f-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-275">'Razor'</span></span>
- <span data-ttu-id="8065f-276">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-277">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-278">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-279">'Blazor'</span></span>
- <span data-ttu-id="8065f-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-280">'Identity'</span></span>
- <span data-ttu-id="8065f-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-282">'Razor'</span></span>
- <span data-ttu-id="8065f-283">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-284">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-285">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-286">'Blazor'</span></span>
- <span data-ttu-id="8065f-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-287">'Identity'</span></span>
- <span data-ttu-id="8065f-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-289">'Razor'</span></span>
- <span data-ttu-id="8065f-290">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-291">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-292">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-293">'Blazor'</span></span>
- <span data-ttu-id="8065f-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-294">'Identity'</span></span>
- <span data-ttu-id="8065f-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-296">'Razor'</span></span>
- <span data-ttu-id="8065f-297">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-298">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-299">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-300">'Blazor'</span></span>
- <span data-ttu-id="8065f-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-301">'Identity'</span></span>
- <span data-ttu-id="8065f-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-303">'Razor'</span></span>
- <span data-ttu-id="8065f-304">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-304">'SignalR' uid:</span></span> 

<span data-ttu-id="8065f-305">---------------- | titre de la--- : 'ASP.NET Core Blazor les scénarios de sécurité supplémentaires du Webassembly’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-306">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-307">'Blazor'</span></span>
- <span data-ttu-id="8065f-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-308">'Identity'</span></span>
- <span data-ttu-id="8065f-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-310">'Razor'</span></span>
- <span data-ttu-id="8065f-311">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-311">'SignalR' uid:</span></span> 

<span data-ttu-id="8065f-312">---- | | `authentication/login`           | Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="8065f-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="8065f-313">| | `authentication/login-callback`  | Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="8065f-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="8065f-314">| | `authentication/login-failed`    | Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="8065f-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="8065f-315">| | `authentication/logout`          | Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="8065f-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="8065f-316">| | `authentication/logout-callback` | Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="8065f-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="8065f-317">| | `authentication/logout-failed`   | Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="8065f-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="8065f-318">| | `authentication/logged-out`      | Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="8065f-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="8065f-319">| | `authentication/profile`         | Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8065f-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="8065f-320">| | `authentication/register`        | Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8065f-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="8065f-321">Les itinéraires indiqués dans le tableau précédent sont configurables via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8065f-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8065f-322">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="8065f-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="8065f-323">Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .</span><span class="sxs-lookup"><span data-stu-id="8065f-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="8065f-324">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="8065f-325">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="8065f-326">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="8065f-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="8065f-327">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="8065f-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="8065f-328">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="8065f-328">Customize the authentication user interface</span></span>

<span data-ttu-id="8065f-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="8065f-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="8065f-330">Chaque État peut être personnalisé en passant un personnalisé <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="8065f-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="8065f-331">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comme suit.</span><span class="sxs-lookup"><span data-stu-id="8065f-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="8065f-332">`Authentication`composant (*pages/Authentication. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="8065f-333">Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="8065f-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="8065f-334">Routage</span><span class="sxs-lookup"><span data-stu-id="8065f-334">Route</span></span>                            | <span data-ttu-id="8065f-335">Fragment</span><span class="sxs-lookup"><span data-stu-id="8065f-335">Fragment</span></span>                |
| ---
<span data-ttu-id="8065f-336">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-337">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-338">'Blazor'</span></span>
- <span data-ttu-id="8065f-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-339">'Identity'</span></span>
- <span data-ttu-id="8065f-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-341">'Razor'</span></span>
- <span data-ttu-id="8065f-342">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-343">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-344">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-345">'Blazor'</span></span>
- <span data-ttu-id="8065f-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-346">'Identity'</span></span>
- <span data-ttu-id="8065f-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-348">'Razor'</span></span>
- <span data-ttu-id="8065f-349">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-350">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-351">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-352">'Blazor'</span></span>
- <span data-ttu-id="8065f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-353">'Identity'</span></span>
- <span data-ttu-id="8065f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-355">'Razor'</span></span>
- <span data-ttu-id="8065f-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-357">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-358">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-359">'Blazor'</span></span>
- <span data-ttu-id="8065f-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-360">'Identity'</span></span>
- <span data-ttu-id="8065f-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-362">'Razor'</span></span>
- <span data-ttu-id="8065f-363">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-364">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-365">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-366">'Blazor'</span></span>
- <span data-ttu-id="8065f-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-367">'Identity'</span></span>
- <span data-ttu-id="8065f-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-369">'Razor'</span></span>
- <span data-ttu-id="8065f-370">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-371">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-372">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-373">'Blazor'</span></span>
- <span data-ttu-id="8065f-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-374">'Identity'</span></span>
- <span data-ttu-id="8065f-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-376">'Razor'</span></span>
- <span data-ttu-id="8065f-377">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-378">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-379">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-380">'Blazor'</span></span>
- <span data-ttu-id="8065f-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-381">'Identity'</span></span>
- <span data-ttu-id="8065f-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-383">'Razor'</span></span>
- <span data-ttu-id="8065f-384">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-385">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-386">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-387">'Blazor'</span></span>
- <span data-ttu-id="8065f-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-388">'Identity'</span></span>
- <span data-ttu-id="8065f-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-390">'Razor'</span></span>
- <span data-ttu-id="8065f-391">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-392">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-393">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-394">'Blazor'</span></span>
- <span data-ttu-id="8065f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-395">'Identity'</span></span>
- <span data-ttu-id="8065f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-397">'Razor'</span></span>
- <span data-ttu-id="8065f-398">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-399">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-400">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-401">'Blazor'</span></span>
- <span data-ttu-id="8065f-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-402">'Identity'</span></span>
- <span data-ttu-id="8065f-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-404">'Razor'</span></span>
- <span data-ttu-id="8065f-405">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-406">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-407">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-408">'Blazor'</span></span>
- <span data-ttu-id="8065f-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-409">'Identity'</span></span>
- <span data-ttu-id="8065f-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-411">'Razor'</span></span>
- <span data-ttu-id="8065f-412">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-413">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-414">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-415">'Blazor'</span></span>
- <span data-ttu-id="8065f-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-416">'Identity'</span></span>
- <span data-ttu-id="8065f-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-418">'Razor'</span></span>
- <span data-ttu-id="8065f-419">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-420">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-421">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-422">'Blazor'</span></span>
- <span data-ttu-id="8065f-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-423">'Identity'</span></span>
- <span data-ttu-id="8065f-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-425">'Razor'</span></span>
- <span data-ttu-id="8065f-426">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-427">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-428">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-429">'Blazor'</span></span>
- <span data-ttu-id="8065f-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-430">'Identity'</span></span>
- <span data-ttu-id="8065f-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-432">'Razor'</span></span>
- <span data-ttu-id="8065f-433">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-433">'SignalR' uid:</span></span> 

<span data-ttu-id="8065f-434">---------------- | titre de la--- : 'ASP.NET Core Blazor les scénarios de sécurité supplémentaires du Webassembly’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-435">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-436">'Blazor'</span></span>
- <span data-ttu-id="8065f-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-437">'Identity'</span></span>
- <span data-ttu-id="8065f-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-439">'Razor'</span></span>
- <span data-ttu-id="8065f-440">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-441">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-442">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-443">'Blazor'</span></span>
- <span data-ttu-id="8065f-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-444">'Identity'</span></span>
- <span data-ttu-id="8065f-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-446">'Razor'</span></span>
- <span data-ttu-id="8065f-447">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-448">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-449">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-450">'Blazor'</span></span>
- <span data-ttu-id="8065f-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-451">'Identity'</span></span>
- <span data-ttu-id="8065f-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-453">'Razor'</span></span>
- <span data-ttu-id="8065f-454">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-455">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-456">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-457">'Blazor'</span></span>
- <span data-ttu-id="8065f-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-458">'Identity'</span></span>
- <span data-ttu-id="8065f-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-460">'Razor'</span></span>
- <span data-ttu-id="8065f-461">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-462">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-463">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-464">'Blazor'</span></span>
- <span data-ttu-id="8065f-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-465">'Identity'</span></span>
- <span data-ttu-id="8065f-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-467">'Razor'</span></span>
- <span data-ttu-id="8065f-468">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-469">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-470">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-471">'Blazor'</span></span>
- <span data-ttu-id="8065f-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-472">'Identity'</span></span>
- <span data-ttu-id="8065f-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-474">'Razor'</span></span>
- <span data-ttu-id="8065f-475">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-476">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-477">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-478">'Blazor'</span></span>
- <span data-ttu-id="8065f-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-479">'Identity'</span></span>
- <span data-ttu-id="8065f-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-481">'Razor'</span></span>
- <span data-ttu-id="8065f-482">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-483">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-484">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-485">'Blazor'</span></span>
- <span data-ttu-id="8065f-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-486">'Identity'</span></span>
- <span data-ttu-id="8065f-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-488">'Razor'</span></span>
- <span data-ttu-id="8065f-489">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8065f-490">titre : 'ASP.NET Core Blazor Webassembly autres scénarios de sécurité supplémentaires’auteur : Description : 'Découvrez comment configurer Blazor webassembly pour des scénarios de sécurité supplémentaires. '</span><span class="sxs-lookup"><span data-stu-id="8065f-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="8065f-491">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8065f-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8065f-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8065f-492">'Blazor'</span></span>
- <span data-ttu-id="8065f-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8065f-493">'Identity'</span></span>
- <span data-ttu-id="8065f-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8065f-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="8065f-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8065f-495">'Razor'</span></span>
- <span data-ttu-id="8065f-496">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="8065f-496">'SignalR' uid:</span></span> 

<span data-ttu-id="8065f-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="8065f-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="8065f-498">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="8065f-498">Customize the user</span></span>

<span data-ttu-id="8065f-499">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="8065f-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="8065f-500">Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8065f-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="8065f-501">Créez une classe qui étend la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe :</span><span class="sxs-lookup"><span data-stu-id="8065f-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="8065f-502">Créez une fabrique qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="8065f-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="8065f-503">Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="8065f-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="8065f-504">Les inscriptions suivantes sont valides :</span><span class="sxs-lookup"><span data-stu-id="8065f-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="8065f-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="8065f-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="8065f-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="8065f-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="8065f-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="8065f-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="8065f-508">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="8065f-508">Support prerendering with authentication</span></span>

<span data-ttu-id="8065f-509">Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application Webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="8065f-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="8065f-510">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="8065f-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="8065f-511">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="8065f-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="8065f-512">Dans la classe de l’application cliente `Program` (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :</span><span class="sxs-lookup"><span data-stu-id="8065f-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="8065f-513">Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="8065f-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="8065f-514">Dans la méthode de l’application serveur `Startup.Configure` , remplacez les [points de terminaison. MapFallbackToFile (« index. html »)](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) avec des [points de terminaison. MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="8065f-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="8065f-515">Dans l’application serveur, créez un dossier *pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="8065f-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="8065f-516">Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="8065f-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="8065f-517">Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="8065f-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="8065f-518">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="8065f-518">Update the file's contents:</span></span>

* <span data-ttu-id="8065f-519">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="8065f-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="8065f-520">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="8065f-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="8065f-521">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="8065f-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="8065f-522">Lors de l’authentification et de l’autorisation d’une Blazor application Webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8065f-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="8065f-523">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="8065f-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="8065f-524">Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="8065f-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="8065f-525">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="8065f-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="8065f-526">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="8065f-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="8065f-527">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="8065f-527">In this scenario:</span></span>

* <span data-ttu-id="8065f-528">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="8065f-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="8065f-529">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="8065f-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="8065f-530">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="8065f-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="8065f-531">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="8065f-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="8065f-532">Configurez Identity avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="8065f-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="8065f-533">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="8065f-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="8065f-534">Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="8065f-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="8065f-535">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="8065f-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="8065f-536">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="8065f-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="8065f-537">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="8065f-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="8065f-538">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.</span><span class="sxs-lookup"><span data-stu-id="8065f-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="8065f-539">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="8065f-539">We don't recommend this approach.</span></span> <span data-ttu-id="8065f-540">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="8065f-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="8065f-541">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="8065f-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="8065f-542">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="8065f-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="8065f-543">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="8065f-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="8065f-544">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="8065f-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="8065f-545">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="8065f-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="8065f-546">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="8065f-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="8065f-547">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="8065f-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="8065f-548">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="8065f-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="8065f-549">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="8065f-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="8065f-550">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="8065f-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="8065f-551">Utiliser des points de terminaison OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="8065f-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="8065f-552">La bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="8065f-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="8065f-553">Pour utiliser un point de terminaison v 2.0, configurez l’option de support JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8065f-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="8065f-554">Dans l’exemple suivant, AAD est configuré pour v 2.0 en ajoutant un `v2.0` segment à la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propriété :</span><span class="sxs-lookup"><span data-stu-id="8065f-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="8065f-555">Le paramètre peut également être défini dans le fichier de paramètres de l’application (*appSettings. JSON*) :</span><span class="sxs-lookup"><span data-stu-id="8065f-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="8065f-556">Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement.</span><span class="sxs-lookup"><span data-stu-id="8065f-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="8065f-557">Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou dans le fichier de paramètres de l’application (*appSettings. JSON*) avec la `Authority` clé.</span><span class="sxs-lookup"><span data-stu-id="8065f-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="8065f-558">La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0.</span><span class="sxs-lookup"><span data-stu-id="8065f-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="8065f-559">Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="8065f-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
