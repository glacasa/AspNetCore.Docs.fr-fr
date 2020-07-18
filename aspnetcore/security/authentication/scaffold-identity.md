---
title: Génération Identity de modèles automatique dans les projets ASP.net Core
author: rick-anderson
description: Découvrez comment générer Identity une structure dans un projet ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: a8ca520d84d382b95cd4c0e2962ba4e5c922049e
ms.sourcegitcommit: 3544941682869734ea0113e24e02ed0ec9e1a9ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2020
ms.locfileid: "86464564"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="d89f1-103">Génération Identity de modèles automatique dans les projets ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d89f1-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="d89f1-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d89f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d89f1-105">ASP.NET Core fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d89f1-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d89f1-106">Les applications qui incluent Identity peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la Identity Razor bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="d89f1-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="d89f1-107">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="d89f1-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="d89f1-108">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="d89f1-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="d89f1-109">Le code généré est prioritaire sur le même code dans le Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="d89f1-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="d89f1-110">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une Identity source d’interface utilisateur complète](#full).</span><span class="sxs-lookup"><span data-stu-id="d89f1-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="d89f1-111">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le Identity package RCL.</span><span class="sxs-lookup"><span data-stu-id="d89f1-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="d89f1-112">Vous avez la possibilité de sélectionner du Identity code à générer.</span><span class="sxs-lookup"><span data-stu-id="d89f1-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="d89f1-113">Bien que le générateur de modèles génère la majeure partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="d89f1-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="d89f1-114">Ce document explique les étapes nécessaires à la réalisation d’une Identity mise à jour de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="d89f1-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="d89f1-115">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="d89f1-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="d89f1-116">Inspectez les modifications après avoir exécuté le générateur de Identity modèles.</span><span class="sxs-lookup"><span data-stu-id="d89f1-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="d89f1-117">Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="d89f1-118">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="d89f1-119">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="d89f1-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="d89f1-120">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="d89f1-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="d89f1-121">Lors de la génération de modèles automatique Identity avec un nouveau contexte de données dans un projet avec des comptes individuels existants :</span><span class="sxs-lookup"><span data-stu-id="d89f1-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="d89f1-122">Dans `Startup.ConfigureServices` , supprimez les appels à :</span><span class="sxs-lookup"><span data-stu-id="d89f1-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="d89f1-123">Par exemple, `AddDbContext` et `AddDefaultIdentity` sont commentés dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d89f1-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="d89f1-124">Le code précédent fait un commentaire sur le code qui est dupliqué dans *Areas/ Identity / Identity HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="d89f1-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="d89f1-125">En règle générale, les applications créées avec des comptes individuels ne doivent ***pas*** créer de contexte de données.</span><span class="sxs-lookup"><span data-stu-id="d89f1-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="d89f1-126">Génération de modèles automatique Identity dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="d89f1-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="d89f1-127">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d89f1-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="d89f1-128">Générer Identity une structure dans un Razor projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="d89f1-128">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="d89f1-129">est configuré dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-130">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="d89f1-131">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="d89f1-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="d89f1-132">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="d89f1-132">Enable authentication</span></span>

<span data-ttu-id="d89f1-133">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d89f1-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="d89f1-134">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="d89f1-134">Layout changes</span></span>

