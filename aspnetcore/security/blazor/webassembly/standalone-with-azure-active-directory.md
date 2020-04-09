---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976992"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="62a6b-102">Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="62a6b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="62a6b-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="62a6b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="62a6b-104">Pour créer Blazor une application autonome WebAssembly qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="62a6b-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="62a6b-105">[Créer un locataire et une application web AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="62a6b-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="62a6b-106">Enregistrez une application AAD dans la zone**d’enregistrement de l’application** **Azure Active Directory** > du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="62a6b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="62a6b-107">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="62a6b-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="62a6b-108">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="62a6b-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="62a6b-109">Vous pouvez sélectionner **des comptes dans cet annuaire organisationnel uniquement** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="62a6b-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="62a6b-110">Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .</span><span class="sxs-lookup"><span data-stu-id="62a6b-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="62a6b-111">Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**</span><span class="sxs-lookup"><span data-stu-id="62a6b-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="62a6b-112">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="62a6b-112">Select **Register**.</span></span>

<span data-ttu-id="62a6b-113">Dans les > **configurations de plate-forme** **d’authentification** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="62a6b-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="62a6b-114">Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="62a6b-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="62a6b-115">Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.</span><span class="sxs-lookup"><span data-stu-id="62a6b-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="62a6b-116">Les autres défauts de paiement de l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="62a6b-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="62a6b-117">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="62a6b-117">Select the **Save** button.</span></span>

<span data-ttu-id="62a6b-118">Enregistrez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="62a6b-118">Record the following information:</span></span>

* <span data-ttu-id="62a6b-119">ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="62a6b-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="62a6b-120">Id d’annuaire (Id locataire) `22222222-2222-2222-2222-222222222222`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="62a6b-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="62a6b-121">Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="62a6b-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="62a6b-122">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="62a6b-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="62a6b-123">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="62a6b-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="62a6b-124">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a6b-124">Authentication package</span></span>

<span data-ttu-id="62a6b-125">Lorsqu’une application est créée pour`SingleOrg`utiliser les comptes de travail ou d’école`Microsoft.Authentication.WebAssembly.Msal`(), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="62a6b-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="62a6b-126">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="62a6b-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="62a6b-127">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="62a6b-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="62a6b-128">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="62a6b-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="62a6b-129">Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.</span><span class="sxs-lookup"><span data-stu-id="62a6b-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="62a6b-130">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a6b-130">Authentication service support</span></span>

<span data-ttu-id="62a6b-131">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet.</span><span class="sxs-lookup"><span data-stu-id="62a6b-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="62a6b-132">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="62a6b-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="62a6b-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="62a6b-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="62a6b-134">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="62a6b-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="62a6b-135">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.</span><span class="sxs-lookup"><span data-stu-id="62a6b-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="62a6b-136">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="62a6b-136">Access token scopes</span></span>

<span data-ttu-id="62a6b-137">Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="62a6b-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="62a6b-138">Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez la `MsalProviderOptions`portée aux portées symboliques d’accès par défaut des :</span><span class="sxs-lookup"><span data-stu-id="62a6b-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="62a6b-139">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="62a6b-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="62a6b-140">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="62a6b-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="62a6b-141">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="62a6b-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="62a6b-142">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="62a6b-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="62a6b-143">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="62a6b-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="62a6b-144">Page d'index</span><span class="sxs-lookup"><span data-stu-id="62a6b-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="62a6b-145">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="62a6b-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="62a6b-146">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="62a6b-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="62a6b-147">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="62a6b-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="62a6b-148">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a6b-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="62a6b-149">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="62a6b-149">Additional resources</span></span>

* [<span data-ttu-id="62a6b-150">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="62a6b-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="62a6b-151">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="62a6b-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
