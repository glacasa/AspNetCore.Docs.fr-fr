---
title: Sécuriser une Blazor application hébergée par l' Identity ASP.net Core webassembly avec le serveur
author: guardrex
description: Pour créer une application Blazor hébergée avec l’authentification à partir de Visual Studio qui utilise un backend [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: bf2298618e922df412e0742177afd390c4116388
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768119"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Sécuriser une Blazor application hébergée par l' Identity ASP.net Core webassembly avec le serveur

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer une nouvelle Blazor application hébergée dans Visual Studio qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API :

1. Utilisez Visual Studio pour créer une ** Blazor application webassembly** . Pour plus d’informations, consultez <xref:blazor/get-started>.
1. Dans la boîte de dialogue **créer une nouvelle Blazor application** , sélectionnez **modifier** dans la section **authentification** .
1. Sélectionnez **des comptes d’utilisateur individuels** , puis cliquez sur **OK**.
1. Activez la case à cocher **ASP.net Core hébergé** dans la section **avancé** .
1. Cliquez sur le bouton **Créer**.

Pour créer l’application dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,). Le nom du dossier devient également une partie du nom du projet.

## <a name="server-app-configuration"></a>Configuration de l’application serveur

Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.

### <a name="startup-class"></a>Classe de démarrage

La `Startup` classe comporte les ajouts suivants :

* Dans `Startup.ConfigureServices` :

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer avec une méthode <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> d’assistance supplémentaire qui définit des conventions de ASP.net Core par défaut en plus de IdentityServer :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentification avec une méthode <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dans `Startup.Configure` :

  * Intergiciel d’authentification responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :

    ```csharp
    app.UseAuthentication();
    ```

  * L’intergiciel IdentityServer qui expose les points de terminaison Open ID Connect (OIDC) :

    ```csharp
    app.UseIdentityServer();
    ```

  * Intergiciel d’authentification et d’autorisation :

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [IdentityServer](https://identityserver.io/) pour les scénarios de ASP.net core. IdentityServer est une infrastructure puissante et extensible pour gérer les problèmes de sécurité des applications. IdentityServer expose une complexité inutile pour les scénarios les plus courants. Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ. Une fois vos besoins d’authentification modifiés, toute la puissance de IdentityServer est toujours disponible pour personnaliser l’authentification en fonction des exigences d’une application.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut. La stratégie est configurée pour Identity autoriser à gérer toutes les demandes routées vers n’importe quel Identity sous- `/Identity`chemin dans l’espace d’URL. Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes. En outre, cette méthode :

* Inscrit une `{APPLICATION NAME}API` ressource API avec IdentityServer avec une étendue par défaut `{APPLICATION NAME}API`.
* Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par IdentityServer pour l’application.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Dans `WeatherForecastController` (*Controllers/WeatherForecastController. cs*), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe. L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource. La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> qui est défini par au schéma de stratégie mentionné précédemment. La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` Dans (*Data/ApplicationDbContext. cs*), le <xref:Microsoft.EntityFrameworkCore.DbContext> même est utilisé dans Identity , avec l’exception qu’il étend <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> pour inclure le schéma pour IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Pour obtenir le contrôle total du schéma de base de données, héritez de Identity <xref:Microsoft.EntityFrameworkCore.DbContext> l’une des classes disponibles et configurez le contexte `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` pour inclure `OnModelCreating` le Identity schéma en appelant dans la méthode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Dans `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), le point de terminaison client est approvisionné pour servir les paramètres OIDC.

### <a name="app-settings-files"></a>Fichiers de paramètres d’application

Dans le fichier de paramètres d’application (*appSettings. JSON*) à la racine du `IdentityServer` projet, la section décrit la liste des clients configurés. Dans l’exemple suivant, il existe un seul client. Le nom du client correspond au nom de l’application et est mappé par Convention au paramètre `ClientId` OAuth. Le profil indique le type d’application en cours de configuration. Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Configuration de l’application cliente

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes d’utilisateur`Individual`individuels (), l’application reçoit automatiquement une référence de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package pour le package dans le fichier projet de l’application. Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.

### <a name="api-authorization-support"></a>Prise en charge des autorisations d’API

La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la méthode d’extension fournie dans le package. Cette méthode configure tous les services nécessaires à l’application pour interagir avec le système d’autorisation existant.

```csharp
builder.Services.AddApiAuthorization();
```

Par défaut, il charge la configuration de l’application par Convention à `_configuration/{client-id}`partir de. Par Convention, l’ID client est défini sur le nom de l’assembly de l’application. Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.

### <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans `MainLayout` le composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :

* Pour les utilisateurs authentifiés :
  * Affiche le nom de l’utilisateur actuel.
  * Propose un lien vers la page de profil utilisateur dans IdentityASP.net core.
  * Offre un bouton permettant de se déconnecter de l’application.
* Pour les utilisateurs anonymes :
  * Offre la possibilité de s’inscrire.
  * Offre la possibilité de se connecter.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application à partir du projet serveur. Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/additional-scenarios>
