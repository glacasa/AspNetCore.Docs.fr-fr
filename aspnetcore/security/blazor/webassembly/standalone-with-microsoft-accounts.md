---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Microsoft Accounts
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977078"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="52b63-102">Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Microsoft Accounts</span><span class="sxs-lookup"><span data-stu-id="52b63-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="52b63-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52b63-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="52b63-104">Pour créer Blazor une application autonome WebAssembly qui utilise [microsoft Accounts avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="52b63-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="52b63-105">Créer un locataire et une application web AAD</span><span class="sxs-lookup"><span data-stu-id="52b63-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="52b63-106">Enregistrez une application AAD dans la zone**d’enregistrement de l’application** **Azure Active Directory** > du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="52b63-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="52b63-107">1\.</span><span class="sxs-lookup"><span data-stu-id="52b63-107">1\.</span></span> <span data-ttu-id="52b63-108">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="52b63-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="52b63-109">2\.</span><span class="sxs-lookup"><span data-stu-id="52b63-109">2\.</span></span> <span data-ttu-id="52b63-110">Dans **les types de compte pris en**charge , sélectionnez des comptes dans **n’importe quel répertoire organisationnel.**</span><span class="sxs-lookup"><span data-stu-id="52b63-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="52b63-111">3\.</span><span class="sxs-lookup"><span data-stu-id="52b63-111">3\.</span></span> <span data-ttu-id="52b63-112">Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .</span><span class="sxs-lookup"><span data-stu-id="52b63-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="52b63-113">4\.</span><span class="sxs-lookup"><span data-stu-id="52b63-113">4\.</span></span> <span data-ttu-id="52b63-114">Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**</span><span class="sxs-lookup"><span data-stu-id="52b63-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="52b63-115">5\.</span><span class="sxs-lookup"><span data-stu-id="52b63-115">5\.</span></span> <span data-ttu-id="52b63-116">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="52b63-116">Select **Register**.</span></span>

   <span data-ttu-id="52b63-117">Dans les > **configurations de plate-forme** **d’authentification** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="52b63-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="52b63-118">1\.</span><span class="sxs-lookup"><span data-stu-id="52b63-118">1\.</span></span> <span data-ttu-id="52b63-119">Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="52b63-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="52b63-120">2\.</span><span class="sxs-lookup"><span data-stu-id="52b63-120">2\.</span></span> <span data-ttu-id="52b63-121">Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.</span><span class="sxs-lookup"><span data-stu-id="52b63-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="52b63-122">3\.</span><span class="sxs-lookup"><span data-stu-id="52b63-122">3\.</span></span> <span data-ttu-id="52b63-123">Les autres défauts de paiement de l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="52b63-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="52b63-124">4\.</span><span class="sxs-lookup"><span data-stu-id="52b63-124">4\.</span></span> <span data-ttu-id="52b63-125">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="52b63-125">Select the **Save** button.</span></span>

   <span data-ttu-id="52b63-126">Enregistrez l’ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="52b63-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="52b63-127">Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="52b63-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="52b63-128">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="52b63-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="52b63-129">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="52b63-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="52b63-130">Après avoir créé l’application, vous devriez être en mesure de:</span><span class="sxs-lookup"><span data-stu-id="52b63-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="52b63-131">Connectez-vous à l’application à l’aide d’un compte Microsoft.</span><span class="sxs-lookup"><span data-stu-id="52b63-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="52b63-132">Demandez des jetons d’accès pour les API Blazor Microsoft en utilisant la même approche que pour les applications autonomes à condition que vous ayez configuré l’application correctement.</span><span class="sxs-lookup"><span data-stu-id="52b63-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="52b63-133">Pour plus d’informations, voir [Quickstart: Configurer une application pour exposer les API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="52b63-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="52b63-134">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="52b63-134">Authentication package</span></span>

<span data-ttu-id="52b63-135">Lorsqu’une application est créée pour`SingleOrg`utiliser les comptes de travail ou d’école`Microsoft.Authentication.WebAssembly.Msal`(), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="52b63-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="52b63-136">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="52b63-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="52b63-137">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="52b63-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="52b63-138">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="52b63-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="52b63-139">Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.</span><span class="sxs-lookup"><span data-stu-id="52b63-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="52b63-140">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="52b63-140">Authentication service support</span></span>

<span data-ttu-id="52b63-141">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet.</span><span class="sxs-lookup"><span data-stu-id="52b63-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="52b63-142">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="52b63-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="52b63-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="52b63-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="52b63-144">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="52b63-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="52b63-145">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous enregistrez l’application.</span><span class="sxs-lookup"><span data-stu-id="52b63-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="52b63-146">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="52b63-146">Access token scopes</span></span>

<span data-ttu-id="52b63-147">Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="52b63-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="52b63-148">Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez la `MsalProviderOptions`portée aux portées symboliques d’accès par défaut des :</span><span class="sxs-lookup"><span data-stu-id="52b63-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="52b63-149">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="52b63-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="52b63-150">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="52b63-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="52b63-151">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="52b63-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="52b63-152">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="52b63-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="52b63-153">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="52b63-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="52b63-154">Page d'index</span><span class="sxs-lookup"><span data-stu-id="52b63-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="52b63-155">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="52b63-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="52b63-156">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="52b63-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="52b63-157">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="52b63-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="52b63-158">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="52b63-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="52b63-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="52b63-159">Additional resources</span></span>

* [<span data-ttu-id="52b63-160">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="52b63-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="52b63-161">Quickstart : Enregistrez une application avec la plate-forme d’identité Microsoft</span><span class="sxs-lookup"><span data-stu-id="52b63-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="52b63-162">Quickstart: Configurer une application pour exposer les API Web</span><span class="sxs-lookup"><span data-stu-id="52b63-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
