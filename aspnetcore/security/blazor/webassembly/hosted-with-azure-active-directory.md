---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 3a2f3bdd194b9153c5d59af7adfad3a3c8c56b23
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776036"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="220a4-102">Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="220a4-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="220a4-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="220a4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="220a4-104">Cet article explique comment créer une [ Blazor application hébergée par webassembly](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="220a4-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="220a4-105">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="220a4-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="220a4-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="220a4-106">Create a tenant</span></span>

<span data-ttu-id="220a4-107">Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="220a4-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="220a4-108">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="220a4-108">Register a server API app</span></span>

<span data-ttu-id="220a4-109">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="220a4-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="220a4-110">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="220a4-110">Select **New registration**.</span></span>
1. <span data-ttu-id="220a4-111">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="220a4-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="220a4-112">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="220a4-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="220a4-113">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="220a4-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="220a4-114">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="220a4-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="220a4-115">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="220a4-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="220a4-116">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="220a4-116">Select **Register**.</span></span>

<span data-ttu-id="220a4-117">Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph** > **User. Read** , car l’application n’a pas besoin d’accéder à la connexion ou au profil UER.</span><span class="sxs-lookup"><span data-stu-id="220a4-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="220a4-118">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="220a4-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="220a4-119">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="220a4-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="220a4-120">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="220a4-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="220a4-121">Spécifiez un **nom d’étendue** (par `API.Access`exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="220a4-122">Spécifiez un **nom d’affichage du consentement administrateur** ( `Access API`par exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="220a4-123">Fournissez une **Description du consentement** de l’administrateur `Allows the app to access server app API endpoints.`(par exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="220a4-124">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="220a4-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="220a4-125">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="220a4-125">Select **Add scope**.</span></span>

<span data-ttu-id="220a4-126">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="220a4-126">Record the following information:</span></span>

* <span data-ttu-id="220a4-127">*Application API serveur* ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)</span><span class="sxs-lookup"><span data-stu-id="220a4-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="220a4-128">URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="220a4-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="220a4-129">ID de répertoire (ID de locataire) (par `222222222-2222-2222-2222-222222222222`exemple,)</span><span class="sxs-lookup"><span data-stu-id="220a4-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="220a4-130">Domaine du locataire AAD (par exemple `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="220a4-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="220a4-131">Étendue par défaut (par exemple `API.Access`,)</span><span class="sxs-lookup"><span data-stu-id="220a4-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="220a4-132">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="220a4-132">Register a client app</span></span>

<span data-ttu-id="220a4-133">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application cliente* dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="220a4-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="220a4-134">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="220a4-134">Select **New registration**.</span></span>
1. <span data-ttu-id="220a4-135">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="220a4-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="220a4-136">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="220a4-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="220a4-137">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="220a4-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="220a4-138">Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.</span><span class="sxs-lookup"><span data-stu-id="220a4-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="220a4-139">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="220a4-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="220a4-140">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="220a4-140">Select **Register**.</span></span>

<span data-ttu-id="220a4-141">Dans le**site Web\*\*\*\*configurations** > de la plateforme **d’authentification** > :</span><span class="sxs-lookup"><span data-stu-id="220a4-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="220a4-142">Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.</span><span class="sxs-lookup"><span data-stu-id="220a4-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="220a4-143">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="220a4-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="220a4-144">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="220a4-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="220a4-145">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="220a4-145">Select the **Save** button.</span></span>

<span data-ttu-id="220a4-146">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="220a4-146">In **API permissions**:</span></span>

1. <span data-ttu-id="220a4-147">Vérifiez que l’application a **Microsoft Graph** > autorisation**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="220a4-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="220a4-148">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="220a4-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="220a4-149">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor serveur AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="220a4-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="220a4-150">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="220a4-150">Open the **API** list.</span></span>
1. <span data-ttu-id="220a4-151">Activez l’accès à l’API (par exemple `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="220a4-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="220a4-152">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="220a4-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="220a4-153">Sélectionnez le bouton **Grant admin content for {locataire Name}** .</span><span class="sxs-lookup"><span data-stu-id="220a4-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="220a4-154">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="220a4-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="220a4-155">Enregistrez l’ID de l’application *cliente* (ID client) (par `33333333-3333-3333-3333-333333333333`exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="220a4-156">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="220a4-156">Create the app</span></span>

<span data-ttu-id="220a4-157">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="220a4-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="220a4-158">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="220a4-159">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="220a4-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="220a4-160">Passez l’URI ID d’application à `app-id-uri` l’option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="220a4-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="220a4-161">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="220a4-161">Server app configuration</span></span>

<span data-ttu-id="220a4-162">*Cette section se rapporte à l’application **serveur** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="220a4-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="220a4-163">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="220a4-163">Authentication package</span></span>

<span data-ttu-id="220a4-164">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API `Microsoft.AspNetCore.Authentication.AzureAD.UI`Web est assurée par le :</span><span class="sxs-lookup"><span data-stu-id="220a4-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="220a4-165">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="220a4-165">Authentication service support</span></span>

<span data-ttu-id="220a4-166">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="220a4-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="220a4-167">La `AddAzureADBearer` méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="220a4-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="220a4-168">`UseAuthentication`et `UseAuthorization` Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="220a4-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="220a4-169">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="220a4-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="220a4-170">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="220a4-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="220a4-171">Utilisateur. Identity. Nomme</span><span class="sxs-lookup"><span data-stu-id="220a4-171">User.Identity.Name</span></span>

<span data-ttu-id="220a4-172">Par défaut, l’API d’application serveur est `User.Identity.Name` renseignée avec la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` du type de revendication (par `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`exemple,).</span><span class="sxs-lookup"><span data-stu-id="220a4-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="220a4-173">Pour configurer l’application afin qu’elle reçoive la `name` valeur du type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="220a4-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="220a4-174">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="220a4-174">App settings</span></span>

<span data-ttu-id="220a4-175">Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="220a4-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="220a4-176">Exemple :</span><span class="sxs-lookup"><span data-stu-id="220a4-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="220a4-177">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="220a4-177">WeatherForecast controller</span></span>

<span data-ttu-id="220a4-178">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' `[Authorize]` attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="220a4-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="220a4-179">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="220a4-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="220a4-180">L' `[Authorize]` attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="220a4-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="220a4-181">L' `[Authorize]` attribut utilisé dans l' Blazor application webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="220a4-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="220a4-182">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="220a4-182">Client app configuration</span></span>

<span data-ttu-id="220a4-183">*Cette section se rapporte à l’application **cliente** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="220a4-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="220a4-184">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="220a4-184">Authentication package</span></span>

<span data-ttu-id="220a4-185">Quand une application est créée pour utiliser des comptes professionnels ou scolaires`SingleOrg`(), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="220a4-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="220a4-186">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="220a4-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="220a4-187">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="220a4-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="220a4-188">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="220a4-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="220a4-189">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="220a4-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="220a4-190">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="220a4-190">Authentication service support</span></span>

<span data-ttu-id="220a4-191">La prise `HttpClient` en charge des instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="220a4-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="220a4-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="220a4-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="220a4-193">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="220a4-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="220a4-194">Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="220a4-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="220a4-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="220a4-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="220a4-196">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="220a4-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="220a4-197">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="220a4-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="220a4-198">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="220a4-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="220a4-199">Exemple :</span><span class="sxs-lookup"><span data-stu-id="220a4-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="220a4-200">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="220a4-200">Access token scopes</span></span>

<span data-ttu-id="220a4-201">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="220a4-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="220a4-202">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="220a4-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="220a4-203">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="220a4-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="220a4-204">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="220a4-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="220a4-205">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="220a4-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="220a4-206">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="220a4-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="220a4-207">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="220a4-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="220a4-208">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="220a4-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="220a4-209">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="220a4-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="220a4-210">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="220a4-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="220a4-211">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="220a4-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="220a4-212">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="220a4-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="220a4-213">Page d'index</span><span class="sxs-lookup"><span data-stu-id="220a4-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="220a4-214">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="220a4-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="220a4-215">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="220a4-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="220a4-216">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="220a4-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="220a4-217">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="220a4-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="220a4-218">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="220a4-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="220a4-219">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="220a4-219">Run the app</span></span>

<span data-ttu-id="220a4-220">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="220a4-220">Run the app from the Server project.</span></span> <span data-ttu-id="220a4-221">Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="220a4-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="220a4-222">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="220a4-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="220a4-223">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="220a4-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
