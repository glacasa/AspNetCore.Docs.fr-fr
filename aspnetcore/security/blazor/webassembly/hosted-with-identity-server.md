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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="0c1a7-103">Sécurisez une Blazor application WebAssembly ASP.NET Core avec Identity Server</span><span class="sxs-lookup"><span data-stu-id="0c1a7-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="0c1a7-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c1a7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="0c1a7-105">Pour créer Blazor une nouvelle application hébergée dans Visual Studio qui utilise [IdentityServer](https://identityserver.io/) pour authentifier les utilisateurs et les appels API :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="0c1a7-106">Utilisez Visual Studio pour créer une nouvelle \*\* Blazor application WebAssembly.\*\*</span><span class="sxs-lookup"><span data-stu-id="0c1a7-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="0c1a7-107">Pour plus d’informations, consultez <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="0c1a7-108">Dans le **dialogue Blazor Créer une nouvelle application,** sélectionnez **Changement** dans la section **Authentification.**</span><span class="sxs-lookup"><span data-stu-id="0c1a7-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="0c1a7-109">Sélectionnez **des comptes d’utilisateurs individuels** suivis par **OK**.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="0c1a7-110">Sélectionnez la **case à cocher hébergée ASP.NET Core** dans la section **Advanced.**</span><span class="sxs-lookup"><span data-stu-id="0c1a7-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="0c1a7-111">Cliquez sur le bouton **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-111">Select the **Create** button.</span></span>

<span data-ttu-id="0c1a7-112">Pour créer l’application dans une coque de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="0c1a7-113">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="0c1a7-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="0c1a7-114">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="0c1a7-115">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="0c1a7-115">Server app configuration</span></span>

<span data-ttu-id="0c1a7-116">Les sections suivantes décrivent les ajouts au projet lorsque le support d’authentification est inclus.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="0c1a7-117">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="0c1a7-117">Startup class</span></span>

<span data-ttu-id="0c1a7-118">La `Startup` classe a les ajouts suivants:</span><span class="sxs-lookup"><span data-stu-id="0c1a7-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="0c1a7-119">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="0c1a7-120">Identité avec l’interface utilisateur par défaut :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="0c1a7-121">IdentityServer avec <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> une méthode d’aide supplémentaire qui met en place quelques conventions par défaut ASP.NET Core en plus de IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="0c1a7-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="0c1a7-122">Authentification <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> avec une méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT produits par IdentityServer :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="0c1a7-123">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="0c1a7-124">Le middleware d’authentification qui est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur sur le contexte de la demande:</span><span class="sxs-lookup"><span data-stu-id="0c1a7-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="0c1a7-125">Le middleware IdentityServer qui expose les points de terminaison Open ID Connect (OIDC) :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="0c1a7-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="0c1a7-126">AddApiAuthorization</span></span>

<span data-ttu-id="0c1a7-127">La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’aide configure [IdentityServer](https://identityserver.io/) pour ASP.NET scénarios Core.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="0c1a7-128">IdentityServer est un cadre puissant et extensible pour le traitement des problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="0c1a7-129">IdentityServer expose la complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="0c1a7-130">Par conséquent, un ensemble de conventions et d’options de configuration est prévu que nous considérons un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="0c1a7-131">Une fois que vos besoins d’authentification changent, la pleine puissance d’IdentityServer est toujours disponible pour personnaliser l’authentification en fonction des exigences d’une application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="0c1a7-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="0c1a7-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="0c1a7-133">La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’aide configure un schéma de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="0c1a7-134">La stratégie est configurée pour permettre à Identity de traiter toutes `/Identity`les demandes acheminées vers n’importe quel sous-site dans l’espace URL d’identité .</span><span class="sxs-lookup"><span data-stu-id="0c1a7-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="0c1a7-135">Les <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> poignées toutes les autres demandes.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="0c1a7-136">En outre, cette méthode:</span><span class="sxs-lookup"><span data-stu-id="0c1a7-136">Additionally, this method:</span></span>

* <span data-ttu-id="0c1a7-137">Enregistre une `{APPLICATION NAME}API` ressource API auprès d’IdentityServer avec une portée par défaut de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="0c1a7-138">Configure le JWT Bearer Token Middleware pour valider les jetons émis par IdentityServer pour l’application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="0c1a7-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="0c1a7-139">WeatherForecastController</span></span>

<span data-ttu-id="0c1a7-140">Dans `WeatherForecastController` le *(Controllers/WeatherForecastController.cs*), l’attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) est appliqué à la classe.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="0c1a7-141">L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="0c1a7-142">La politique d’autorisation par défaut est configurée pour <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> utiliser le système d’authentification par défaut, qui est configuré par le régime de politique qui a été mentionné précédemment.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="0c1a7-143">La méthode d’aide configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> comme le gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="0c1a7-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="0c1a7-144">ApplicationDbContext</span></span>

<span data-ttu-id="0c1a7-145">Dans `ApplicationDbContext` le (*Data/ApplicationDbContext.cs*), la même chose <xref:Microsoft.EntityFrameworkCore.DbContext> est <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> utilisée dans Identity à l’exception qu’il s’étend pour inclure le schéma pour IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="0c1a7-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="0c1a7-147">Pour prendre le contrôle total du schéma de base <xref:Microsoft.EntityFrameworkCore.DbContext> de données, héritez de l’une des classes d’identité disponibles et configurez le contexte pour inclure le schéma d’identité en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la `OnModelCreating` méthode.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="0c1a7-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="0c1a7-148">OidcConfigurationController</span></span>

<span data-ttu-id="0c1a7-149">Dans `OidcConfigurationController` le *(Controllers/OidcConfigurationController.cs*), le critère d’évaluation du client est prévu pour servir les paramètres de l’OIDC.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="0c1a7-150">Fichiers de paramètres d’applications</span><span class="sxs-lookup"><span data-stu-id="0c1a7-150">App settings files</span></span>

<span data-ttu-id="0c1a7-151">Dans le fichier des paramètres de l’application *(appsettings.json*) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="0c1a7-152">Dans l’exemple suivant, il y a un seul client.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-152">In the following example, there's a single client.</span></span> <span data-ttu-id="0c1a7-153">Le nom du client correspond au nom de l’application `ClientId` et est cartographié par convention au paramètre OAuth.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="0c1a7-154">Le profil indique que le type d’application est configuré.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="0c1a7-155">Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="0c1a7-156">Dans le fichier de paramètres de l’application Environnement Développement *(appsettings. Development.json*) à la `IdentityServer` racine du projet, la section décrit la clé utilisée pour signer des jetons.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="0c1a7-157">Configuration de l’application client</span><span class="sxs-lookup"><span data-stu-id="0c1a7-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0c1a7-158">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="0c1a7-158">Authentication package</span></span>

<span data-ttu-id="0c1a7-159">Lorsqu’une application est créée pour`Individual`utiliser des comptes utilisateur individuels `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ( ), l’application reçoit automatiquement une référence de paquet pour le paquet dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="0c1a7-160">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0c1a7-161">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="0c1a7-162">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="0c1a7-163">Soutien à l’autorisation de l’API</span><span class="sxs-lookup"><span data-stu-id="0c1a7-163">API authorization support</span></span>

<span data-ttu-id="0c1a7-164">Le support pour l’authentification des utilisateurs est branché `Microsoft.AspNetCore.Components.WebAssembly.Authentication` sur le conteneur de service par la méthode d’extension fournie à l’intérieur du paquet.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="0c1a7-165">Cette méthode met en place tous les services nécessaires pour que l’application interagisse avec le système d’autorisation existant.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="0c1a7-166">Par défaut, il charge la configuration de `_configuration/{client-id}`l’application par convention à partir de .</span><span class="sxs-lookup"><span data-stu-id="0c1a7-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="0c1a7-167">Par convention, l’ID du client est réglé sur le nom d’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="0c1a7-168">Cette URL peut être modifiée pour indiquer un point de terminaison distinct en appelant la surcharge avec des options.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="0c1a7-169">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="0c1a7-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="0c1a7-170">Page d'index</span><span class="sxs-lookup"><span data-stu-id="0c1a7-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="0c1a7-171">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="0c1a7-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="0c1a7-172">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0c1a7-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="0c1a7-173">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0c1a7-173">LoginDisplay component</span></span>

<span data-ttu-id="0c1a7-174">Le `LoginDisplay` composant *(Shared/LoginDisplay.razor*) est `MainLayout` rendu dans le composant (*Shared/MainLayout.razor*) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="0c1a7-175">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-175">For authenticated users:</span></span>
  * <span data-ttu-id="0c1a7-176">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-176">Displays the current user name.</span></span>
  * <span data-ttu-id="0c1a7-177">Offre un lien vers la page de profil utilisateur dans ASP.NET’identité de base.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="0c1a7-178">Offre un bouton pour vous déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0c1a7-179">Pour les utilisateurs anonymes :</span><span class="sxs-lookup"><span data-stu-id="0c1a7-179">For anonymous users:</span></span>
  * <span data-ttu-id="0c1a7-180">Offre la possibilité de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-180">Offers the option to register.</span></span>
  * <span data-ttu-id="0c1a7-181">Offre la possibilité de vous connecter.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="0c1a7-182">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="0c1a7-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="0c1a7-183">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="0c1a7-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="0c1a7-184">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="0c1a7-184">Run the app</span></span>

<span data-ttu-id="0c1a7-185">Exécutez l’application à partir du projet Server.</span><span class="sxs-lookup"><span data-stu-id="0c1a7-185">Run the app from the Server project.</span></span> <span data-ttu-id="0c1a7-186">Lorsque vous utilisez Visual Studio, sélectionnez le projet Server dans **Solution Explorer** et sélectionnez le bouton **Run** dans la barre d’outils ou démarrez l’application à partir du menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="0c1a7-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0c1a7-187">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0c1a7-187">Additional resources</span></span>

* [<span data-ttu-id="0c1a7-188">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0c1a7-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