<span data-ttu-id="d89f1-135">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="d89f1-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="d89f1-136">Génération de modèles automatique Identity dans un Razor projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="d89f1-136">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="d89f1-137">Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-138">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="d89f1-139">Génération de modèles automatique Identity dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="d89f1-139">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="d89f1-140">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d89f1-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="d89f1-141">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d89f1-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="d89f1-142">est configuré dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-143">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="d89f1-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="d89f1-144">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d89f1-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="d89f1-145">Génération Identity de modèles automatique dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="d89f1-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="d89f1-146">Générer Identity une structure dans un Blazor Server projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="d89f1-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="d89f1-147">est configuré dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-148">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="d89f1-149">Migrations</span><span class="sxs-lookup"><span data-stu-id="d89f1-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="d89f1-150">Passer un jeton XSRF à l’application</span><span class="sxs-lookup"><span data-stu-id="d89f1-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="d89f1-151">Les jetons peuvent être passés à des composants :</span><span class="sxs-lookup"><span data-stu-id="d89f1-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="d89f1-152">Lorsque les jetons d’authentification sont approvisionnés et enregistrés dans le cookie d’authentification, ils peuvent être passés à des composants.</span><span class="sxs-lookup"><span data-stu-id="d89f1-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="d89f1-153">les composants ne peuvent pas être utilisés `HttpContext` directement. il n’existe donc aucun moyen d’obtenir un [jeton XSRF (contrefaçon anti-request)](xref:security/anti-request-forgery) pour effectuer une publication sur le Identity point de terminaison de déconnexion de `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="d89f1-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="d89f1-154">Un jeton XSRF peut être passé à des composants.</span><span class="sxs-lookup"><span data-stu-id="d89f1-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="d89f1-155">Pour plus d’informations, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="d89f1-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="d89f1-156">Dans le fichier *pages/_Host. cshtml* , établissez le jeton après l’avoir ajouté `InitialApplicationState` aux `TokenProvider` classes et :</span><span class="sxs-lookup"><span data-stu-id="d89f1-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="d89f1-157">Mettez à jour le `App` composant (*app. Razor*) pour affecter le `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="d89f1-158">Le `TokenProvider` service présenté dans la rubrique est utilisé dans le `LoginDisplay` composant dans la section [mise en page et changements de workflow d’authentification](#layout-and-authentication-flow-changes) suivants.</span><span class="sxs-lookup"><span data-stu-id="d89f1-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="d89f1-159">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="d89f1-159">Enable authentication</span></span>

<span data-ttu-id="d89f1-160">Dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="d89f1-160">In the `Startup` class:</span></span>

