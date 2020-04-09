---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec la bibliothèque Authentication
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977039"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="86f3b-102">Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec la bibliothèque Authentication</span><span class="sxs-lookup"><span data-stu-id="86f3b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="86f3b-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="86f3b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="86f3b-104">*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les orientations dans ce sujet. Voir les sujets AAD et AAD B2C dans ce tableau de nœud de contenu.*</span><span class="sxs-lookup"><span data-stu-id="86f3b-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="86f3b-105">Pour créer Blazor une application autonome WebAssembly qui utilise `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la bibliothèque, exécutez la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="86f3b-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="86f3b-106">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="86f3b-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="86f3b-107">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="86f3b-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="86f3b-108">Dans Visual Studio, [créez une Blazor application WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="86f3b-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="86f3b-109">Définissez **l’authentification** **des comptes utilisateur individuels** avec l’option Des comptes **d’utilisateurs Store.**</span><span class="sxs-lookup"><span data-stu-id="86f3b-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="86f3b-110">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="86f3b-110">Authentication package</span></span>

<span data-ttu-id="86f3b-111">Lorsqu’une application est créée pour utiliser des comptes utilisateur `Microsoft.AspNetCore.Components.WebAssembly.Authentication` individuels, l’application reçoit automatiquement une référence de paquet pour le paquet dans le fichier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="86f3b-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="86f3b-112">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="86f3b-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="86f3b-113">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="86f3b-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="86f3b-114">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="86f3b-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="86f3b-115">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="86f3b-115">Authentication service support</span></span>

<span data-ttu-id="86f3b-116">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddOidcAuthentication` méthode d’extension fournie par le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet.</span><span class="sxs-lookup"><span data-stu-id="86f3b-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="86f3b-117">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="86f3b-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="86f3b-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="86f3b-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="86f3b-119">Le support d’authentification pour les applications autonomes est offert à l’aide d’Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="86f3b-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="86f3b-120">La `AddOidcAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application à l’aide d’OIDC.</span><span class="sxs-lookup"><span data-stu-id="86f3b-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="86f3b-121">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la propriété intellectuelle conforme à l’OIDC.</span><span class="sxs-lookup"><span data-stu-id="86f3b-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="86f3b-122">Obtenez les valeurs lorsque vous enregistrez l’application, qui se produit généralement dans leur portail en ligne.</span><span class="sxs-lookup"><span data-stu-id="86f3b-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="86f3b-123">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="86f3b-123">Access token scopes</span></span>

<span data-ttu-id="86f3b-124">Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="86f3b-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="86f3b-125">Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez `OidcProviderOptions`la portée aux portées symboliques par défaut de la :</span><span class="sxs-lookup"><span data-stu-id="86f3b-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="86f3b-126">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="86f3b-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="86f3b-127">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="86f3b-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="86f3b-128">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="86f3b-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="86f3b-129">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="86f3b-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="86f3b-130">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="86f3b-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="86f3b-131">Page d'index</span><span class="sxs-lookup"><span data-stu-id="86f3b-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="86f3b-132">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="86f3b-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="86f3b-133">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="86f3b-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="86f3b-134">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="86f3b-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="86f3b-135">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="86f3b-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="86f3b-136">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="86f3b-136">Additional resources</span></span>

* [<span data-ttu-id="86f3b-137">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="86f3b-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
