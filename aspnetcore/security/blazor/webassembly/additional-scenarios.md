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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Scénarios de sécurité supplémentaires ASP.NET Core éblouissant webassembly

Par [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Attacher des jetons aux demandes sortantes

Le `AuthorizationMessageHandler` service peut être utilisé avec `HttpClient` pour joindre des jetons d’accès aux demandes sortantes. Les jetons sont acquis à l’aide du `IAccessTokenProvider` service existant. Si un jeton ne peut pas être acquis, une `AccessTokenNotAvailableException` exception est levée. `AccessTokenNotAvailableException`dispose d’une `Redirect` méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton. `AuthorizationMessageHandler`Peut être configuré avec les URL, les portées et l’URL de retour autorisées à l’aide de la `ConfigureHandler` méthode.

Dans l’exemple suivant, `AuthorizationMessageHandler` configure un `HttpClient` en `Program.Main` (*Program.cs*) :

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

Pour plus de commodité, une `BaseAddressAuthorizationMessageHandler` est incluse et préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée. Les modèles de webassembly éblouissants compatibles avec l’authentification utilisent désormais <xref:System.Net.Http.IHttpClientFactory> dans le projet d’API serveur pour configurer un <xref:System.Net.Http.HttpClient> avec les `BaseAddressAuthorizationMessageHandler` éléments suivants :

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

Dans le cas où le client est créé avec `CreateClient` dans l’exemple précédent, <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de demandes au projet serveur.

Le configuré <xref:System.Net.Http.HttpClient> est ensuite utilisé pour effectuer des demandes autorisées à l’aide d’un `try-catch` modèle simple.

`FetchData`composant (*pages/fetchData. Razor*) :

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

## <a name="typed-httpclient"></a>HttpClient typé

Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*) :

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData`composant (*pages/fetchData. Razor*) :

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Configurer le gestionnaire HttpClient

Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.

`Program.Main`(*Program.cs*) :

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé

Si l’application de webassembly éblouissant utilise habituellement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :

`Program.Main`(*Program.cs*) :

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .

Un composant crée le à <xref:System.Net.Http.HttpClient> partir du <xref:System.Net.Http.IHttpClientFactory> pour effectuer des demandes non authentifiées ou non autorisées :

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
> Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.

## <a name="request-additional-access-tokens"></a>Demander des jetons d’accès supplémentaires

Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .

Dans l’exemple suivant, des Azure Active Directory supplémentaires (AAD) Microsoft Graph des étendues d’API sont requises par une application pour lire les données utilisateur et envoyer des messages électroniques. Après avoir ajouté les autorisations Microsoft Graph API dans le portail Azure AAD, les étendues supplémentaires sont configurées dans l’application cliente.

`Program.Main`(*Program.cs*) :

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

La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.

Dans un composant Razor :

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

`TryGetToken`Cette

* `true`avec le `token` à utiliser.
* `false`Si le jeton n’est pas récupéré.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient et HttpRequestMessage avec les options de demande d’API Fetch

Lors de l’exécution sur webassembly dans une application de webassembly éblouissante, [httpclient](xref:fundamentals/http-requests) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes. Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande. Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :

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

L’implémentation de .NET webassembly de `HttpClient` utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Les options de requête HTTP FETCH peuvent être configurées avec `HttpRequestMessage` les méthodes d’extension indiquées dans le tableau suivant.

| `HttpRequestMessage`méthode d’extension | Propriété de requête Fetch |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [informations d’identification](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [en](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [garantis](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique `SetBrowserRequestOption` .
 
La réponse HTTP est généralement mise en mémoire tampon dans une application de webassembly éblouissante pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse. Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la `SetBrowserResponseStreamingEnabled` méthode d’extension sur la demande.

Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la `SetBrowserRequestCredentials` méthode d’extension :

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Lors de l’envoi d’informations d’identification (cookies/en-têtes d’autorisation) sur les demandes CORS, l' `Authorization` en-tête doit être autorisé par la stratégie cors.

La stratégie suivante comprend la configuration pour :

* Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).
* Toute méthode (verbe).
* `Content-Type`et `Authorization` en-têtes. Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application (*composants/HTTPRequestTester. Razor*).

## <a name="handle-token-request-errors"></a>Gérer les erreurs de demande de jeton

Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide d’Open ID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le fournisseur d’identité (IP) sous la forme d’un cookie de session défini à la suite de l’utilisateur qui fournit ses informations d’identification.

Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons. Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés. Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.

Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP. Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide. En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.

Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons. Elles font partie de la nature des SPAs. Un SPA utilisant des cookies ne peut pas non plus appeler une API serveur si le cookie d’authentification est supprimé.

Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :

* Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.
* Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.

Lorsque l’application demande un jeton, deux résultats sont possibles :

* La demande a échoué et l’application a un jeton valide.
* La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.

En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection. Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :

* Stocke l’état actuel de la page dans le stockage de session. Pendant `OnInitializeAsync` , vérifiez si l’État peut être restauré avant de continuer.
* Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.
* Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.

L’exemple suivant montre comment :

* Conserver l’état avant la redirection vers la page de connexion.
* Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Enregistrer l’état de l’application avant une opération d’authentification

Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP. Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie. Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.

`Authentication`composant (*pages/Authentication. Razor*) :

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

## <a name="customize-app-routes"></a>Personnaliser les itinéraires de l’application

Par défaut, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.

| Routage                            | Objectif |
| -------------------------------- | ------- |
| `authentication/login`           | Déclenche une opération de connexion. |
| `authentication/login-callback`  | Gère le résultat de toute opération de connexion. |
| `authentication/login-failed`    | Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque. |
| `authentication/logout`          | Déclenche une opération de déconnexion. |
| `authentication/logout-callback` | Gère le résultat d’une opération de déconnexion. |
| `authentication/logout-failed`   | Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque. |
| `authentication/logged-out`      | Indique que l’utilisateur a réussi à se déconnecter. |
| `authentication/profile`         | Déclenche une opération de modification du profil utilisateur. |
| `authentication/register`        | Déclenche une opération pour inscrire un nouvel utilisateur. |

Les itinéraires indiqués dans le tableau précédent sont configurables via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` . Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.

Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .

