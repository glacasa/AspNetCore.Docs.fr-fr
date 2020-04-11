---
title: Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123451"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="cbb1b-102">Sécurisez une Blazor application autonome WebAssembly ASP.NET Core avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="cbb1b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="cbb1b-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cbb1b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="cbb1b-104">Pour créer Blazor une application autonome WebAssembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="cbb1b-105">Suivez les conseils dans les sujets suivants pour créer un locataire et enregistrer une application web dans le portail Azure :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="cbb1b-106">Créez un enregistrement de locataire &ndash; [AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) des informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="cbb1b-107">1\.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-107">1\.</span></span> <span data-ttu-id="cbb1b-108">AAD B2C instance (par exemple, , `https://contoso.b2clogin.com/`qui comprend la barre oblique de fuite)</span><span class="sxs-lookup"><span data-stu-id="cbb1b-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="cbb1b-109">2\.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-109">2\.</span></span> <span data-ttu-id="cbb1b-110">Domaine AAD B2C Tenant `contoso.onmicrosoft.com`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="cbb1b-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="cbb1b-111">[Enregistrez une application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Web Faites les sélections suivantes lors de l’inscription de l’application :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="cbb1b-112">1\.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-112">1\.</span></span> <span data-ttu-id="cbb1b-113">Définissez **l’application Web / API Web** à **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="cbb1b-114">2\.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-114">2\.</span></span> <span data-ttu-id="cbb1b-115">Définir **Permettre le flux implicite** à **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="cbb1b-116">3\.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-116">3\.</span></span> <span data-ttu-id="cbb1b-117">Ajouter une URL `https://localhost:5001/authentication/login-callback`de **réponse** de .</span><span class="sxs-lookup"><span data-stu-id="cbb1b-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="cbb1b-118">Enregistrez l’ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="cbb1b-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="cbb1b-119">[Créer des flux d’utilisateurs](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Créer un flux d’identifiants et d’inscription.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="cbb1b-120">Au minimum, sélectionnez **l’attribut** > utilisateur**de l’utilisateur Display Name** des revendications d’application pour remplir le `context.User.Identity.Name` `LoginDisplay` composant *(Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="cbb1b-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="cbb1b-121">Enregistrez le nom de flux d’utilisateur d’inscription et `B2C_1_signupsignin`d’inscription créé pour l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="cbb1b-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="cbb1b-122">Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="cbb1b-123">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="cbb1b-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cbb1b-124">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cbb1b-125">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbb1b-125">Authentication package</span></span>

<span data-ttu-id="cbb1b-126">Lorsqu’une application est créée pour utiliser`IndividualB2C`un compte B2C individuel ( ), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque Microsoft Authentication](/azure/active-directory/develop/msal-overview) ().`Microsoft.Authentication.WebAssembly.Msal`</span><span class="sxs-lookup"><span data-stu-id="cbb1b-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="cbb1b-127">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cbb1b-128">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="cbb1b-129">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="cbb1b-130">Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cbb1b-131">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbb1b-131">Authentication service support</span></span>

<span data-ttu-id="cbb1b-132">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="cbb1b-133">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="cbb1b-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cbb1b-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cbb1b-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="cbb1b-135">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cbb1b-136">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="cbb1b-137">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="cbb1b-137">Access token scopes</span></span>

<span data-ttu-id="cbb1b-138">Le Blazor modèle WebAssembly ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cbb1b-139">Pour fournir un jeton dans le cadre du flux d’inscription, ajoutez la `MsalProviderOptions`portée aux portées symboliques d’accès par défaut des :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="cbb1b-140">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="cbb1b-141">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="cbb1b-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="cbb1b-142">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="cbb1b-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="cbb1b-143">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="cbb1b-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="cbb1b-144">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="cbb1b-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cbb1b-145">Page d'index</span><span class="sxs-lookup"><span data-stu-id="cbb1b-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cbb1b-146">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="cbb1b-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cbb1b-147">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="cbb1b-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cbb1b-148">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="cbb1b-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cbb1b-149">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbb1b-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cbb1b-150">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cbb1b-150">Additional resources</span></span>

* [<span data-ttu-id="cbb1b-151">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cbb1b-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="cbb1b-152">Didacticiel : créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="cbb1b-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="cbb1b-153">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="cbb1b-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
