---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec Azure Active Directory
author: guardrex
description: ''
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 6ff95f0c5c925cbafef2b997a6cb23aeb15ff1aa
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153965"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec Azure Active Directory

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Cet article explique comment créer une [ Blazor application hébergée par webassembly](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.

## <a name="register-apps-in-aad-and-create-solution"></a>Inscrire des applications dans AAD et créer une solution

### <a name="create-a-tenant"></a>Créer un client

Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.

### <a name="register-a-server-api-app"></a>Inscrire une application API serveur

Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* dans la **Azure Active Directory**  >  zone de**inscriptions d’applications** Azure Active Directory de l’portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor Server AAD**).
1. Choisissez un **type de compte pris en charge**. Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.
1. Désactivez la case **Permissions**  >  à cocher autorisations**accorder à l’administrateur pour OpenID et offline_access** .
1. Sélectionnez **Inscription**.

Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph**  >  **User. Read** , car l’application n’a pas besoin d’accéder à la connexion ou au profil UER.

Dans **exposer une API**:

1. sélectionner **Ajouter une étendue**.
1. Sélectionnez **Enregistrer et continuer**.
1. Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).
1. Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).
1. Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).
1. Confirmez que l' **État** est défini sur **activé**.
1. Sélectionnez **Ajouter une étendue**.

Notez les informations suivantes :

* *Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111` )
* URI ID d’application (par exemple,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` ou la valeur personnalisée que vous avez fournie)
* ID de répertoire (ID de locataire) (par exemple, `222222222-2222-2222-2222-222222222222` )
* Domaine du locataire AAD (par exemple, `contoso.onmicrosoft.com` )
* Étendue par défaut (par exemple, `API.Access` )

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' **Azure Active Directory** *application cliente* dans la  >  zone de**inscriptions d’applications** Azure Active Directory de l’portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor client AAD**).
1. Choisissez un **type de compte pris en charge**. Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de redirection `https://localhost:5001/authentication/login-callback` .
1. Désactivez la case **Permissions**  >  à cocher autorisations**accorder à l’administrateur pour OpenID et offline_access** .
1. Sélectionnez **Inscription**.

Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :

1. Confirmez que l' **URI de redirection** de `https://localhost:5001/authentication/login-callback` est présent.
1. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Dans **autorisations d’API**:

1. Vérifiez que l’application a **Microsoft Graph**  >  autorisation**User. Read** .
1. Sélectionnez **Ajouter une autorisation** suivi de **mes API**.
1. Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, ** Blazor serveur AAD**).
1. Ouvrez la liste des **API** .
1. Activez l’accès à l’API (par exemple, `API.Access` ).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez le bouton **Grant admin content for {locataire Name}** . Cliquez sur **Oui** pour confirmer la suppression.

Enregistrez l’ID de l’application *cliente* (ID client) (par exemple, `33333333-3333-3333-3333-333333333333` ).

### <a name="create-the-app"></a>Créer l’application

Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ). Le nom du dossier devient également une partie du nom du projet.

> [!NOTE]
> Passez l’URI ID d’application à l' `app-id-uri` option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configuration de l’application serveur

*Cette section se rapporte à l’application **serveur** de la solution.*

### <a name="authentication-package"></a>Package d’authentification

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le `Microsoft.AspNetCore.Authentication.AzureAD.UI` :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La `AddAzureADBearer` méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`et `UseAuthorization` Assurez-vous que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Utilisateur. Identity . Nomme

Par défaut, l’API d’application serveur est renseignée `User.Identity.Name` avec la valeur du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` type de revendication (par exemple, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Paramètres d’application

Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>Contrôleur WeatherForecast

Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' `[Authorize]` attribut appliqué au contrôleur. Il est **important** de comprendre que :

* L' `[Authorize]` attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.
* L' `[Authorize]` attribut utilisé dans l' Blazor application webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuration de l’application cliente

*Cette section se rapporte à l’application **cliente** de la solution.*

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

Le `Microsoft.Authentication.WebAssembly.Msal` Package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge des `HttpClient` instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` Package. Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.

La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Étendues de jeton d’accès

Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :

* Inclus par défaut dans la demande de connexion.
* Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.

Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles. Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte. Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Fournissez l’URI d’étendue sans le schéma et l’hôte :
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Attacher des jetons aux demandes sortantes](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application à partir du projet serveur. Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
