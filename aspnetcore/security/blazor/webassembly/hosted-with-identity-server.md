---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec le serveur d’identité
author: guardrex
description: Pour créer une application Blazor hébergée avec l’authentification à partir de Visual Studio qui utilise un backend [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110943"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="7bd5b-103">Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec le serveur d’identité</span><span class="sxs-lookup"><span data-stu-id="7bd5b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7bd5b-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7bd5b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="7bd5b-105">Les instructions de cet article s’appliquent à ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="7bd5b-106">Cette rubrique sera mise à jour pour couvrir l’aperçu 5 le vendredi 24 avril.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="7bd5b-107">Pour créer une nouvelle Blazor application hébergée dans Visual Studio qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="7bd5b-108">Utilisez Visual Studio pour créer une \*\* Blazor application webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="7bd5b-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="7bd5b-109">Pour plus d’informations, consultez <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="7bd5b-110">Dans la boîte de dialogue **créer une nouvelle Blazor application** , sélectionnez **modifier** dans la section **authentification** .</span><span class="sxs-lookup"><span data-stu-id="7bd5b-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="7bd5b-111">Sélectionnez **des comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="7bd5b-112">Activez la case à cocher **ASP.net Core hébergé** dans la section **avancé** .</span><span class="sxs-lookup"><span data-stu-id="7bd5b-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="7bd5b-113">Cliquez sur le bouton **Créer**.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-113">Select the **Create** button.</span></span>

<span data-ttu-id="7bd5b-114">Pour créer l’application dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="7bd5b-115">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="7bd5b-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7bd5b-116">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7bd5b-117">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="7bd5b-117">Server app configuration</span></span>

<span data-ttu-id="7bd5b-118">Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7bd5b-119">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="7bd5b-119">Startup class</span></span>

<span data-ttu-id="7bd5b-120">La `Startup` classe comporte les ajouts suivants :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="7bd5b-121">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7bd5b-122">Identité :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7bd5b-123">IdentityServer avec une méthode <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> d’assistance supplémentaire qui définit des conventions de ASP.net Core par défaut en plus de IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7bd5b-124">Authentification avec une méthode <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7bd5b-125">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7bd5b-126">Intergiciel d’authentification responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7bd5b-127">L’intergiciel IdentityServer qui expose les points de terminaison Open ID Connect (OIDC) :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7bd5b-128">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7bd5b-128">AddApiAuthorization</span></span>

<span data-ttu-id="7bd5b-129">La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [IdentityServer](https://identityserver.io/) pour les scénarios de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="7bd5b-130">IdentityServer est une infrastructure puissante et extensible pour gérer les problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7bd5b-131">IdentityServer expose une complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7bd5b-132">Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7bd5b-133">Une fois vos besoins d’authentification modifiés, toute la puissance de IdentityServer est toujours disponible pour personnaliser l’authentification en fonction des exigences d’une application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7bd5b-134">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7bd5b-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="7bd5b-135">La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7bd5b-136">La stratégie est configurée pour autoriser l’identité à gérer toutes les demandes routées vers un sous-chemin dans `/Identity`l’espace d’URL d’identité.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7bd5b-137">Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7bd5b-138">En outre, cette méthode :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-138">Additionally, this method:</span></span>

* <span data-ttu-id="7bd5b-139">Inscrit une `{APPLICATION NAME}API` ressource API avec IdentityServer avec une étendue par défaut `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7bd5b-140">Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par IdentityServer pour l’application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7bd5b-141">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7bd5b-141">WeatherForecastController</span></span>

<span data-ttu-id="7bd5b-142">Dans `WeatherForecastController` (*Controllers/WeatherForecastController. cs*), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7bd5b-143">L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7bd5b-144">La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> qui est défini par au schéma de stratégie mentionné précédemment.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="7bd5b-145">La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7bd5b-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7bd5b-146">ApplicationDbContext</span></span>

<span data-ttu-id="7bd5b-147">Dans `ApplicationDbContext` (*Data/ApplicationDbContext. cs*), le même <xref:Microsoft.EntityFrameworkCore.DbContext> est utilisé dans l’identité, avec l’exception qu’il <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> étend pour inclure le schéma pour IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7bd5b-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7bd5b-149">Pour obtenir le contrôle total du schéma de base de données, héritez de l' <xref:Microsoft.EntityFrameworkCore.DbContext> une des classes d’identité disponibles et configurez le contexte `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` pour inclure `OnModelCreating` le schéma d’identité en appelant dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7bd5b-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7bd5b-150">OidcConfigurationController</span></span>

<span data-ttu-id="7bd5b-151">Dans `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), le point de terminaison client est approvisionné pour servir les paramètres OIDC.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="7bd5b-152">Fichiers de paramètres d’application</span><span class="sxs-lookup"><span data-stu-id="7bd5b-152">App settings files</span></span>

<span data-ttu-id="7bd5b-153">Dans le fichier de paramètres d’application (*appSettings. JSON*) à la racine du `IdentityServer` projet, la section décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7bd5b-154">Dans l’exemple suivant, il existe un seul client.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-154">In the following example, there's a single client.</span></span> <span data-ttu-id="7bd5b-155">Le nom du client correspond au nom de l’application et est mappé par Convention au paramètre `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7bd5b-156">Le profil indique le type d’application en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7bd5b-157">Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="7bd5b-158">Dans le fichier de paramètres d’application de l’environnement de développement (*appSettings. Development. JSON*) à la racine du projet, `IdentityServer` la section décrit la clé utilisée pour signer les jetons.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="7bd5b-159">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="7bd5b-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7bd5b-160">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7bd5b-160">Authentication package</span></span>

<span data-ttu-id="7bd5b-161">Quand une application est créée pour utiliser des comptes d’utilisateur`Individual`individuels (), l’application reçoit automatiquement une référence de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package pour le package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="7bd5b-162">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7bd5b-163">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="7bd5b-164">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="7bd5b-165">Prise en charge des autorisations d’API</span><span class="sxs-lookup"><span data-stu-id="7bd5b-165">API authorization support</span></span>

<span data-ttu-id="7bd5b-166">La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la méthode d’extension fournie dans le package.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="7bd5b-167">Cette méthode configure tous les services nécessaires à l’application pour interagir avec le système d’autorisation existant.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7bd5b-168">Par défaut, il charge la configuration de l’application par Convention à `_configuration/{client-id}`partir de.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7bd5b-169">Par Convention, l’ID client est défini sur le nom de l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7bd5b-170">Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7bd5b-171">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="7bd5b-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7bd5b-172">Page d'index</span><span class="sxs-lookup"><span data-stu-id="7bd5b-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="7bd5b-173">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="7bd5b-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7bd5b-174">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7bd5b-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7bd5b-175">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7bd5b-175">LoginDisplay component</span></span>

<span data-ttu-id="7bd5b-176">Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans `MainLayout` le composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="7bd5b-177">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-177">For authenticated users:</span></span>
  * <span data-ttu-id="7bd5b-178">Affiche le nom de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-178">Displays the current user name.</span></span>
  * <span data-ttu-id="7bd5b-179">Propose un lien vers la page de profil utilisateur dans ASP.NET Core identité.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7bd5b-180">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7bd5b-181">Pour les utilisateurs anonymes :</span><span class="sxs-lookup"><span data-stu-id="7bd5b-181">For anonymous users:</span></span>
  * <span data-ttu-id="7bd5b-182">Offre la possibilité de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-182">Offers the option to register.</span></span>
  * <span data-ttu-id="7bd5b-183">Offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="7bd5b-184">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="7bd5b-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7bd5b-185">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="7bd5b-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7bd5b-186">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="7bd5b-186">Run the app</span></span>

<span data-ttu-id="7bd5b-187">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="7bd5b-187">Run the app from the Server project.</span></span> <span data-ttu-id="7bd5b-188">Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="7bd5b-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7bd5b-189">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7bd5b-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
