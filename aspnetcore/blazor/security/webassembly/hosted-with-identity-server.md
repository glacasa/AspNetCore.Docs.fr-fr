---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec le Identity serveur
author: guardrex
description: Pour créer une Blazor application hébergée avec l’authentification à partir de Visual Studio qui utilise un backend [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: cce6b6b1ec144e362415fe34645aef567269c873
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402206"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="7b2f1-103">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec le Identity serveur</span><span class="sxs-lookup"><span data-stu-id="7b2f1-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7b2f1-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7b2f1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7b2f1-105">Cet article explique comment créer une Blazor application hébergée qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b2f1-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b2f1-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7b2f1-107">Dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-107">In Visual Studio:</span></span>

1. <span data-ttu-id="7b2f1-108">Créer une **Blazor WebAssembly** application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="7b2f1-109">Pour plus d’informations, consultez <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="7b2f1-110">Dans la boîte de dialogue **créer une nouvelle Blazor application** , sélectionnez **modifier** dans la section **authentification** .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="7b2f1-111">Sélectionnez **des comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="7b2f1-112">Activez la case à cocher **ASP.net Core hébergé** dans la section **avancé** .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="7b2f1-113">Cliquez sur le bouton **Créer**.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7b2f1-114">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="7b2f1-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="7b2f1-115">Pour créer l’application dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="7b2f1-116">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7b2f1-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7b2f1-117">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="7b2f1-118">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="7b2f1-118">Server app configuration</span></span>

<span data-ttu-id="7b2f1-119">Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7b2f1-120">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="7b2f1-120">Startup class</span></span>

<span data-ttu-id="7b2f1-121">La `Startup` classe a les ajouts suivants.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="7b2f1-122">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7b2f1-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7b2f1-124">IdentityServer avec une <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance supplémentaire qui définit les conventions de ASP.net Core par défaut par-dessus IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7b2f1-125">Authentification avec une <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7b2f1-126">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7b2f1-127">L’intergiciel IdentityServer expose les points de terminaison Open ID Connect (OIDC) :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="7b2f1-128">L’intergiciel d’authentification est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7b2f1-129">L’intergiciel d’autorisation active les fonctionnalités d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7b2f1-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7b2f1-130">AddApiAuthorization</span></span>

<span data-ttu-id="7b2f1-131">La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [IdentityServer](https://identityserver.io/) pour les scénarios de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="7b2f1-132">IdentityServer est une infrastructure puissante et extensible pour gérer les problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7b2f1-133">IdentityServer expose une complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7b2f1-134">Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7b2f1-135">Une fois vos besoins d’authentification modifiés, toute la puissance de IdentityServer est disponible pour personnaliser l’authentification en fonction des exigences d’une application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7b2f1-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7b2f1-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="7b2f1-137">La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7b2f1-138">La stratégie est configurée pour autoriser Identity à gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7b2f1-139">Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7b2f1-140">En outre, cette méthode :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-140">Additionally, this method:</span></span>

* <span data-ttu-id="7b2f1-141">Inscrit une `{APPLICATION NAME}API` ressource API avec IdentityServer avec une étendue par défaut `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7b2f1-142">Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par IdentityServer pour l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7b2f1-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7b2f1-143">WeatherForecastController</span></span>

<span data-ttu-id="7b2f1-144">Dans `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-144">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7b2f1-145">L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7b2f1-146">La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="7b2f1-147">La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7b2f1-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7b2f1-148">ApplicationDbContext</span></span>

<span data-ttu-id="7b2f1-149">Dans le `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> s’étend pour inclure le schéma pour IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-149">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7b2f1-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7b2f1-151">Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7b2f1-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7b2f1-152">OidcConfigurationController</span></span>

<span data-ttu-id="7b2f1-153">Dans le `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), le point de terminaison client est approvisionné pour servir les paramètres OIDC.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-153">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="7b2f1-154">Fichiers de paramètres d’application</span><span class="sxs-lookup"><span data-stu-id="7b2f1-154">App settings files</span></span>

<span data-ttu-id="7b2f1-155">Dans le fichier de paramètres d’application ( `appsettings.json` ) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-155">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7b2f1-156">Dans l’exemple suivant, il existe un seul client.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-156">In the following example, there's a single client.</span></span> <span data-ttu-id="7b2f1-157">Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7b2f1-158">Le profil indique le type d’application en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7b2f1-159">Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="7b2f1-160">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="7b2f1-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="7b2f1-161">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="7b2f1-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7b2f1-162">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7b2f1-162">Authentication package</span></span>

<span data-ttu-id="7b2f1-163">Quand une application est créée pour utiliser des comptes d’utilisateur individuels ( `Individual` ), l’application reçoit automatiquement une référence de package pour le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="7b2f1-164">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7b2f1-165">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="7b2f1-166">Prise en charge des autorisations d’API</span><span class="sxs-lookup"><span data-stu-id="7b2f1-166">API authorization support</span></span>

<span data-ttu-id="7b2f1-167">La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par la méthode d’extension fournie dans le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Package.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="7b2f1-168">Cette méthode Configure les services requis par l’application pour interagir avec le système d’autorisation existant.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7b2f1-169">Par défaut, la configuration de l’application est chargée par Convention à partir de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7b2f1-170">Par Convention, l’ID client est défini sur le nom de l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7b2f1-171">Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7b2f1-172">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="7b2f1-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7b2f1-173">Page d'index</span><span class="sxs-lookup"><span data-stu-id="7b2f1-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="7b2f1-174">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="7b2f1-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7b2f1-175">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7b2f1-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7b2f1-176">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7b2f1-176">LoginDisplay component</span></span>

<span data-ttu-id="7b2f1-177">Le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ) est rendu dans le `MainLayout` composant ( `Shared/MainLayout.razor` ) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-177">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="7b2f1-178">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-178">For authenticated users:</span></span>
  * <span data-ttu-id="7b2f1-179">Affiche le nom de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-179">Displays the current user name.</span></span>
  * <span data-ttu-id="7b2f1-180">Propose un lien vers la page de profil utilisateur dans ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7b2f1-181">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7b2f1-182">Pour les utilisateurs anonymes :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-182">For anonymous users:</span></span>
  * <span data-ttu-id="7b2f1-183">Offre la possibilité de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-183">Offers the option to register.</span></span>
  * <span data-ttu-id="7b2f1-184">Offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="7b2f1-185">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="7b2f1-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7b2f1-186">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="7b2f1-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7b2f1-187">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="7b2f1-187">Run the app</span></span>

