---
title: Sécuriser une Blazor application autonome Webassembly ASP.net core à l’aide de la bibliothèque d’authentification
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ba6b3a333a021184ad8a42d6292915e908cc6eb7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103951"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="89d4f-102">Sécuriser une Blazor application autonome Webassembly ASP.net core à l’aide de la bibliothèque d’authentification</span><span class="sxs-lookup"><span data-stu-id="89d4f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="89d4f-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89d4f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89d4f-104">*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les instructions de cette rubrique. Consultez les rubriques AAD et AAD B2C dans ce nœud de table des matières.*</span><span class="sxs-lookup"><span data-stu-id="89d4f-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="89d4f-105">Pour créer une Blazor application Webassembly autonome qui utilise la bibliothèque [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) , exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="89d4f-105">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="89d4f-106">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="89d4f-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="89d4f-107">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="89d4f-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="89d4f-108">Dans Visual Studio, [créez une Blazor application webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="89d4f-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="89d4f-109">Définissez **l’authentification** sur **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** .</span><span class="sxs-lookup"><span data-stu-id="89d4f-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="89d4f-110">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="89d4f-110">Authentication package</span></span>

<span data-ttu-id="89d4f-111">Quand une application est créée pour utiliser des comptes d’utilisateur individuels, l’application reçoit automatiquement une référence de package pour le package [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="89d4f-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="89d4f-112">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="89d4f-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="89d4f-113">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="89d4f-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="89d4f-114">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="89d4f-114">Authentication service support</span></span>

<span data-ttu-id="89d4f-115">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> méthode d’extension fournie par le package [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="89d4f-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="89d4f-116">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="89d4f-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="89d4f-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="89d4f-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="89d4f-118">La configuration est fournie par le *wwwroot/appsettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="89d4f-118">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="89d4f-119">La prise en charge de l’authentification pour les applications autonomes est proposée à l’aide d’Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="89d4f-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="89d4f-120">La <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application à l’aide de OIDC.</span><span class="sxs-lookup"><span data-stu-id="89d4f-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="89d4f-121">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de l’adresse IP conforme à OIDC.</span><span class="sxs-lookup"><span data-stu-id="89d4f-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="89d4f-122">Obtenez les valeurs lors de l’inscription de l’application, qui se produit généralement dans son portail en ligne.</span><span class="sxs-lookup"><span data-stu-id="89d4f-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="89d4f-123">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="89d4f-123">Access token scopes</span></span>

<span data-ttu-id="89d4f-124">Le Blazor modèle Webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="89d4f-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="89d4f-125">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jetons par défaut du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="89d4f-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="89d4f-126">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="89d4f-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="89d4f-127">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="89d4f-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="89d4f-128">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="89d4f-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="89d4f-129">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="89d4f-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="89d4f-130">Page d'index</span><span class="sxs-lookup"><span data-stu-id="89d4f-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="89d4f-131">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="89d4f-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="89d4f-132">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="89d4f-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="89d4f-133">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="89d4f-133">LoginDisplay component</span></span>

<span data-ttu-id="89d4f-134">Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans le `MainLayout` composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="89d4f-134">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="89d4f-135">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="89d4f-135">For authenticated users:</span></span>
  * <span data-ttu-id="89d4f-136">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="89d4f-136">Displays the current username.</span></span>
  * <span data-ttu-id="89d4f-137">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="89d4f-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="89d4f-138">Pour les utilisateurs anonymes, offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="89d4f-138">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a><span data-ttu-id="89d4f-139">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="89d4f-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="89d4f-140">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="89d4f-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="89d4f-141">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="89d4f-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)