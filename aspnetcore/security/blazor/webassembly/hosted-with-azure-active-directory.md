---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110926"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="6d02f-102">Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d02f-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="6d02f-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6d02f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="6d02f-104">Les instructions de cet article s’appliquent à ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="6d02f-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="6d02f-105">Cette rubrique sera mise à jour pour couvrir l’aperçu 5 le vendredi 24 avril.</span><span class="sxs-lookup"><span data-stu-id="6d02f-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="6d02f-106">Cet article explique comment créer une [ Blazor application hébergée par webassembly](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="6d02f-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="6d02f-107">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="6d02f-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6d02f-108">Créer un client</span><span class="sxs-lookup"><span data-stu-id="6d02f-108">Create a tenant</span></span>

<span data-ttu-id="6d02f-109">Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="6d02f-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6d02f-110">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="6d02f-110">Register a server API app</span></span>

<span data-ttu-id="6d02f-111">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="6d02f-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6d02f-112">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-112">Select **New registration**.</span></span>
1. <span data-ttu-id="6d02f-113">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6d02f-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="6d02f-114">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="6d02f-115">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="6d02f-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="6d02f-116">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="6d02f-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6d02f-117">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6d02f-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-118">Select **Register**.</span></span>

<span data-ttu-id="6d02f-119">Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph** > **User. Read** , car l’application n’a pas besoin d’accéder à la connexion ou au profil UER.</span><span class="sxs-lookup"><span data-stu-id="6d02f-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="6d02f-120">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="6d02f-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="6d02f-121">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="6d02f-122">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="6d02f-123">Spécifiez un **nom d’étendue** (par `API.Access`exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6d02f-124">Spécifiez un **nom d’affichage du consentement administrateur** ( `Access API`par exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6d02f-125">Fournissez une **Description du consentement** de l’administrateur `Allows the app to access server app API endpoints.`(par exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6d02f-126">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="6d02f-127">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-127">Select **Add scope**.</span></span>

<span data-ttu-id="6d02f-128">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="6d02f-128">Record the following information:</span></span>

* <span data-ttu-id="6d02f-129">*Application API serveur* ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)</span><span class="sxs-lookup"><span data-stu-id="6d02f-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="6d02f-130">URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="6d02f-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="6d02f-131">ID de répertoire (ID de locataire) (par `222222222-2222-2222-2222-222222222222`exemple,)</span><span class="sxs-lookup"><span data-stu-id="6d02f-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="6d02f-132">Domaine du locataire AAD (par exemple `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="6d02f-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="6d02f-133">Étendue par défaut (par exemple `API.Access`,)</span><span class="sxs-lookup"><span data-stu-id="6d02f-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6d02f-134">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="6d02f-134">Register a client app</span></span>

<span data-ttu-id="6d02f-135">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application cliente* dans la zone de**inscriptions d’applications** **Azure Active Directory** > de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="6d02f-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6d02f-136">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-136">Select **New registration**.</span></span>
1. <span data-ttu-id="6d02f-137">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6d02f-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="6d02f-138">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="6d02f-139">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="6d02f-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="6d02f-140">Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.</span><span class="sxs-lookup"><span data-stu-id="6d02f-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="6d02f-141">Désactivez la case à cocher **autorisations** > **accorder à l’administrateur pour OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6d02f-142">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-142">Select **Register**.</span></span>

<span data-ttu-id="6d02f-143">Dans le**site Web\*\*\*\*configurations** > de la plateforme **d’authentification** > :</span><span class="sxs-lookup"><span data-stu-id="6d02f-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6d02f-144">Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.</span><span class="sxs-lookup"><span data-stu-id="6d02f-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6d02f-145">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6d02f-146">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="6d02f-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6d02f-147">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-147">Select the **Save** button.</span></span>

<span data-ttu-id="6d02f-148">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="6d02f-148">In **API permissions**:</span></span>

1. <span data-ttu-id="6d02f-149">Vérifiez que l’application a **Microsoft Graph** > autorisation**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="6d02f-150">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="6d02f-151">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor serveur AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6d02f-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="6d02f-152">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-152">Open the **API** list.</span></span>
1. <span data-ttu-id="6d02f-153">Activez l’accès à l’API (par exemple `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6d02f-154">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="6d02f-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="6d02f-155">Sélectionnez le bouton **Grant admin content for {locataire Name}** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="6d02f-156">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="6d02f-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="6d02f-157">Enregistrez l’ID de l’application *cliente* (ID client) (par `33333333-3333-3333-3333-333333333333`exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6d02f-158">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="6d02f-158">Create the app</span></span>

<span data-ttu-id="6d02f-159">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="6d02f-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="6d02f-160">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6d02f-161">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="6d02f-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6d02f-162">Passez l’URI ID d’application à `app-id-uri` l’option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="6d02f-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6d02f-163">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="6d02f-163">Server app configuration</span></span>

<span data-ttu-id="6d02f-164">*Cette section se rapporte à l’application **serveur** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="6d02f-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6d02f-165">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="6d02f-165">Authentication package</span></span>

<span data-ttu-id="6d02f-166">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API `Microsoft.AspNetCore.Authentication.AzureAD.UI`Web est assurée par le :</span><span class="sxs-lookup"><span data-stu-id="6d02f-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="6d02f-167">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="6d02f-167">Authentication service support</span></span>

<span data-ttu-id="6d02f-168">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="6d02f-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6d02f-169">La `AddAzureADBearer` méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="6d02f-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="6d02f-170">`UseAuthentication`et `UseAuthorization` Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="6d02f-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="6d02f-171">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="6d02f-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6d02f-172">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="6d02f-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="6d02f-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="6d02f-173">User.Identity.Name</span></span>

<span data-ttu-id="6d02f-174">Par défaut, l’API d’application serveur est `User.Identity.Name` renseignée avec la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` du type de revendication (par `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`exemple,).</span><span class="sxs-lookup"><span data-stu-id="6d02f-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="6d02f-175">Pour configurer l’application afin qu’elle reçoive la `name` valeur du type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6d02f-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="6d02f-176">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="6d02f-176">App settings</span></span>

<span data-ttu-id="6d02f-177">Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="6d02f-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="6d02f-178">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="6d02f-178">WeatherForecast controller</span></span>

<span data-ttu-id="6d02f-179">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' `[Authorize]` attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="6d02f-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="6d02f-180">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="6d02f-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="6d02f-181">L' `[Authorize]` attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="6d02f-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6d02f-182">L' `[Authorize]` attribut utilisé dans l' Blazor application webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="6d02f-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="6d02f-183">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="6d02f-183">Client app configuration</span></span>

<span data-ttu-id="6d02f-184">*Cette section se rapporte à l’application **cliente** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="6d02f-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6d02f-185">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="6d02f-185">Authentication package</span></span>

<span data-ttu-id="6d02f-186">Quand une application est créée pour utiliser des comptes professionnels ou scolaires`SingleOrg`(), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="6d02f-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6d02f-187">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="6d02f-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6d02f-188">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="6d02f-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="6d02f-189">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="6d02f-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="6d02f-190">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="6d02f-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6d02f-191">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="6d02f-191">Authentication service support</span></span>

<span data-ttu-id="6d02f-192">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="6d02f-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6d02f-193">Cette méthode configure tous les services requis pour que l’application interagisse avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="6d02f-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6d02f-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d02f-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6d02f-195">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="6d02f-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6d02f-196">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="6d02f-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="6d02f-197">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="6d02f-197">Access token scopes</span></span>

<span data-ttu-id="6d02f-198">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="6d02f-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6d02f-199">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="6d02f-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6d02f-200">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="6d02f-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6d02f-201">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="6d02f-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6d02f-202">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="6d02f-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6d02f-203">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="6d02f-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6d02f-204">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="6d02f-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6d02f-205">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="6d02f-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6d02f-206">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="6d02f-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="6d02f-207">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="6d02f-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6d02f-208">Page d'index</span><span class="sxs-lookup"><span data-stu-id="6d02f-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6d02f-209">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="6d02f-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6d02f-210">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6d02f-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6d02f-211">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6d02f-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6d02f-212">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="6d02f-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6d02f-213">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="6d02f-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6d02f-214">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="6d02f-214">Run the app</span></span>

<span data-ttu-id="6d02f-215">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="6d02f-215">Run the app from the Server project.</span></span> <span data-ttu-id="6d02f-216">Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="6d02f-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6d02f-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6d02f-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="6d02f-218">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="6d02f-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
