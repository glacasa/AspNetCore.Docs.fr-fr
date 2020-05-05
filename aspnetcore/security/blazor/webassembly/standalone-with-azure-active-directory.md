---
title: Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory
author: guardrex
description: ''
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 77584c7102fd2b288227877a052addb9f2296fa6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776654"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="7637a-102">Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7637a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="7637a-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7637a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7637a-104">Pour créer une Blazor application webassembly autonome qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="7637a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="7637a-105">[Créez un client et une application Web AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="7637a-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="7637a-106">Inscrire une application AAD dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="7637a-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7637a-107">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7637a-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="7637a-108">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="7637a-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="7637a-109">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7637a-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="7637a-110">Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.</span><span class="sxs-lookup"><span data-stu-id="7637a-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="7637a-111">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="7637a-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7637a-112">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="7637a-112">Select **Register**.</span></span>

<span data-ttu-id="7637a-113">Dans le**site Web\*\*\*\*configurations** > de la plateforme **d’authentification** > :</span><span class="sxs-lookup"><span data-stu-id="7637a-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7637a-114">Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.</span><span class="sxs-lookup"><span data-stu-id="7637a-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7637a-115">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="7637a-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7637a-116">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7637a-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7637a-117">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7637a-117">Select the **Save** button.</span></span>

<span data-ttu-id="7637a-118">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7637a-118">Record the following information:</span></span>

* <span data-ttu-id="7637a-119">ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)</span><span class="sxs-lookup"><span data-stu-id="7637a-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="7637a-120">ID de répertoire (ID de locataire) (par `22222222-2222-2222-2222-222222222222`exemple,)</span><span class="sxs-lookup"><span data-stu-id="7637a-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="7637a-121">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7637a-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="7637a-122">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="7637a-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7637a-123">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="7637a-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7637a-124">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7637a-124">Authentication package</span></span>

<span data-ttu-id="7637a-125">Quand une application est créée pour utiliser des comptes professionnels ou scolaires`SingleOrg`(), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="7637a-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="7637a-126">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="7637a-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7637a-127">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7637a-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="7637a-128">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="7637a-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="7637a-129">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="7637a-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7637a-130">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="7637a-130">Authentication service support</span></span>

<span data-ttu-id="7637a-131">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="7637a-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="7637a-132">Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="7637a-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7637a-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7637a-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="7637a-134">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="7637a-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7637a-135">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="7637a-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="7637a-136">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7637a-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="7637a-137">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7637a-137">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="7637a-138">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7637a-138">Access token scopes</span></span>

<span data-ttu-id="7637a-139">Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="7637a-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7637a-140">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par `MsalProviderOptions`défaut du :</span><span class="sxs-lookup"><span data-stu-id="7637a-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="7637a-141">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="7637a-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="7637a-142">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="7637a-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="7637a-143">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="7637a-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="7637a-144">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="7637a-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7637a-145">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7637a-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7637a-146">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="7637a-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="7637a-147">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="7637a-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7637a-148">Page d'index</span><span class="sxs-lookup"><span data-stu-id="7637a-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="7637a-149">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="7637a-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7637a-150">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7637a-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7637a-151">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7637a-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7637a-152">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="7637a-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7637a-153">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7637a-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7637a-154">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="7637a-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
