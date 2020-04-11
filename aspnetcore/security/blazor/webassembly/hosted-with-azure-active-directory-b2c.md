---
title: Sécurisez une Blazor application WebAssembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123467"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Sécurisez une Blazor application WebAssembly ASP.NET Core avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Cet article décrit comment Blazor créer une application autonome WebAssembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Enregistrez les applications dans AAD B2C et créez une solution

### <a name="create-a-tenant"></a>Créer un client

Suivez les conseils dans [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C et enregistrer les informations suivantes:

* AAD B2C instance (par exemple, , `https://contoso.b2clogin.com/`qui comprend la barre oblique de fuite)
* Domaine AAD B2C Tenant `contoso.onmicrosoft.com`(par exemple, )

### <a name="register-a-server-api-app"></a>Enregistrez une application API serveur

Suivez les conseils dans [Tutorial: Enregistrez une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour enregistrer une application AAD pour *l’application Server API* dans la zone **Azure Active Directory** > **App d’enregistrement** du portail Azure :

1. Sélectionnez **Nouvelle inscription**.
1. Fournir un **nom** pour l’application (par exemple, ** Blazor Serveur AAD B2C**).
1. Pour **les types de compte pris en**charge, sélectionnez des comptes dans **n’importe quel répertoire organisationnel ou tout fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.** (multi-locataire) pour cette expérience.
1. *L’application Server API* ne nécessite pas **d’URI Redirect dans** ce scénario, alors laissez la baisse vers le **Web** et n’entrez pas dans une URI rediriger.
1. Confirmez que **Les autorisations** > **Grant admin concent à openid et offline_access autorisations sont activées.**
1. Sélectionnez **Inscription**.

Dans **Expose une API**:

1. sélectionner **Ajouter une étendue**.
1. Sélectionnez **Enregistrer et continuer**.
1. Fournir un **nom scope** `API.Access`(par exemple, ).
1. Fournir un **nom d’affichage de consentement d’administration** (par exemple, `Access API`).
1. Fournir une description du consentement `Allows the app to access server app API endpoints.`de **l’administration** (par exemple, ).
1. Confirmez que **l’Etat** est prêt à **Enabled**.
1. Sélectionnez **Ajouter une étendue**.

Enregistrez les informations suivantes :

* *Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)
* App ID URI (par exemple, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, ou la valeur personnalisée que vous avez fournie)
* Id d’annuaire (Id locataire) `222222222-2222-2222-2222-222222222222`(par exemple, )
* *Application API serveur* App ID URI (par exemple, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`le portail Azure peut par défaut la valeur de l’ID client)
* Portée par défaut `API.Access`(par exemple, )

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les conseils dans [Tutorial: Enregistrez une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour enregistrer une application AAD pour *l’application Client* dans la zone **Azure Active Directory** > **App d’enregistrement** du portail Azure:

1. Sélectionnez **Nouvelle inscription**.
1. Fournir un **nom** pour l’application (par exemple, ** Blazor Client AAD B2C**).
1. Pour **les types de compte pris en**charge, sélectionnez des comptes dans **n’importe quel répertoire organisationnel ou tout fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.** (multi-locataire) pour cette expérience.
1. Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .
1. Confirmez que **Les autorisations** > **Grant admin concent à openid et offline_access autorisations sont activées.**
1. Sélectionnez **Inscription**.

Dans les > **configurations de plate-forme** **d’authentification** > **Web**:

1. Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.
1. Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.
1. Les autres défauts de paiement de l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

Dans **les autorisations de l’API**:

1. Confirmez que l’application dispose de l’autorisation **Microsoft Graph** > **User.Read.**
1. Sélectionnez **Ajouter une permission** suivie par Mes **API**.
1. Sélectionnez *l’application Server API* à partir de la colonne **Nom** (par exemple, ** Blazor Serveur AAD B2C**).
1. Ouvrez la liste **API.**
1. Activer l’accès à l’API (par exemple, `API.Access`).
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez le **contenu de l’administrateur Grant pour le bouton 'TENANT NAME'.** Sélectionnez **Oui** pour confirmer.

Dans **Home** > **Azure AD B2C** > **Flux d’utilisateurs**:

[Créer un flux d’utilisateur d’inscription et de connexion](/azure/active-directory-b2c/tutorial-create-user-flows)

Au minimum, sélectionnez **l’attribut** > utilisateur**de l’utilisateur Display Name** des revendications d’application pour remplir le `context.User.Identity.Name` `LoginDisplay` composant *(Shared/LoginDisplay.razor*).

Enregistrez les informations suivantes :

* Enregistrez l’ID *d’application* de l’application Client (IDENTIFIANT client) (par exemple, `33333333-3333-3333-3333-333333333333`).
* Enregistrez le nom de flux d’utilisateur d’inscription et `B2C_1_signupsignin`d’inscription créé pour l’application (par exemple, ).

### <a name="create-the-app"></a>Créer l’application

Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

> [!NOTE]
> Passez l’App ID URI à l’option, `app-id-uri` mais notez qu’un changement de configuration peut être nécessaire dans l’application client, qui est décrite dans la section portée des [jetons d’accès.](#access-token-scopes)

## <a name="server-app-configuration"></a>Configuration de l’application serveur

*Cette section concerne l’application **Server** de la solution.*

### <a name="authentication-package"></a>Forfait d’authentification

Le support pour l’authentification et l’autorisation des appels `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`à ASP.NET API Web de base est fourni par le :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Support de service d’authentification

La `AddAuthentication` méthode configure les services d’authentification dans l’application et configure le gestionnaire JWT Bearer comme méthode d’authentification par défaut. La `AddAzureADB2CBearer` méthode définit les paramètres spécifiques du gestionnaire JWT Bearer nécessaires pour valider les jetons émis par l’Annuaire actif Azure B2C :

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`et `UseAuthorization` s’assurer que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande visant à accéder à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Par défaut, `User.Identity.Name` le n’est pas peuplé.

Pour configurer l’application pour `name` recevoir la valeur du type de réclamation, configurez le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the in `Startup.ConfigureServices`:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Paramètres de l’application

Le fichier *appsettings.json* contient les options pour configurer le gestionnaire de porteur JWT utilisé pour valider les jetons d’accès.

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Contrôleur WeatherForecast

Le contrôleur WeatherForecast *(Controllers/WeatherForecastController.cs*) expose une `[Authorize]` API protégée avec l’attribut appliqué au contrôleur. Il est **important** de comprendre que :

* L’attribut `[Authorize]` dans ce contrôleur API est la seule chose qui protège cette API contre l’accès non autorisé.
* L’attribut `[Authorize]` utilisé Blazor dans l’application WebAssembly ne sert qu’à indicer à l’application que l’utilisateur doit être autorisé à travailler correctement pour l’application.

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

## <a name="client-app-configuration"></a>Configuration de l’application client

*Cette section concerne l’application **Client** de la solution.*

### <a name="authentication-package"></a>Forfait d’authentification

Lorsqu’une application est créée pour utiliser`IndividualB2C`un compte B2C individuel ( ), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque Microsoft Authentication](/azure/active-directory/develop/msal-overview) ().`Microsoft.Authentication.WebAssembly.Msal` Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.

Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.

Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.

### <a name="authentication-service-support"></a>Support de service d’authentification

La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet. Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application. Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.

### <a name="access-token-scopes"></a>Portée symbolique d’accès

Les portées symboliques d’accès par défaut représentent la liste des portées symboliques d’accès qui sont :

* Inclus par défaut dans le signe en demande.
* Utilisé pour fournir un jeton d’accès immédiatement après l’authentification.

Toutes les portées doivent appartenir à la même application par les règles Azure Active Directory. Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API au besoin :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte. Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Fournir la portée URI sans le régime et l’hôte:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

### <a name="imports-file"></a>Fichier d’importations

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Composant de l’application

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

Exécutez l’application à partir du projet Server. Lorsque vous utilisez Visual Studio, sélectionnez le projet Server dans **Solution Explorer** et sélectionnez le bouton **Run** dans la barre d’outils ou démarrez l’application à partir du menu **Debug.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
