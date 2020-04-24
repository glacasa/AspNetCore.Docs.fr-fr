---
title: Sécuriser une Blazor application autonome webassembly ASP.net core avec des comptes Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111186"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="ec4c2-102">Sécuriser une Blazor application autonome webassembly ASP.net core avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="ec4c2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="ec4c2-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ec4c2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="ec4c2-104">Les instructions de cet article s’appliquent à ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="ec4c2-105">Cette rubrique sera mise à jour pour couvrir l’aperçu 5 le vendredi 24 avril.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="ec4c2-106">Pour créer une Blazor application webassembly autonome qui utilise [des comptes Microsoft avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="ec4c2-107">Créer un locataire AAD et une application Web</span><span class="sxs-lookup"><span data-stu-id="ec4c2-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="ec4c2-108">Inscrire une application AAD dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="ec4c2-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-109">1\.</span></span> <span data-ttu-id="ec4c2-110">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="ec4c2-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-111">2\.</span></span> <span data-ttu-id="ec4c2-112">Dans **types de comptes pris en charge**, sélectionnez **comptes dans n’importe quel annuaire d’organisation**.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="ec4c2-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-113">3\.</span></span> <span data-ttu-id="ec4c2-114">Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="ec4c2-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-115">4\.</span></span> <span data-ttu-id="ec4c2-116">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="ec4c2-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="ec4c2-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-117">5\.</span></span> <span data-ttu-id="ec4c2-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-118">Select **Register**.</span></span>

   <span data-ttu-id="ec4c2-119">Dans le**site Web\*\*\*\*configurations** > de la plateforme **d’authentification** > :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="ec4c2-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-120">1\.</span></span> <span data-ttu-id="ec4c2-121">Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="ec4c2-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-122">2\.</span></span> <span data-ttu-id="ec4c2-123">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="ec4c2-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-124">3\.</span></span> <span data-ttu-id="ec4c2-125">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="ec4c2-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-126">4\.</span></span> <span data-ttu-id="ec4c2-127">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-127">Select the **Save** button.</span></span>

   <span data-ttu-id="ec4c2-128">Enregistrez l’ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="ec4c2-129">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="ec4c2-130">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ec4c2-131">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="ec4c2-132">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="ec4c2-133">Connectez-vous à l’application à l’aide d’un compte Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="ec4c2-134">Demandez des jetons d’accès pour les API Microsoft à l’aide de la Blazor même approche que pour les applications autonomes, à condition que vous ayez correctement configuré l’application.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="ec4c2-135">Pour plus d’informations, consultez [démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ec4c2-136">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="ec4c2-136">Authentication package</span></span>

<span data-ttu-id="ec4c2-137">Quand une application est créée pour utiliser des comptes professionnels ou scolaires`SingleOrg`(), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="ec4c2-138">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ec4c2-139">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="ec4c2-140">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="ec4c2-141">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ec4c2-142">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="ec4c2-142">Authentication service support</span></span>

<span data-ttu-id="ec4c2-143">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="ec4c2-144">Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="ec4c2-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ec4c2-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ec4c2-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="ec4c2-146">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ec4c2-147">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="ec4c2-148">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="ec4c2-148">Access token scopes</span></span>

<span data-ttu-id="ec4c2-149">Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ec4c2-150">Pour approvisionner un jeton dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut `MsalProviderOptions`du :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="ec4c2-151">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="ec4c2-152">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="ec4c2-153">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="ec4c2-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="ec4c2-154">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="ec4c2-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="ec4c2-155">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="ec4c2-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ec4c2-156">Page d'index</span><span class="sxs-lookup"><span data-stu-id="ec4c2-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="ec4c2-157">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="ec4c2-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ec4c2-158">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ec4c2-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ec4c2-159">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ec4c2-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ec4c2-160">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="ec4c2-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ec4c2-161">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ec4c2-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="ec4c2-162">Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="ec4c2-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="ec4c2-163">Démarrage rapide : configurer une application pour exposer des API Web</span><span class="sxs-lookup"><span data-stu-id="ec4c2-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
