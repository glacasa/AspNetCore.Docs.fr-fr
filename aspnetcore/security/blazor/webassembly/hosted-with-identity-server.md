---
title: Sécurisez une Blazor application WebAssembly ASP.NET Core avec Identity Server
author: guardrex
description: Créer une Blazor nouvelle application hébergée avec authentification au sein de Visual Studio qui utilise un backend [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501284"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Sécurisez une Blazor application WebAssembly ASP.NET Core avec Identity Server

Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Pour créer Blazor une nouvelle application hébergée dans Visual Studio qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels API :

1. Utilisez Visual Studio pour créer une nouvelle ** Blazor application WebAssembly.** Pour plus d’informations, consultez <xref:blazor/get-started>.
1. Dans le **dialogue Blazor Créer une nouvelle application,** sélectionnez **Changement** dans la section **Authentification.**
1. Sélectionnez **des comptes d’utilisateurs individuels** suivis par **OK**.
1. Sélectionnez la **case à cocher hébergée ASP.NET Core** dans la section **Advanced.**
1. Cliquez sur le bouton **Créer**.

Pour créer l’application dans une coque de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`). Le nom du dossier fait également partie du nom du projet.

## <a name="server-app-configuration"></a>Configuration de l’application serveur

Les sections suivantes décrivent les ajouts au projet lorsque le support d’authentification est inclus.

### <a name="startup-class"></a>Classe de démarrage

La `Startup` classe a les ajouts suivants:

* Dans `Startup.ConfigureServices` :

  * Identité avec l’interface utilisateur par défaut :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer avec <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> une méthode d’aide supplémentaire qui met en place quelques conventions par défaut ASP.NET Core en plus de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentification <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> avec une méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dans `Startup.Configure` :

  * Le middleware d’authentification qui est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur sur le contexte de la demande:

    ```csharp
    app.UseAuthentication();
    ```

  * Le middleware IdentityServer qui expose les points de terminaison Open ID Connect (OIDC) :

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’aide configure [IdentityServer](https://identityserver.io/) pour ASP.NET scénarios Core. IdentityServer est un cadre puissant et extensible pour le traitement des problèmes de sécurité des applications. IdentityServer expose la complexité inutile pour les scénarios les plus courants. Par conséquent, un ensemble de conventions et d’options de configuration est prévu que nous considérons un bon point de départ. Une fois que vos besoins d’authentification changent, la pleine puissance d’IdentityServer est toujours disponible pour personnaliser l’authentification en fonction des exigences d’une application.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’aide configure un schéma de stratégie pour l’application en tant que gestionnaire d’authentification par défaut. La stratégie est configurée pour permettre à Identity de traiter toutes `/Identity`les demandes acheminées vers n’importe quel sous-site dans l’espace URL d’identité . Les <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> poignées toutes les autres demandes. En outre, cette méthode:

* Enregistre une `{APPLICATION NAME}API` ressource API auprès d’IdentityServer avec une portée par défaut de `{APPLICATION NAME}API`.
* Configure le JWT Bearer Token Middleware pour valider les jetons émis par IdentityServer pour l’application.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Dans `WeatherForecastController` le *(Controllers/WeatherForecastController.cs*), l’attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) est appliqué à la classe. L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource. La politique d’autorisation par défaut est configurée pour <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> utiliser le système d’authentification par défaut, qui est configuré par le régime de politique qui a été mentionné précédemment. La méthode d’aide configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> comme le gestionnaire par défaut pour les demandes à l’application.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Dans `ApplicationDbContext` le (*Data/ApplicationDbContext.cs*), la même chose <xref:Microsoft.EntityFrameworkCore.DbContext> est <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> utilisée dans Identity à l’exception qu’il s’étend pour inclure le schéma pour IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Pour prendre le contrôle total du schéma de base <xref:Microsoft.EntityFrameworkCore.DbContext> de données, héritez de l’une des classes d’identité disponibles et configurez le contexte pour inclure le schéma d’identité en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la `OnModelCreating` méthode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Dans `OidcConfigurationController` le *(Controllers/OidcConfigurationController.cs*), le critère d’évaluation du client est prévu pour servir les paramètres de l’OIDC.

### <a name="app-settings-files"></a>Fichiers de paramètres d’applications

Dans le fichier des paramètres de l’application *(appsettings.json*) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés. Dans l’exemple suivant, il y a un seul client. Le nom du client correspond au nom de l’application `ClientId` et est cartographié par convention au paramètre OAuth. Le profil indique que le type d’application est configuré. Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Dans le fichier de paramètres de l’application Environnement Développement *(appsettings. Development.json*) à la `IdentityServer` racine du projet, la section décrit la clé utilisée pour signer des jetons. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configuration de l’application client

### <a name="authentication-package"></a>Forfait d’authentification

Lorsqu’une application est créée pour`Individual`utiliser des comptes utilisateur individuels `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ( ), l’application reçoit automatiquement une référence de paquet pour le paquet dans le fichier de projet de l’application. Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.

Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.

### <a name="api-authorization-support"></a>Soutien à l’autorisation de l’API

Le support pour l’authentification des utilisateurs est branché `Microsoft.AspNetCore.Components.WebAssembly.Authentication` sur le conteneur de service par la méthode d’extension fournie à l’intérieur du paquet. Cette méthode met en place tous les services nécessaires pour que l’application interagisse avec le système d’autorisation existant.

```csharp
builder.Services.AddApiAuthorization();
```

Par défaut, il charge la configuration de `_configuration/{client-id}`l’application par convention à partir de . Par convention, l’ID du client est réglé sur le nom d’assemblage de l’application. Cette URL peut être modifiée pour indiquer un point de terminaison distinct en appelant la surcharge avec des options.

### <a name="imports-file"></a>Fichier d’importations

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Composant de l’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

Le `LoginDisplay` composant *(Shared/LoginDisplay.razor*) est `MainLayout` rendu dans le composant (*Shared/MainLayout.razor*) et gère les comportements suivants :

* Pour les utilisateurs authentifiés :
  * Affiche le nom d’utilisateur actuel.
  * Offre un lien vers la page de profil utilisateur dans ASP.NET’identité de base.
  * Offre un bouton pour vous déconnecter de l’application.
* Pour les utilisateurs anonymes :
  * Offre la possibilité de s’inscrire.
  * Offre la possibilité de vous connecter.

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

Exécutez l’application à partir du projet Server. Lorsque vous utilisez Visual Studio, sélectionnez le projet Server dans **Solution Explorer** et sélectionnez le bouton **Run** dans la barre d’outils ou démarrez l’application à partir du menu **Debug.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Demander des jetons d’accès supplémentaires](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