<span data-ttu-id="7b2f1-188">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-188">Run the app from the Server project.</span></span> <span data-ttu-id="7b2f1-189">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7b2f1-190">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7b2f1-191">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="7b2f1-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="7b2f1-192">Nom et revendication de rôle avec autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="7b2f1-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="7b2f1-193">Fabrique d’utilisateur personnalisée</span><span class="sxs-lookup"><span data-stu-id="7b2f1-193">Custom user factory</span></span>

<span data-ttu-id="7b2f1-194">Dans l’application cliente, créez une fabrique d’utilisateurs personnalisée.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="7b2f1-195">Le serveur envoie plusieurs rôles sous forme de tableau JSON dans une seule `role` revendication.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="7b2f1-196">Un rôle unique est envoyé sous la forme d’une valeur de chaîne dans la revendication.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="7b2f1-197">La fabrique crée une `role` revendication individuelle pour chacun des rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="7b2f1-198">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="7b2f1-198">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="7b2f1-199">Dans l’application cliente, inscrivez la fabrique dans `Program.Main` ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-199">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="7b2f1-200">Dans l’application serveur, appelez <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sur le Identity générateur, qui ajoute des services liés aux rôles :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="7b2f1-201">Configurer le Identity serveur</span><span class="sxs-lookup"><span data-stu-id="7b2f1-201">Configure Identity Server</span></span>

<span data-ttu-id="7b2f1-202">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="7b2f1-203">Options d’autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="7b2f1-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="7b2f1-204">Service de profil</span><span class="sxs-lookup"><span data-stu-id="7b2f1-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="7b2f1-205">Options d’autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="7b2f1-205">API authorization options</span></span>

<span data-ttu-id="7b2f1-206">Dans l’application serveur :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-206">In the Server app:</span></span>

* <span data-ttu-id="7b2f1-207">Configurez Identity le serveur pour placer les `name` `role` revendications et dans le jeton d’ID et le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="7b2f1-208">Empêche le mappage par défaut des rôles dans le gestionnaire de jetons JWT.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="7b2f1-209">Service de profil</span><span class="sxs-lookup"><span data-stu-id="7b2f1-209">Profile Service</span></span>

<span data-ttu-id="7b2f1-210">Dans l’application serveur, créez une `ProfileService` implémentation.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="7b2f1-211">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="7b2f1-211">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="7b2f1-212">Dans l’application serveur, inscrivez le service de profil dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="7b2f1-213">Utiliser des mécanismes d’autorisation</span><span class="sxs-lookup"><span data-stu-id="7b2f1-213">Use authorization mechanisms</span></span>

<span data-ttu-id="7b2f1-214">Dans l’application cliente, les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="7b2f1-215">L’un des mécanismes d’autorisation des composants peut utiliser un rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="7b2f1-216">[ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="7b2f1-216">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="7b2f1-217">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="7b2f1-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="7b2f1-218">[Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="7b2f1-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="7b2f1-219">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="7b2f1-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="7b2f1-220">`User.Identity.Name`est renseigné dans l’application cliente avec le nom d’utilisateur de l’utilisateur, qui est généralement l’adresse de messagerie de connexion.</span><span class="sxs-lookup"><span data-stu-id="7b2f1-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7b2f1-221">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7b2f1-221">Additional resources</span></span>

* [<span data-ttu-id="7b2f1-222">Déploiement sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7b2f1-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="7b2f1-223">Importer un certificat à partir d’Key Vault (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="7b2f1-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7b2f1-224">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="7b2f1-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
