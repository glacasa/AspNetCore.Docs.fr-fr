---
title: Sécuriser une Blazor application autonome webassembly ASP.net core à l’aide de la bibliothèque d’authentification
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110944"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="df49c-102">Sécuriser une Blazor application autonome webassembly ASP.net core à l’aide de la bibliothèque d’authentification</span><span class="sxs-lookup"><span data-stu-id="df49c-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="df49c-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="df49c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="df49c-104">Les instructions de cet article s’appliquent à ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="df49c-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="df49c-105">Cette rubrique sera mise à jour pour couvrir l’aperçu 5 le vendredi 24 avril.</span><span class="sxs-lookup"><span data-stu-id="df49c-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="df49c-106">*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les instructions de cette rubrique. Consultez les rubriques AAD et AAD B2C dans ce nœud de table des matières.*</span><span class="sxs-lookup"><span data-stu-id="df49c-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="df49c-107">Pour créer une Blazor application webassembly autonome qui `Microsoft.AspNetCore.Components.WebAssembly.Authentication` utilise une bibliothèque, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="df49c-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="df49c-108">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="df49c-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="df49c-109">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="df49c-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="df49c-110">Dans Visual Studio, [créez une Blazor application webassembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="df49c-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="df49c-111">Définissez **l’authentification** sur **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** .</span><span class="sxs-lookup"><span data-stu-id="df49c-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="df49c-112">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="df49c-112">Authentication package</span></span>

<span data-ttu-id="df49c-113">Quand une application est créée pour utiliser des comptes d’utilisateur individuels, l’application reçoit automatiquement une référence de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package pour le package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="df49c-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="df49c-114">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="df49c-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="df49c-115">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="df49c-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="df49c-116">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="df49c-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="df49c-117">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="df49c-117">Authentication service support</span></span>

<span data-ttu-id="df49c-118">La prise en charge de l’authentification des utilisateurs est inscrite `AddOidcAuthentication` dans le conteneur de service `Microsoft.AspNetCore.Components.WebAssembly.Authentication` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="df49c-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="df49c-119">Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="df49c-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="df49c-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="df49c-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="df49c-121">La prise en charge de l’authentification pour les applications autonomes est proposée à l’aide d’Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="df49c-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="df49c-122">La `AddOidcAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application à l’aide de OIDC.</span><span class="sxs-lookup"><span data-stu-id="df49c-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="df49c-123">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de l’adresse IP conforme à OIDC.</span><span class="sxs-lookup"><span data-stu-id="df49c-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="df49c-124">Obtenez les valeurs lors de l’inscription de l’application, qui se produit généralement dans son portail en ligne.</span><span class="sxs-lookup"><span data-stu-id="df49c-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="df49c-125">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="df49c-125">Access token scopes</span></span>

<span data-ttu-id="df49c-126">Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="df49c-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="df49c-127">Pour approvisionner un jeton dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jetons par défaut du `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="df49c-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="df49c-128">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="df49c-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="df49c-129">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="df49c-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="df49c-130">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="df49c-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="df49c-131">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="df49c-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="df49c-132">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="df49c-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="df49c-133">Page d'index</span><span class="sxs-lookup"><span data-stu-id="df49c-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="df49c-134">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="df49c-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="df49c-135">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="df49c-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="df49c-136">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="df49c-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="df49c-137">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="df49c-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="df49c-138">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="df49c-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 