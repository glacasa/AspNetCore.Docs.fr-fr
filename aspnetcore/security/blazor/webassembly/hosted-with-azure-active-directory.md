---
title: Sécurisez Blazor une application WebAssembly ASP.NET Core avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977130"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="3494f-102">Sécurisez Blazor une application WebAssembly ASP.NET Core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3494f-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="3494f-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3494f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="3494f-104">Cet article décrit comment créer une [ Blazor application hébergée Par WebAssembly](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="3494f-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="3494f-105">Enregistrez les applications dans AAD B2C et créez une solution</span><span class="sxs-lookup"><span data-stu-id="3494f-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="3494f-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="3494f-106">Create a tenant</span></span>

<span data-ttu-id="3494f-107">Suivez les conseils dans [Quickstart: Mettre en place un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="3494f-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="3494f-108">Enregistrez une application API serveur</span><span class="sxs-lookup"><span data-stu-id="3494f-108">Register a server API app</span></span>

<span data-ttu-id="3494f-109">Suivez les conseils de [Quickstart : Enregistrez une application avec la plate-forme d’identité Microsoft](/azure/active-directory/develop/quickstart-register-app) et les sujets Azure AAD suivants pour enregistrer une application AAD pour *l’application Server API* dans la zone**d’enregistrement** **azure Active Directory** > App du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="3494f-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3494f-110">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="3494f-110">Select **New registration**.</span></span>
1. <span data-ttu-id="3494f-111">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3494f-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3494f-112">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="3494f-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="3494f-113">Vous pouvez sélectionner **des comptes dans cet annuaire organisationnel uniquement** (locataire unique) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="3494f-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3494f-114">*L’application Server API* ne nécessite pas **d’URI Redirect dans** ce scénario, alors laissez la baisse vers le **Web** et n’entrez pas dans une URI rediriger.</span><span class="sxs-lookup"><span data-stu-id="3494f-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="3494f-115">Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**</span><span class="sxs-lookup"><span data-stu-id="3494f-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3494f-116">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="3494f-116">Select **Register**.</span></span>

<span data-ttu-id="3494f-117">Dans **les autorisations API**, supprimez l’autorisation **Microsoft Graph** > **User.Read,** car l’application ne nécessite pas d’accès au profil de connexion ou d’uer.</span><span class="sxs-lookup"><span data-stu-id="3494f-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="3494f-118">Dans **Expose une API**:</span><span class="sxs-lookup"><span data-stu-id="3494f-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="3494f-119">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="3494f-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="3494f-120">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="3494f-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="3494f-121">Fournir un **nom scope** `API.Access`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="3494f-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3494f-122">Fournir un **nom d’affichage de consentement d’administration** (par exemple, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="3494f-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="3494f-123">Fournir une description du consentement `Allows the app to access server app API endpoints.`de **l’administration** (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="3494f-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="3494f-124">Confirmez que **l’Etat** est prêt à **Enabled**.</span><span class="sxs-lookup"><span data-stu-id="3494f-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="3494f-125">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="3494f-125">Select **Add scope**.</span></span>

<span data-ttu-id="3494f-126">Enregistrez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="3494f-126">Record the following information:</span></span>

* <span data-ttu-id="3494f-127">*Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="3494f-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3494f-128">App ID URI (par exemple, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="3494f-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="3494f-129">Id d’annuaire (Id locataire) `222222222-2222-2222-2222-222222222222`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="3494f-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="3494f-130">Domaine AAD Tenant (par exemple, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="3494f-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="3494f-131">Portée par défaut `API.Access`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="3494f-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="3494f-132">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="3494f-132">Register a client app</span></span>

<span data-ttu-id="3494f-133">Suivez les conseils de [Quickstart : Enregistrez une application avec la plate-forme d’identité Microsoft](/azure/active-directory/develop/quickstart-register-app) et les sujets Azure AAD suivants pour enregistrer une application AAD pour *l’application Client* dans la zone **Azure Active Directory** > **App des enregistrements** du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="3494f-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3494f-134">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="3494f-134">Select **New registration**.</span></span>
1. <span data-ttu-id="3494f-135">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3494f-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="3494f-136">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="3494f-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="3494f-137">Vous pouvez sélectionner **des comptes dans cet annuaire organisationnel uniquement** (locataire unique) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="3494f-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="3494f-138">Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .</span><span class="sxs-lookup"><span data-stu-id="3494f-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="3494f-139">Désactiver **l’administration de** > la subvention d’autorisation d’autorisation concent à la case à cocher**des autorisations openid et offline_access.**</span><span class="sxs-lookup"><span data-stu-id="3494f-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3494f-140">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="3494f-140">Select **Register**.</span></span>

<span data-ttu-id="3494f-141">Dans les > **configurations de plate-forme** **d’authentification** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="3494f-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3494f-142">Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="3494f-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3494f-143">Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.</span><span class="sxs-lookup"><span data-stu-id="3494f-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3494f-144">Les autres défauts de paiement de l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="3494f-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3494f-145">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="3494f-145">Select the **Save** button.</span></span>

<span data-ttu-id="3494f-146">Dans **les autorisations de l’API**:</span><span class="sxs-lookup"><span data-stu-id="3494f-146">In **API permissions**:</span></span>

1. <span data-ttu-id="3494f-147">Confirmez que l’application dispose de l’autorisation **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="3494f-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="3494f-148">Sélectionnez **Ajouter une permission** suivie par Mes **API**.</span><span class="sxs-lookup"><span data-stu-id="3494f-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="3494f-149">Sélectionnez *l’application Server API* à partir de la colonne **Nom** (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3494f-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="3494f-150">Ouvrez la liste **API.**</span><span class="sxs-lookup"><span data-stu-id="3494f-150">Open the **API** list.</span></span>
1. <span data-ttu-id="3494f-151">Activer l’accès à l’API (par exemple, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="3494f-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="3494f-152">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="3494f-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="3494f-153">Sélectionnez le **contenu de l’administrateur Grant pour le bouton 'TENANT NAME'.**</span><span class="sxs-lookup"><span data-stu-id="3494f-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="3494f-154">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="3494f-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="3494f-155">Enregistrez l’ID *d’application* de l’application Client (IDENTIFIANT client) (par exemple, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="3494f-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="3494f-156">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="3494f-156">Create the app</span></span>

<span data-ttu-id="3494f-157">Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="3494f-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3494f-158">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="3494f-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3494f-159">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="3494f-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="3494f-160">Passez l’App ID URI à l’option, `app-id-uri` mais notez qu’un changement de configuration peut être nécessaire dans l’application client, qui est décrite dans la section portée des [jetons d’accès.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="3494f-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="3494f-161">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="3494f-161">Server app configuration</span></span>

<span data-ttu-id="3494f-162">*Cette section concerne l’application **Server** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="3494f-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3494f-163">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="3494f-163">Authentication package</span></span>

<span data-ttu-id="3494f-164">Le support pour l’authentification et l’autorisation des appels `Microsoft.AspNetCore.Authentication.AzureAD.UI`à ASP.NET API Web de base est fourni par le :</span><span class="sxs-lookup"><span data-stu-id="3494f-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="3494f-165">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="3494f-165">Authentication service support</span></span>

<span data-ttu-id="3494f-166">La `AddAuthentication` méthode configure les services d’authentification dans l’application et configure le gestionnaire JWT Bearer comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="3494f-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="3494f-167">La `AddAzureADBearer` méthode définit les paramètres spécifiques du gestionnaire JWT Bearer nécessaires pour valider les jetons émis par l’annuaire actif Azure :</span><span class="sxs-lookup"><span data-stu-id="3494f-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="3494f-168">`UseAuthentication`et `UseAuthorization` s’assurer que :</span><span class="sxs-lookup"><span data-stu-id="3494f-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="3494f-169">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="3494f-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="3494f-170">Toute demande visant à accéder à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="3494f-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="3494f-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="3494f-171">User.Identity.Name</span></span>

<span data-ttu-id="3494f-172">Par défaut, l’application Server `User.Identity.Name` API remplit `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` la valeur du `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`type de réclamation (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="3494f-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="3494f-173">Pour configurer l’application pour `name` recevoir la valeur du type de réclamation, configurez le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3494f-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="3494f-174">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="3494f-174">App settings</span></span>

<span data-ttu-id="3494f-175">Le fichier *appsettings.json* contient les options pour configurer le gestionnaire de porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="3494f-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="3494f-176">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="3494f-176">WeatherForecast controller</span></span>

<span data-ttu-id="3494f-177">Le contrôleur WeatherForecast *(Controllers/WeatherForecastController.cs*) expose une `[Authorize]` API protégée avec l’attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="3494f-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="3494f-178">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="3494f-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="3494f-179">L’attribut `[Authorize]` dans ce contrôleur API est la seule chose qui protège cette API contre l’accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="3494f-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="3494f-180">L’attribut `[Authorize]` utilisé Blazor dans l’application WebAssembly ne sert qu’à indicer à l’application que l’utilisateur doit être autorisé à travailler correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="3494f-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="3494f-181">Configuration de l’application client</span><span class="sxs-lookup"><span data-stu-id="3494f-181">Client app configuration</span></span>

<span data-ttu-id="3494f-182">*Cette section concerne l’application **Client** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="3494f-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3494f-183">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="3494f-183">Authentication package</span></span>

<span data-ttu-id="3494f-184">Lorsqu’une application est créée pour`SingleOrg`utiliser les comptes de travail ou d’école`Microsoft.Authentication.WebAssembly.Msal`(), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="3494f-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3494f-185">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="3494f-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3494f-186">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="3494f-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3494f-187">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="3494f-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3494f-188">Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.</span><span class="sxs-lookup"><span data-stu-id="3494f-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="3494f-189">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="3494f-189">Authentication service support</span></span>

<span data-ttu-id="3494f-190">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet.</span><span class="sxs-lookup"><span data-stu-id="3494f-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3494f-191">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="3494f-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3494f-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3494f-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="3494f-193">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="3494f-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3494f-194">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.</span><span class="sxs-lookup"><span data-stu-id="3494f-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="3494f-195">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="3494f-195">Access token scopes</span></span>

<span data-ttu-id="3494f-196">Les portées symboliques d’accès par défaut représentent la liste des portées symboliques d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="3494f-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="3494f-197">Inclus par défaut dans le signe en demande.</span><span class="sxs-lookup"><span data-stu-id="3494f-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="3494f-198">Utilisé pour fournir un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="3494f-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="3494f-199">Toutes les portées doivent appartenir à la même application par les règles Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="3494f-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="3494f-200">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API au besoin :</span><span class="sxs-lookup"><span data-stu-id="3494f-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3494f-201">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="3494f-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3494f-202">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="3494f-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3494f-203">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="3494f-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3494f-204">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="3494f-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="3494f-205">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="3494f-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3494f-206">Page d'index</span><span class="sxs-lookup"><span data-stu-id="3494f-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="3494f-207">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="3494f-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3494f-208">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3494f-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3494f-209">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3494f-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="3494f-210">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="3494f-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3494f-211">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="3494f-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3494f-212">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="3494f-212">Run the app</span></span>

<span data-ttu-id="3494f-213">Exécutez l’application à partir du projet Server.</span><span class="sxs-lookup"><span data-stu-id="3494f-213">Run the app from the Server project.</span></span> <span data-ttu-id="3494f-214">Lorsque vous utilisez Visual Studio, sélectionnez le projet Server dans **Solution Explorer** et sélectionnez le bouton **Run** dans la barre d’outils ou démarrez l’application à partir du menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="3494f-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3494f-215">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3494f-215">Additional resources</span></span>

* [<span data-ttu-id="3494f-216">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3494f-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="3494f-217">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="3494f-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
