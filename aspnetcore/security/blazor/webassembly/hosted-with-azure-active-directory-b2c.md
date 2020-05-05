---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory B2C
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 05068853615a63611188175d95c27f1442973a86
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768206"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="40f6b-102">Sécuriser une Blazor application hébergée par l’ASP.net Core webassembly avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="40f6b-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="40f6b-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="40f6b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="40f6b-104">Cet article explique comment créer une Blazor application autonome webassembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="40f6b-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="40f6b-105">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="40f6b-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="40f6b-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="40f6b-106">Create a tenant</span></span>

<span data-ttu-id="40f6b-107">Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C et enregistrer les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="40f6b-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="40f6b-108">AAD B2C instance (par exemple, `https://contoso.b2clogin.com/`, qui comprend la barre oblique finale)</span><span class="sxs-lookup"><span data-stu-id="40f6b-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="40f6b-109">AAD B2C domaine de locataire (par exemple `contoso.onmicrosoft.com`,)</span><span class="sxs-lookup"><span data-stu-id="40f6b-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="40f6b-110">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="40f6b-110">Register a server API app</span></span>

<span data-ttu-id="40f6b-111">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour inscrire une application AAD pour l' *application API serveur* dans la zone**inscriptions d’applications** **Azure Active Directory** > de la portail Azure :</span><span class="sxs-lookup"><span data-stu-id="40f6b-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="40f6b-112">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-112">Select **New registration**.</span></span>
1. <span data-ttu-id="40f6b-113">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="40f6b-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="40f6b-114">Pour les **types de comptes pris en charge**, sélectionnez les **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="40f6b-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="40f6b-115">(multi-locataire) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="40f6b-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="40f6b-116">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="40f6b-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="40f6b-117">Vérifiez que **Permissions** > **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="40f6b-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="40f6b-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-118">Select **Register**.</span></span>

<span data-ttu-id="40f6b-119">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="40f6b-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="40f6b-120">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="40f6b-121">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="40f6b-122">Spécifiez un **nom d’étendue** (par `API.Access`exemple,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="40f6b-123">Spécifiez un **nom d’affichage du consentement administrateur** ( `Access API`par exemple,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="40f6b-124">Fournissez une **Description du consentement** de l’administrateur `Allows the app to access server app API endpoints.`(par exemple,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="40f6b-125">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="40f6b-126">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-126">Select **Add scope**.</span></span>

<span data-ttu-id="40f6b-127">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="40f6b-127">Record the following information:</span></span>

* <span data-ttu-id="40f6b-128">*Application API serveur* ID d’application (ID client) (par exemple `11111111-1111-1111-1111-111111111111`,)</span><span class="sxs-lookup"><span data-stu-id="40f6b-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="40f6b-129">URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="40f6b-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="40f6b-130">ID de répertoire (ID de locataire) (par `222222222-2222-2222-2222-222222222222`exemple,)</span><span class="sxs-lookup"><span data-stu-id="40f6b-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="40f6b-131">*Application API serveur* URI ID d’application (par exemple `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`,, le portail Azure peut être la valeur par défaut de l’ID client)</span><span class="sxs-lookup"><span data-stu-id="40f6b-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="40f6b-132">Étendue par défaut (par exemple `API.Access`,)</span><span class="sxs-lookup"><span data-stu-id="40f6b-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="40f6b-133">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="40f6b-133">Register a client app</span></span>

<span data-ttu-id="40f6b-134">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *application cliente* dans la zone**inscriptions d’applications** **Azure Active Directory** > du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="40f6b-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="40f6b-135">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-135">Select **New registration**.</span></span>
1. <span data-ttu-id="40f6b-136">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="40f6b-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="40f6b-137">Pour les **types de comptes pris en charge**, sélectionnez les **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="40f6b-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="40f6b-138">(multi-locataire) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="40f6b-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="40f6b-139">Laissez la liste déroulante **URI de redirection** définie sur **Web**et indiquez un URI de `https://localhost:5001/authentication/login-callback`redirection.</span><span class="sxs-lookup"><span data-stu-id="40f6b-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="40f6b-140">Vérifiez que **Permissions** > **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="40f6b-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="40f6b-141">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-141">Select **Register**.</span></span>