`Authentication`composant (*pages/Authentication. Razor*) :

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*) :

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

Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le `RemoteAuthenticatorView` avec un paramètre d’action explicite :

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.

## <a name="customize-the-authentication-user-interface"></a>Personnaliser l’interface utilisateur de l’authentification

`RemoteAuthenticatorView`comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification. Chaque État peut être personnalisé en passant un personnalisé `RenderFragment` . Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le `RemoteAuthenticatorView` comme suit.

`Authentication`composant (*pages/Authentication. Razor*) :

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

Le `RemoteAuthenticatorView` a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.

| Routage                            | Fragment                |
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

## <a name="customize-the-user"></a>Personnaliser l’utilisateur

Les utilisateurs liés à l’application peuvent être personnalisés. Dans l’exemple suivant, tous les utilisateurs authentifiés reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.

Créez une classe qui étend la `RemoteUserAccount` classe :

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Créez une fabrique qui étend `AccountClaimsPrincipalFactory<TAccount>` :

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

Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation. Les inscriptions suivantes sont valides : 

* `AddOidcAuthentication`:

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

* `AddMsalAuthentication`:

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
  
* `AddApiAuthorization`:

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

## <a name="support-prerendering-with-authentication"></a>Prendre en charge le prérendu avec l’authentification

Après avoir utilisé les instructions de l’une des Blazor rubriques de l’application Webassembly hébergées, suivez les instructions ci-dessous pour créer une application qui :

* Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.
* N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.

Dans la classe de l’application cliente `Program` (*Program.cs*), les inscriptions de service courantes de facteur dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :

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

Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :

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

Dans la méthode de l’application serveur `Startup.Configure` , remplacez `endpoints.MapFallbackToFile("index.html")` par `endpoints.MapFallbackToPage("/_Host")` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Dans l’application serveur, créez un dossier *pages* s’il n’existe pas. Créez une page *_Host. cshtml* dans le dossier *pages* de l’application serveur. Collez le contenu du fichier *wwwroot/index.html* de l’application cliente dans le fichier *pages/_Host. cshtml* . Mettez à jour le contenu du fichier :

* Ajoutez `@page "_Host"` en haut du fichier.
* Remplacez la `<app>Loading...</app>` balise par le suivant :

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Options pour les applications hébergées et les fournisseurs de connexion tiers

Lors de l’authentification et de l’autorisation d’une Blazor application Webassembly hébergée auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur. Celui que vous choisissez dépend de votre scénario.

Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Authentifier les utilisateurs pour appeler uniquement des API tierces protégées

Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Dans ce scénario :

* Le serveur hébergeant l’application ne joue aucun rôle.
* Les API sur le serveur ne peuvent pas être protégées.
* L’application ne peut appeler que des API tierces protégées.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers

Configurez Identity avec un fournisseur de connexion tiers. Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.

Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification. À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers

Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur. À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.

Nous ne recommandons pas cette approche. Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public. Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel. Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.

* Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.
* De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces

Effectuez un appel d’API à partir du client vers l’API serveur. À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.

Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :

* Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.
* L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.