* <span data-ttu-id="d89f1-161">Confirmez que Razor les services de pages sont ajoutés dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d89f1-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="d89f1-162">Si vous utilisez [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), inscrivez le service.</span><span class="sxs-lookup"><span data-stu-id="d89f1-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="d89f1-163">Appelez `UseDatabaseErrorPage` sur le générateur d’applications dans `Startup.Configure` pour l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="d89f1-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="d89f1-164">Appelez `UseAuthentication` et `UseAuthorization` after `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="d89f1-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="d89f1-165">Ajoutez un point de terminaison pour les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="d89f1-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="d89f1-166">Modifications de la disposition et du workflow d’authentification</span><span class="sxs-lookup"><span data-stu-id="d89f1-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="d89f1-167">Ajoutez un `RedirectToLogin` composant (*RedirectToLogin. Razor*) au dossier *partagé* de l’application dans la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="d89f1-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Ajoutez un `LoginDisplay` composant (*LoginDisplay. Razor*) au dossier *partagé* de l’application. <span data-ttu-id="d89f1-169">Le [service TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fournit le jeton XSRF pour le formulaire HTML qui publie sur le Identity point de terminaison logout de déconnexion :</span><span class="sxs-lookup"><span data-stu-id="d89f1-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="d89f1-170">Dans le `MainLayout` composant (*Shared/MainLayout. Razor*), ajoutez le `LoginDisplay` composant au contenu de l’élément de ligne de plus haut niveau `<div>` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="d89f1-171">Points de terminaison d’authentification de style</span><span class="sxs-lookup"><span data-stu-id="d89f1-171">Style authentication endpoints</span></span>

<span data-ttu-id="d89f1-172">Étant donné que Blazor Server utilise Razor Identity les pages pages, le style de l’interface utilisateur change lorsqu’un visiteur navigue entre Identity les pages et les composants.</span><span class="sxs-lookup"><span data-stu-id="d89f1-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="d89f1-173">Vous avez deux options pour traiter les styles Incongruous :</span><span class="sxs-lookup"><span data-stu-id="d89f1-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="d89f1-174">Composants de build Identity</span><span class="sxs-lookup"><span data-stu-id="d89f1-174">Build Identity components</span></span>

<span data-ttu-id="d89f1-175">Une approche de l’utilisation de composants Identity au lieu de pages consiste à créer des Identity composants.</span><span class="sxs-lookup"><span data-stu-id="d89f1-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="d89f1-176">Étant donné que `SignInManager` et `UserManager` ne sont pas pris en charge dans Razor les composants, utilisez les points de terminaison d’API dans l' Blazor Server application pour traiter les actions de compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d89f1-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="d89f1-177">Utiliser une disposition personnalisée avec les Blazor styles d’application</span><span class="sxs-lookup"><span data-stu-id="d89f1-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="d89f1-178">La Identity disposition et les styles des pages peuvent être modifiés pour produire des pages qui utilisent le thème par défaut Blazor .</span><span class="sxs-lookup"><span data-stu-id="d89f1-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="d89f1-179">L’exemple de cette section est simplement un point de départ pour la personnalisation.</span><span class="sxs-lookup"><span data-stu-id="d89f1-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="d89f1-180">Un travail supplémentaire est probablement requis pour une expérience utilisateur optimale.</span><span class="sxs-lookup"><span data-stu-id="d89f1-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="d89f1-181">Créez un nouveau `NavMenu_IdentityLayout` composant (*Shared/NavMenu_ Identity Layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d89f1-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="d89f1-182">Pour le balisage et le code du composant, utilisez le même contenu du composant de l’application `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d89f1-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="d89f1-183">Supprimez les `NavLink` composants de tous les composants qui ne sont pas accessibles de façon anonyme, car les redirections automatiques du `RedirectToLogin` composant échouent pour les composants nécessitant une authentification ou une autorisation.</span><span class="sxs-lookup"><span data-stu-id="d89f1-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="d89f1-184">Dans le fichier *pages/Shared/Layout. cshtml* , apportez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="d89f1-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="d89f1-185">Ajoutez Razor des directives en haut du fichier pour utiliser tag Helper et les composants de l’application dans le dossier *partagé* :</span><span class="sxs-lookup"><span data-stu-id="d89f1-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="d89f1-186">Remplacez `{APPLICATION ASSEMBLY}` par le nom de l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d89f1-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="d89f1-187">Ajoutez une `<base>` balise et une Blazor feuille `<link>` de style au `<head>` contenu :</span><span class="sxs-lookup"><span data-stu-id="d89f1-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="d89f1-188">Remplacez le contenu de la `<body>` balise par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d89f1-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="d89f1-189">Génération de modèles automatique Identity dans un Blazor Server projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="d89f1-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="d89f1-190">Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-191">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="d89f1-192">Créer une Identity source d’interface utilisateur complète</span><span class="sxs-lookup"><span data-stu-id="d89f1-192">Create full Identity UI source</span></span>

<span data-ttu-id="d89f1-193">Pour conserver le contrôle total de l' Identity interface utilisateur, exécutez le générateur de Identity modèles automatique et sélectionnez **remplacer tous les fichiers**.</span><span class="sxs-lookup"><span data-stu-id="d89f1-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="d89f1-194">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par Identity Identity dans une application web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d89f1-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="d89f1-195">Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d89f1-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="d89f1-196">La valeur par défaut Identity est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d89f1-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="d89f1-197">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="d89f1-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="d89f1-198">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d89f1-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="d89f1-199">Configuration du mot de passe</span><span class="sxs-lookup"><span data-stu-id="d89f1-199">Password configuration</span></span>

<span data-ttu-id="d89f1-200">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="d89f1-201">`InputModel``Password`les propriétés se trouvent dans les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="d89f1-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="d89f1-202">Désactiver une page</span><span class="sxs-lookup"><span data-stu-id="d89f1-202">Disable a page</span></span>

<span data-ttu-id="d89f1-203">Cette section montre comment désactiver la page de Registre, mais l’approche peut être utilisée pour désactiver n’importe quelle page.</span><span class="sxs-lookup"><span data-stu-id="d89f1-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="d89f1-204">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="d89f1-204">To disable user registration:</span></span>

