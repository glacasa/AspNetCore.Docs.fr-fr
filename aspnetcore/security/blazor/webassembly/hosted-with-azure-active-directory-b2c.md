---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 6f049906da4a1aa87f293f5ad1af19dad44f477a
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150033"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Cet article explique comment créer une Blazor application autonome webassembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Inscrire des applications dans AAD B2C et créer une solution

### <a name="create-a-tenant"></a>Créer un client

Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C et enregistrer les informations suivantes :

* AAD B2C instance (par exemple, `https://contoso.b2clogin.com/`, qui comprend la barre oblique finale)
* AAD B2C domaine de locataire (par exemple `contoso.onmicrosoft.com`,)

### <a name="register-a-server-api-app"></a>Inscrire une application API serveur

Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour inscrire une application AAD pour l' *application API serveur* dans la zone**inscriptions d’applications** **Azure Active Directory** > de la portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor serveur AAD B2C**).
1. Pour les **types de comptes pris en charge**, sélectionnez les **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.** (multi-locataire) pour cette expérience.
1. L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.
1. Vérifiez que **Permissions** > **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.
1. Sélectionnez **Inscription**.

Dans **exposer une API**:

1. Sélectionnez **Ajouter une étendue**.
1. Sélectionnez **Enregistrer et continuer**.
1. Spécifiez un **nom d’étendue** (par `API.Access`exemple,).
1. Spécifiez un **nom d’affichage du consentement administrateur** ( `Access API`par exemple,).
1. Fournissez une **Description du consentement** de l’administrateur `Allows the app to access server app API endpoints.`(par exemple,).
1. Confirmez que l' **État** est défini sur **activé**.
1. Sélectionnez **Ajouter une étendue**.

Notez les informations suivantes :

* *Application API serveur* ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)
* URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, ou la valeur personnalisée que vous avez fournie)
* ID de répertoire (ID de locataire) (par `222222222-2222-2222-2222-222222222222`exemple,)
* *Application API serveur* URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`,, le portail Azure peut être la valeur par défaut de l’ID client)
* Étendue par défaut (par exemple `API.Access`,)

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *application cliente* dans la zone**inscriptions d’applications** **Azure Active Directory** > du portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, ** Blazor client AAD B2C**).
1. Pour les **types de comptes pris en charge**, sélectionnez les **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.** (multi-locataire) pour cette expérience.
1. Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.
1. Vérifiez que **Permissions** > **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.
1. Sélectionnez **Inscription**.

Dans le**site Web****configurations** > de la plateforme **d’authentification** > :

1. Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.
1. Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Dans **autorisations d’API**:

1. Vérifiez que l’application a **Microsoft Graph** > autorisation**User. Read** .
1. Sélectionnez **Ajouter une autorisation** suivi de **mes API**.
1. Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, ** Blazor serveur AAD B2C**).
1. Ouvrez la liste des **API** .
1. Activez l’accès à l’API (par exemple `API.Access`,).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez le bouton **Grant admin content for {locataire Name}** . Sélectionnez **Oui** pour confirmer.

Dans la **page** > d'**Azure ad B2C** > des**flux utilisateur**:

[Créer un flux d’utilisateur d’inscription et de connexion](/azure/active-directory-b2c/tutorial-create-user-flows)

Au minimum, sélectionnez l’attribut utilisateur des **revendications** > d’application**nom complet** pour remplir `context.User.Identity.Name` le dans `LoginDisplay` le composant (*Shared/LoginDisplay. Razor*).

Notez les informations suivantes :

* Enregistrez l’ID de l’application *cliente* (ID client) (par `33333333-3333-3333-3333-333333333333`exemple,).
* Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple `B2C_1_signupsignin`,).

### <a name="create-the-app"></a>Créer l’application

Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

> [!NOTE]
> Passez l’URI ID d’application à `app-id-uri` l’option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .

## <a name="server-app-configuration"></a>Configuration de l’application serveur

*Cette section se rapporte à l’application **serveur** de la solution.*

### <a name="authentication-package"></a>Package d’authentification

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`Web est assurée par le :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La `AddAzureADB2CBearer` méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`et `UseAuthorization` Assurez-vous que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Par défaut, le `User.Identity.Name` n’est pas rempli.

Pour configurer l’application afin qu’elle reçoive la `name` valeur du type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices`:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Paramètres de l’application

Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
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

Quand une application est créée pour utiliser un compte B2C individuel (`IndividualB2C`), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise `HttpClient` en charge des instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package. Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.

La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
