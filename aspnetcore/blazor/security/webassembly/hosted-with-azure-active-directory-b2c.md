---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core hébergée avec Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: adc45293a6dfd324c12482d2dfffdeaa25eee4a3
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712439"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Inscrire des applications dans AAD B2C et créer une solution

### <a name="create-a-tenant"></a>Créer un client

Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C.

Enregistrez l’instance de AAD B2C (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale). L’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** du portail Azure.

### <a name="register-a-server-api-app"></a>Inscrire une application API serveur

Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :

1. Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor Server AAD B2C**).
1. Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**
1. L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.
1. Vérifiez que **Permissions**  >  **les autorisations accordent le consentement de l’administrateur à OpenID et que les autorisations offline_access** sont activées.
1. Sélectionnez **Enregistrer**.

Notez les informations suivantes :

* *Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.

Dans **exposer une API**:

1. sélectionner **Ajouter une étendue**.
1. Sélectionnez **Enregistrer et continuer**.
1. Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).
1. Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).
1. Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).
1. Confirmez que l' **État** est défini sur **activé**.
1. Sélectionnez **Ajouter une étendue**.

Notez les informations suivantes :

* URI ID d’application (par exemple,, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous avez fournie)
* Nom de l’étendue (par exemple, `API.Access` )

L’URI ID d’application peut nécessiter une configuration spéciale dans l’application cliente, qui est décrite dans la section [étendues de jetons d’accès](#access-token-scopes) plus loin dans cette rubrique.

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *application cliente* , puis procédez comme suit :

1. Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor client AAD B2C**).
1. Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**
1. Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application serveur dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Vérifiez que **Permissions**  >  **les autorisations accordent le consentement de l’administrateur à OpenID et que les autorisations offline_access** sont activées.
1. Sélectionnez **Enregistrer**.

Enregistrez l’ID de l’application (client) (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Dans **autorisations d’API**:

1. Sélectionnez **Ajouter une autorisation** suivi de **mes API**.
1. Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, ** Blazor Server AAD B2C**).
1. Ouvrez la liste des **API** .
1. Activez l’accès à l’API (par exemple, `API.Access` ).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** . Sélectionnez **Oui** pour confirmer l’opération.

Dans la **page**d'  >  **Azure ad B2C**des  >  **flux utilisateur**:

[Créer un flux d’utilisateur d’inscription et de connexion](/azure/active-directory-b2c/tutorial-create-user-flows)

Au minimum, sélectionnez l' **Application claims**  >  attribut utilisateur**nom complet** des revendications d’application pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).

Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Créer l’application

Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Espace réservé                   | Nom du portail Azure                                     | Exemple                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instance                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | ID de l’application (client) pour l' *application cliente*          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | Nom de l’étendue                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | ID de l’application (client) pour l’application *API serveur*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | URI d’ID d’application ([Voir la remarque](#access-token-scopes)) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Workflow d’inscription/de connexion                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Domaine principal/serveur de publication/locataire                       | `contoso.onmicrosoft.com`              |

L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.

> [!NOTE]
> Passez l’URI ID d’application à l' `app-id-uri` option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .
>
> En outre, l’étendue configurée par le Blazor modèle hébergé peut avoir l’hôte URI ID d’application répété. Vérifiez que l’étendue configurée pour la `DefaultAccessTokenScopes` collection est correcte dans `Program.Main` ( `Program.cs` ) de l' *application cliente*.

> [!NOTE]
> Dans le portail Azure, l’URI de redirection Web des configurations de plateforme d’authentification *de l’application cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.
>
> Si l' *application cliente* est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .
>
> Si le port n’a pas été configuré précédemment avec le port connu *de l’application cliente* , revenez à l’inscription de l' *application cliente* dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.

## <a name="server-app-configuration"></a>Configuration de l’application serveur

*Cette section se rapporte à l’application de la solution **`Server`** .*

### <a name="authentication-package"></a>Package d’authentification

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) Package :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Utilisateur. Identity . Nomme

Par défaut, le `User.Identity.Name` n’est pas rempli.

Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Paramètres de l’application

Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Exemple :

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Contrôleur WeatherForecast

Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur. Il est **important** de comprendre que :

* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.
* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.

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

*Cette section se rapporte à l’application de la solution **`Client`** .*

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).

La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package. Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.

La configuration est fournie par le `wwwroot/appsettings.json` fichier :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Exemple :

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :

* [Demander des jetons d’accès supplémentaires](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Attacher des jetons aux demandes sortantes](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Mode de connexion

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

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

Exécutez l’application à partir du projet serveur. Lorsque vous utilisez Visual Studio, vous pouvez :

* Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .
* Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutoriel : Créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