* <span data-ttu-id="d89f1-205">Génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-205">Scaffold Identity.</span></span> <span data-ttu-id="d89f1-206">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="d89f1-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="d89f1-207">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d89f1-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="d89f1-208">Mettre à jour les *zones/ Identity /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="d89f1-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="d89f1-209">Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="d89f1-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="d89f1-210">Commentez ou supprimez le lien d’inscription de *Areas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d89f1-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="d89f1-211">Mettez à jour la page *zones/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="d89f1-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="d89f1-212">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="d89f1-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="d89f1-213">Supprimez le code de confirmation du `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-213">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="d89f1-214">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d89f1-214">Use another app to add users</span></span>

<span data-ttu-id="d89f1-215">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="d89f1-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="d89f1-216">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d89f1-216">Options to add users include:</span></span>

* <span data-ttu-id="d89f1-217">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="d89f1-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="d89f1-218">Application console.</span><span class="sxs-lookup"><span data-stu-id="d89f1-218">A console app.</span></span>

<span data-ttu-id="d89f1-219">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="d89f1-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="d89f1-220">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="d89f1-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="d89f1-221">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d89f1-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="d89f1-222">L’utilisateur est ajouté à la Identity base de données.</span><span class="sxs-lookup"><span data-stu-id="d89f1-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="d89f1-223">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d89f1-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="d89f1-224">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d89f1-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="d89f1-225">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="d89f1-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="d89f1-226">Empêcher la publication d' Identity actifs statiques</span><span class="sxs-lookup"><span data-stu-id="d89f1-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d89f1-227">Pour empêcher la publication Identity des ressources statiques sur la racine Web, consultez <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="d89f1-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d89f1-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d89f1-228">Additional resources</span></span>

* [<span data-ttu-id="d89f1-229">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="d89f1-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d89f1-230">ASP.NET Core 2,1 et versions ultérieures fournissent [ Identity ASP.net Core](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d89f1-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d89f1-231">Les applications qui incluent Identity peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la Identity Razor bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="d89f1-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="d89f1-232">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="d89f1-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="d89f1-233">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="d89f1-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="d89f1-234">Le code généré est prioritaire sur le même code dans le Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="d89f1-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="d89f1-235">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).</span><span class="sxs-lookup"><span data-stu-id="d89f1-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="d89f1-236">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le Identity package RCL.</span><span class="sxs-lookup"><span data-stu-id="d89f1-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="d89f1-237">Vous avez la possibilité de sélectionner du Identity code à générer.</span><span class="sxs-lookup"><span data-stu-id="d89f1-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="d89f1-238">Bien que le générateur de modèles génère la plus grande partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="d89f1-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="d89f1-239">Ce document explique les étapes nécessaires à la réalisation d’une Identity mise à jour de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="d89f1-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="d89f1-240">Quand vous exécutez le générateur de Identity modèles, un fichier de *ScaffoldingReadme.txt* est créé dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="d89f1-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="d89f1-241">Le fichier *ScaffoldingReadme.txt* contient des instructions générales sur ce qui est nécessaire pour effectuer la Identity mise à jour de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="d89f1-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="d89f1-242">Ce document contient des instructions plus complètes que le fichier *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="d89f1-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="d89f1-243">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="d89f1-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="d89f1-244">Inspectez les modifications après avoir exécuté le générateur de Identity modèles.</span><span class="sxs-lookup"><span data-stu-id="d89f1-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="d89f1-245">Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="d89f1-246">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="d89f1-247">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="d89f1-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="d89f1-248">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="d89f1-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="d89f1-249">Génération de modèles automatique Identity dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="d89f1-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="d89f1-250">Ajoutez les appels en surbrillance suivants à la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="d89f1-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="d89f1-251">Générer Identity une structure dans un Razor projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="d89f1-251">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="d89f1-252">est configuré dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-252"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-253">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="d89f1-254">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="d89f1-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="d89f1-255">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="d89f1-255">Enable authentication</span></span>

<span data-ttu-id="d89f1-256">Dans la `Configure` méthode de la `Startup` classe, appelez [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="d89f1-257">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="d89f1-257">Layout changes</span></span>

<span data-ttu-id="d89f1-258">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="d89f1-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="d89f1-259">Génération de modèles automatique Identity dans un Razor projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="d89f1-259">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="d89f1-260">Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-261">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="d89f1-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="d89f1-262">Génération de modèles automatique Identity dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="d89f1-262">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="d89f1-263">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d89f1-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="d89f1-264">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d89f1-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="d89f1-265">est configuré dans *Areas/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d89f1-265"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d89f1-266">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="d89f1-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="d89f1-267">Appeler [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="d89f1-268">Génération Identity de modèles automatique dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="d89f1-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="d89f1-269">Supprimez le dossier *pages/Shared* et les fichiers de ce dossier.</span><span class="sxs-lookup"><span data-stu-id="d89f1-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="d89f1-270">Créer une Identity source d’interface utilisateur complète</span><span class="sxs-lookup"><span data-stu-id="d89f1-270">Create full Identity UI source</span></span>

<span data-ttu-id="d89f1-271">Pour conserver le contrôle total de l' Identity interface utilisateur, exécutez le générateur de Identity modèles automatique et sélectionnez **remplacer tous les fichiers**.</span><span class="sxs-lookup"><span data-stu-id="d89f1-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="d89f1-272">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par Identity Identity dans une application web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d89f1-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="d89f1-273">Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d89f1-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="d89f1-274">La valeur par défaut Identity est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d89f1-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="d89f1-275">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="d89f1-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="d89f1-276">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="d89f1-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="d89f1-277">Configuration du mot de passe</span><span class="sxs-lookup"><span data-stu-id="d89f1-277">Password configuration</span></span>

<span data-ttu-id="d89f1-278">Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="d89f1-279">`InputModel``Password`les propriétés se trouvent dans les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="d89f1-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="d89f1-280">Désactiver la page de Registre</span><span class="sxs-lookup"><span data-stu-id="d89f1-280">Disable register page</span></span>

<span data-ttu-id="d89f1-281">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="d89f1-281">To disable user registration:</span></span>

* <span data-ttu-id="d89f1-282">Génération de modèles automatique Identity .</span><span class="sxs-lookup"><span data-stu-id="d89f1-282">Scaffold Identity.</span></span> <span data-ttu-id="d89f1-283">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="d89f1-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="d89f1-284">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d89f1-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="d89f1-285">Mettre à jour les *zones/ Identity /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="d89f1-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="d89f1-286">Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="d89f1-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="d89f1-287">Commentez ou supprimez le lien d’inscription de *Areas/ Identity /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d89f1-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="d89f1-288">Mettez à jour la page *zones/ Identity /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="d89f1-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="d89f1-289">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="d89f1-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="d89f1-290">Supprimez le code de confirmation du `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="d89f1-290">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="d89f1-291">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d89f1-291">Use another app to add users</span></span>

<span data-ttu-id="d89f1-292">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="d89f1-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="d89f1-293">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d89f1-293">Options to add users include:</span></span>

* <span data-ttu-id="d89f1-294">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="d89f1-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="d89f1-295">Application console.</span><span class="sxs-lookup"><span data-stu-id="d89f1-295">A console app.</span></span>

<span data-ttu-id="d89f1-296">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="d89f1-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="d89f1-297">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="d89f1-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="d89f1-298">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d89f1-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="d89f1-299">L’utilisateur est ajouté à la Identity base de données.</span><span class="sxs-lookup"><span data-stu-id="d89f1-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="d89f1-300">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d89f1-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="d89f1-301">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d89f1-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="d89f1-302">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="d89f1-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d89f1-303">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d89f1-303">Additional resources</span></span>

* [<span data-ttu-id="d89f1-304">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="d89f1-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
