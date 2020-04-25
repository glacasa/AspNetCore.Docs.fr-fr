---
title: Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 4ccf86550a520f1d001088859ef5909041178781
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149996"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="62a68-102">Sécuriser une Blazor application autonome webassembly ASP.NET Core avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="62a68-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="62a68-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="62a68-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="62a68-104">Pour créer une Blazor application webassembly autonome qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="62a68-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="62a68-105">Suivez les instructions des rubriques suivantes pour créer un locataire et inscrire une application Web dans le portail Azure :</span><span class="sxs-lookup"><span data-stu-id="62a68-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="62a68-106">[Créez un AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; enregistrement de locataire avec les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="62a68-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="62a68-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="62a68-107">1\.</span></span> <span data-ttu-id="62a68-108">AAD B2C instance (par exemple, `https://contoso.b2clogin.com/`, qui comprend la barre oblique finale)</span><span class="sxs-lookup"><span data-stu-id="62a68-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="62a68-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="62a68-109">2\.</span></span> <span data-ttu-id="62a68-110">AAD B2C domaine de locataire (par exemple `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="62a68-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="62a68-111">[Inscrire une application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web effectuez les sélections suivantes lors de l’inscription de l’application :</span><span class="sxs-lookup"><span data-stu-id="62a68-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="62a68-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="62a68-112">1\.</span></span> <span data-ttu-id="62a68-113">Définissez **application Web/API Web** sur **Oui**.</span><span class="sxs-lookup"><span data-stu-id="62a68-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="62a68-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="62a68-114">2\.</span></span> <span data-ttu-id="62a68-115">Définissez **autoriser le Flow implicite** sur **Oui**.</span><span class="sxs-lookup"><span data-stu-id="62a68-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="62a68-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="62a68-116">3\.</span></span> <span data-ttu-id="62a68-117">Ajoutez une **URL** de réponse `https://localhost:5001/authentication/login-callback`de.</span><span class="sxs-lookup"><span data-stu-id="62a68-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="62a68-118">Enregistrez l’ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,).</span><span class="sxs-lookup"><span data-stu-id="62a68-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="62a68-119">[Créer des flux](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; d’utilisateurs créez un flux d’utilisateur d’inscription et de connexion.</span><span class="sxs-lookup"><span data-stu-id="62a68-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="62a68-120">Au minimum, sélectionnez l’attribut utilisateur des **revendications** > d’application**nom complet** pour remplir `context.User.Identity.Name` le dans `LoginDisplay` le composant (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="62a68-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="62a68-121">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple `B2C_1_signupsignin`,).</span><span class="sxs-lookup"><span data-stu-id="62a68-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="62a68-122">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="62a68-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="62a68-123">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="62a68-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="62a68-124">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="62a68-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="62a68-125">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a68-125">Authentication package</span></span>

<span data-ttu-id="62a68-126">Quand une application est créée pour utiliser un compte B2C individuel (`IndividualB2C`), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="62a68-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="62a68-127">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="62a68-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="62a68-128">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="62a68-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="62a68-129">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="62a68-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="62a68-130">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="62a68-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="62a68-131">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a68-131">Authentication service support</span></span>

<span data-ttu-id="62a68-132">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="62a68-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="62a68-133">Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="62a68-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="62a68-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="62a68-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="62a68-135">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="62a68-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="62a68-136">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration des comptes Microsoft lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="62a68-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="62a68-137">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="62a68-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="62a68-138">Exemple :</span><span class="sxs-lookup"><span data-stu-id="62a68-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="62a68-139">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="62a68-139">Access token scopes</span></span>

<span data-ttu-id="62a68-140">Le Blazor modèle webassembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="62a68-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="62a68-141">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par `MsalProviderOptions`défaut du :</span><span class="sxs-lookup"><span data-stu-id="62a68-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="62a68-142">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="62a68-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="62a68-143">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="62a68-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="62a68-144">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="62a68-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="62a68-145">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="62a68-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="62a68-146">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="62a68-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="62a68-147">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="62a68-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="62a68-148">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="62a68-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="62a68-149">Page d'index</span><span class="sxs-lookup"><span data-stu-id="62a68-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="62a68-150">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="62a68-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="62a68-151">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="62a68-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="62a68-152">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="62a68-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="62a68-153">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="62a68-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="62a68-154">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="62a68-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="62a68-155">Didacticiel : créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="62a68-155">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="62a68-156">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="62a68-156">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