<span data-ttu-id="40f6b-142">Dans le**site Web\*\*\*\*configurations** > de la plateforme **d’authentification** > :</span><span class="sxs-lookup"><span data-stu-id="40f6b-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="40f6b-143">Confirmez que l’URI de `https://localhost:5001/authentication/login-callback` **redirection** de est présent.</span><span class="sxs-lookup"><span data-stu-id="40f6b-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="40f6b-144">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="40f6b-145">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="40f6b-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="40f6b-146">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-146">Select the **Save** button.</span></span>

<span data-ttu-id="40f6b-147">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="40f6b-147">In **API permissions**:</span></span>

1. <span data-ttu-id="40f6b-148">Vérifiez que l’application a **Microsoft Graph** > autorisation**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="40f6b-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="40f6b-149">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="40f6b-150">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="40f6b-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="40f6b-151">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="40f6b-151">Open the **API** list.</span></span>
1. <span data-ttu-id="40f6b-152">Activez l’accès à l’API (par exemple `API.Access`,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="40f6b-153">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="40f6b-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="40f6b-154">Sélectionnez le bouton **Grant admin content for {locataire Name}** .</span><span class="sxs-lookup"><span data-stu-id="40f6b-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="40f6b-155">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="40f6b-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="40f6b-156">Dans la **page** > d'**Azure ad B2C** > des**flux utilisateur**:</span><span class="sxs-lookup"><span data-stu-id="40f6b-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="40f6b-157">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="40f6b-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="40f6b-158">Au minimum, sélectionnez l’attribut utilisateur des **revendications** > d’application**nom complet** pour remplir `context.User.Identity.Name` le dans `LoginDisplay` le composant (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="40f6b-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="40f6b-159">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="40f6b-159">Record the following information:</span></span>

* <span data-ttu-id="40f6b-160">Enregistrez l’ID de l’application *cliente* (ID client) (par `33333333-3333-3333-3333-333333333333`exemple,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="40f6b-161">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple `B2C_1_signupsignin`,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="40f6b-162">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="40f6b-162">Create the app</span></span>

<span data-ttu-id="40f6b-163">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="40f6b-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="40f6b-164">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un `-o BlazorSample`chemin d’accès (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="40f6b-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="40f6b-165">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="40f6b-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="40f6b-166">Passez l’URI ID d’application à `app-id-uri` l’option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="40f6b-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="40f6b-167">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="40f6b-167">Server app configuration</span></span>

<span data-ttu-id="40f6b-168">*Cette section se rapporte à l’application **serveur** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="40f6b-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="40f6b-169">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="40f6b-169">Authentication package</span></span>

<span data-ttu-id="40f6b-170">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`Web est assurée par le :</span><span class="sxs-lookup"><span data-stu-id="40f6b-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="40f6b-171">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="40f6b-171">Authentication service support</span></span>

<span data-ttu-id="40f6b-172">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="40f6b-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="40f6b-173">La `AddAzureADB2CBearer` méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :</span><span class="sxs-lookup"><span data-stu-id="40f6b-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="40f6b-174">`UseAuthentication`et `UseAuthorization` Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="40f6b-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="40f6b-175">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="40f6b-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="40f6b-176">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="40f6b-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="40f6b-177">Utilisateur. Identity. Nomme</span><span class="sxs-lookup"><span data-stu-id="40f6b-177">User.Identity.Name</span></span>

<span data-ttu-id="40f6b-178">Par défaut, le `User.Identity.Name` n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="40f6b-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="40f6b-179">Pour configurer l’application afin qu’elle reçoive la `name` valeur du type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40f6b-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="40f6b-180">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="40f6b-180">App settings</span></span>

<span data-ttu-id="40f6b-181">Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="40f6b-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="40f6b-182">Exemple :</span><span class="sxs-lookup"><span data-stu-id="40f6b-182">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="40f6b-183">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="40f6b-183">WeatherForecast controller</span></span>

<span data-ttu-id="40f6b-184">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' `[Authorize]` attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="40f6b-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="40f6b-185">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="40f6b-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="40f6b-186">L' `[Authorize]` attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="40f6b-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="40f6b-187">L' `[Authorize]` attribut utilisé dans l' Blazor application webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="40f6b-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="40f6b-188">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="40f6b-188">Client app configuration</span></span>

<span data-ttu-id="40f6b-189">*Cette section se rapporte à l’application **cliente** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="40f6b-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="40f6b-190">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="40f6b-190">Authentication package</span></span>

<span data-ttu-id="40f6b-191">Quand une application est créée pour utiliser un compte B2C individuel (`IndividualB2C`), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="40f6b-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="40f6b-192">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="40f6b-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="40f6b-193">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="40f6b-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="40f6b-194">Remplacez `{VERSION}` dans la référence de package précédente par la version du `Microsoft.AspNetCore.Blazor.Templates` package présentée dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="40f6b-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="40f6b-195">Le `Microsoft.Authentication.WebAssembly.Msal` package ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package à l’application.</span><span class="sxs-lookup"><span data-stu-id="40f6b-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="40f6b-196">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="40f6b-196">Authentication service support</span></span>

<span data-ttu-id="40f6b-197">La prise `HttpClient` en charge des instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="40f6b-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="40f6b-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="40f6b-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="40f6b-199">La prise en charge de l’authentification des utilisateurs est inscrite `AddMsalAuthentication` dans le conteneur de service `Microsoft.Authentication.WebAssembly.Msal` avec la méthode d’extension fournie par le package.</span><span class="sxs-lookup"><span data-stu-id="40f6b-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="40f6b-200">Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="40f6b-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="40f6b-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="40f6b-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="40f6b-202">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="40f6b-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="40f6b-203">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="40f6b-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="40f6b-204">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="40f6b-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="40f6b-205">Exemple :</span><span class="sxs-lookup"><span data-stu-id="40f6b-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="40f6b-206">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="40f6b-206">Access token scopes</span></span>

<span data-ttu-id="40f6b-207">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="40f6b-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="40f6b-208">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="40f6b-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="40f6b-209">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="40f6b-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="40f6b-210">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="40f6b-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="40f6b-211">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="40f6b-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="40f6b-212">Si le Portail Azure fournit un URI d’étendue et **que l’application lève une exception non gérée** lorsqu’elle reçoit une réponse *non autorisée 401* de l’API, essayez d’utiliser un URI d’étendue qui n’inclut pas le schéma et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="40f6b-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="40f6b-213">Par exemple, le Portail Azure peut fournir l’un des formats d’URI d’étendue suivants :</span><span class="sxs-lookup"><span data-stu-id="40f6b-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="40f6b-214">Fournissez l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="40f6b-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="40f6b-215">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="40f6b-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="40f6b-216">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="40f6b-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="40f6b-217">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="40f6b-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="40f6b-218">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="40f6b-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="40f6b-219">Page d'index</span><span class="sxs-lookup"><span data-stu-id="40f6b-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="40f6b-220">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="40f6b-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="40f6b-221">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="40f6b-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="40f6b-222">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="40f6b-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="40f6b-223">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="40f6b-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="40f6b-224">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="40f6b-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="40f6b-225">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="40f6b-225">Run the app</span></span>

<span data-ttu-id="40f6b-226">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="40f6b-226">Run the app from the Server project.</span></span> <span data-ttu-id="40f6b-227">Quand vous utilisez Visual Studio, sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="40f6b-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="40f6b-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="40f6b-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="40f6b-229">Didacticiel : créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="40f6b-229">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="40f6b-230">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="40f6b-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
