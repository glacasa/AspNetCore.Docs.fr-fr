---
titre : « ASP.NET Core Blazor Server scénarios de sécurité supplémentaires » auteur : Description : « Découvrez comment configurer le Blazor serveur pour des scénarios de sécurité supplémentaires ».
monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>BlazorScénarios de sécurité supplémentaires du serveur ASP.net Core

Par [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passer des jetons à une Blazor application serveur

Les jetons disponibles en dehors des Razor composants d’une Blazor application serveur peuvent être passés aux composants avec l’approche décrite dans cette section. Pour obtenir un exemple de code, y compris un `Startup.ConfigureServices` exemple complet, consultez la page [transmission de jetons à une Blazor application côté serveur](https://github.com/javiercn/blazor-server-aad-sample).

Authentifiez l' Blazor application serveur comme vous le feriez avec une Razor application de pages ou MVC standard. Approvisionnez et enregistrez les jetons dans le cookie d’authentification. Par exemple :

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

Définissez un service de fournisseur de jetons **délimités** qui peut être utilisé dans l' Blazor application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans `Startup.ConfigureServices` , ajoutez des services pour :

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Utiliser des points de terminaison OIDC (Open ID Connect) v 2.0

La bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OIDC (Open ID Connect) v 1.0. Pour utiliser un point de terminaison v 2.0, configurez l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option dans le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Le paramètre peut également être défini dans le fichier de paramètres de l’application (*appSettings. JSON*) :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement. Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ou dans le fichier de paramètres de l’application avec la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> clé.

### <a name="code-changes"></a>Modifications du code

* La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0. Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) dans la documentation Azure.
* Étant donné que les ressources sont spécifiées dans les URI de portée pour les points de terminaison v 2.0, supprimez le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> paramètre de propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Vous pouvez trouver l’URI ID d’application à utiliser dans la description de l’inscription de l’application du fournisseur OIDC.
