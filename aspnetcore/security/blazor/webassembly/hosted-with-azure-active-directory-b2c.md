---
title: Sécurisez une Blazor application WebAssembly ASP.NET Core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123467"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="55af3-102">Sécurisez une Blazor application WebAssembly ASP.NET Core avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="55af3-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="55af3-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="55af3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="55af3-104">Cet article décrit comment Blazor créer une application autonome WebAssembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="55af3-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="55af3-105">Enregistrez les applications dans AAD B2C et créez une solution</span><span class="sxs-lookup"><span data-stu-id="55af3-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="55af3-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="55af3-106">Create a tenant</span></span>

<span data-ttu-id="55af3-107">Suivez les conseils dans [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C et enregistrer les informations suivantes:</span><span class="sxs-lookup"><span data-stu-id="55af3-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="55af3-108">AAD B2C instance (par exemple, , `https://contoso.b2clogin.com/`qui comprend la barre oblique de fuite)</span><span class="sxs-lookup"><span data-stu-id="55af3-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="55af3-109">Domaine AAD B2C Tenant `contoso.onmicrosoft.com`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="55af3-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="55af3-110">Enregistrez une application API serveur</span><span class="sxs-lookup"><span data-stu-id="55af3-110">Register a server API app</span></span>

<span data-ttu-id="55af3-111">Suivez les conseils dans [Tutorial: Enregistrez une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour enregistrer une application AAD pour *l’application Server API* dans la zone **Azure Active Directory** > **App d’enregistrement** du portail Azure :</span><span class="sxs-lookup"><span data-stu-id="55af3-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="55af3-112">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="55af3-112">Select **New registration**.</span></span>
1. <span data-ttu-id="55af3-113">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="55af3-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="55af3-114">Pour **les types de compte pris en**charge, sélectionnez des comptes dans **n’importe quel répertoire organisationnel ou tout fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="55af3-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="55af3-115">(multi-locataire) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="55af3-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="55af3-116">*L’application Server API* ne nécessite pas **d’URI Redirect dans** ce scénario, alors laissez la baisse vers le **Web** et n’entrez pas dans une URI rediriger.</span><span class="sxs-lookup"><span data-stu-id="55af3-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="55af3-117">Confirmez que **Les autorisations** > **Grant admin concent à openid et offline_access autorisations sont activées.**</span><span class="sxs-lookup"><span data-stu-id="55af3-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="55af3-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="55af3-118">Select **Register**.</span></span>

<span data-ttu-id="55af3-119">Dans **Expose une API**:</span><span class="sxs-lookup"><span data-stu-id="55af3-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="55af3-120">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="55af3-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="55af3-121">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="55af3-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="55af3-122">Fournir un **nom scope** `API.Access`(par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="55af3-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="55af3-123">Fournir un **nom d’affichage de consentement d’administration** (par exemple, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="55af3-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="55af3-124">Fournir une description du consentement `Allows the app to access server app API endpoints.`de **l’administration** (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="55af3-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="55af3-125">Confirmez que **l’Etat** est prêt à **Enabled**.</span><span class="sxs-lookup"><span data-stu-id="55af3-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="55af3-126">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="55af3-126">Select **Add scope**.</span></span>

<span data-ttu-id="55af3-127">Enregistrez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="55af3-127">Record the following information:</span></span>

* <span data-ttu-id="55af3-128">*Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="55af3-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="55af3-129">App ID URI (par exemple, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="55af3-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="55af3-130">Id d’annuaire (Id locataire) `222222222-2222-2222-2222-222222222222`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="55af3-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="55af3-131">*Application API serveur* App ID URI (par exemple, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`le portail Azure peut par défaut la valeur de l’ID client)</span><span class="sxs-lookup"><span data-stu-id="55af3-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="55af3-132">Portée par défaut `API.Access`(par exemple, )</span><span class="sxs-lookup"><span data-stu-id="55af3-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="55af3-133">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="55af3-133">Register a client app</span></span>

<span data-ttu-id="55af3-134">Suivez les conseils dans [Tutorial: Enregistrez une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour enregistrer une application AAD pour *l’application Client* dans la zone **Azure Active Directory** > **App d’enregistrement** du portail Azure:</span><span class="sxs-lookup"><span data-stu-id="55af3-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="55af3-135">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="55af3-135">Select **New registration**.</span></span>
1. <span data-ttu-id="55af3-136">Fournir un **nom** pour l’application (par exemple, \*\* Blazor Client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="55af3-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="55af3-137">Pour **les types de compte pris en**charge, sélectionnez des comptes dans **n’importe quel répertoire organisationnel ou tout fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="55af3-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="55af3-138">(multi-locataire) pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="55af3-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="55af3-139">Laissez la **redirection URI** déposer vers le bas `https://localhost:5001/authentication/login-callback`fixé sur le **Web**, et de fournir une redirection URI de .</span><span class="sxs-lookup"><span data-stu-id="55af3-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="55af3-140">Confirmez que **Les autorisations** > **Grant admin concent à openid et offline_access autorisations sont activées.**</span><span class="sxs-lookup"><span data-stu-id="55af3-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="55af3-141">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="55af3-141">Select **Register**.</span></span>

<span data-ttu-id="55af3-142">Dans les > **configurations de plate-forme** **d’authentification** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="55af3-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="55af3-143">Confirmez **l’URI Redirect** de `https://localhost:5001/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="55af3-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="55af3-144">Pour **la subvention implicite**, sélectionnez les cases à cocher pour les **jetons d’accès** et **les jetons d’identité**.</span><span class="sxs-lookup"><span data-stu-id="55af3-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="55af3-145">Les autres défauts de paiement de l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="55af3-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="55af3-146">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="55af3-146">Select the **Save** button.</span></span>

<span data-ttu-id="55af3-147">Dans **les autorisations de l’API**:</span><span class="sxs-lookup"><span data-stu-id="55af3-147">In **API permissions**:</span></span>

1. <span data-ttu-id="55af3-148">Confirmez que l’application dispose de l’autorisation **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="55af3-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="55af3-149">Sélectionnez **Ajouter une permission** suivie par Mes **API**.</span><span class="sxs-lookup"><span data-stu-id="55af3-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="55af3-150">Sélectionnez *l’application Server API* à partir de la colonne **Nom** (par exemple, \*\* Blazor Serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="55af3-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="55af3-151">Ouvrez la liste **API.**</span><span class="sxs-lookup"><span data-stu-id="55af3-151">Open the **API** list.</span></span>
1. <span data-ttu-id="55af3-152">Activer l’accès à l’API (par exemple, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="55af3-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="55af3-153">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="55af3-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="55af3-154">Sélectionnez le **contenu de l’administrateur Grant pour le bouton 'TENANT NAME'.**</span><span class="sxs-lookup"><span data-stu-id="55af3-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="55af3-155">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="55af3-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="55af3-156">Dans **Home** > **Azure AD B2C** > **Flux d’utilisateurs**:</span><span class="sxs-lookup"><span data-stu-id="55af3-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="55af3-157">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="55af3-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="55af3-158">Au minimum, sélectionnez **l’attribut** > utilisateur**de l’utilisateur Display Name** des revendications d’application pour remplir le `context.User.Identity.Name` `LoginDisplay` composant *(Shared/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="55af3-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="55af3-159">Enregistrez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="55af3-159">Record the following information:</span></span>

* <span data-ttu-id="55af3-160">Enregistrez l’ID *d’application* de l’application Client (IDENTIFIANT client) (par exemple, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="55af3-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="55af3-161">Enregistrez le nom de flux d’utilisateur d’inscription et `B2C_1_signupsignin`d’inscription créé pour l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="55af3-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="55af3-162">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="55af3-162">Create the app</span></span>

<span data-ttu-id="55af3-163">Remplacez les titulaires de place dans la commande suivante par les informations enregistrées plus tôt et exécutez la commande dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="55af3-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="55af3-164">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, inclure l’option de sortie dans la commande avec un chemin (par exemple, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="55af3-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="55af3-165">Le nom du dossier fait également partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="55af3-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="55af3-166">Passez l’App ID URI à l’option, `app-id-uri` mais notez qu’un changement de configuration peut être nécessaire dans l’application client, qui est décrite dans la section portée des [jetons d’accès.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="55af3-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="55af3-167">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="55af3-167">Server app configuration</span></span>

<span data-ttu-id="55af3-168">*Cette section concerne l’application **Server** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="55af3-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="55af3-169">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="55af3-169">Authentication package</span></span>

<span data-ttu-id="55af3-170">Le support pour l’authentification et l’autorisation des appels `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`à ASP.NET API Web de base est fourni par le :</span><span class="sxs-lookup"><span data-stu-id="55af3-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="55af3-171">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="55af3-171">Authentication service support</span></span>

<span data-ttu-id="55af3-172">La `AddAuthentication` méthode configure les services d’authentification dans l’application et configure le gestionnaire JWT Bearer comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="55af3-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="55af3-173">La `AddAzureADB2CBearer` méthode définit les paramètres spécifiques du gestionnaire JWT Bearer nécessaires pour valider les jetons émis par l’Annuaire actif Azure B2C :</span><span class="sxs-lookup"><span data-stu-id="55af3-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="55af3-174">`UseAuthentication`et `UseAuthorization` s’assurer que :</span><span class="sxs-lookup"><span data-stu-id="55af3-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="55af3-175">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="55af3-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="55af3-176">Toute demande visant à accéder à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="55af3-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="55af3-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="55af3-177">User.Identity.Name</span></span>

<span data-ttu-id="55af3-178">Par défaut, `User.Identity.Name` le n’est pas peuplé.</span><span class="sxs-lookup"><span data-stu-id="55af3-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="55af3-179">Pour configurer l’application pour `name` recevoir la valeur du type de réclamation, configurez le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55af3-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="55af3-180">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="55af3-180">App settings</span></span>

<span data-ttu-id="55af3-181">Le fichier *appsettings.json* contient les options pour configurer le gestionnaire de porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="55af3-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="55af3-182">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="55af3-182">WeatherForecast controller</span></span>

<span data-ttu-id="55af3-183">Le contrôleur WeatherForecast *(Controllers/WeatherForecastController.cs*) expose une `[Authorize]` API protégée avec l’attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="55af3-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="55af3-184">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="55af3-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="55af3-185">L’attribut `[Authorize]` dans ce contrôleur API est la seule chose qui protège cette API contre l’accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="55af3-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="55af3-186">L’attribut `[Authorize]` utilisé Blazor dans l’application WebAssembly ne sert qu’à indicer à l’application que l’utilisateur doit être autorisé à travailler correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="55af3-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="55af3-187">Configuration de l’application client</span><span class="sxs-lookup"><span data-stu-id="55af3-187">Client app configuration</span></span>

<span data-ttu-id="55af3-188">*Cette section concerne l’application **Client** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="55af3-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="55af3-189">Forfait d’authentification</span><span class="sxs-lookup"><span data-stu-id="55af3-189">Authentication package</span></span>

<span data-ttu-id="55af3-190">Lorsqu’une application est créée pour utiliser`IndividualB2C`un compte B2C individuel ( ), l’application reçoit automatiquement une référence de paquet pour la [bibliothèque Microsoft Authentication](/azure/active-directory/develop/msal-overview) ().`Microsoft.Authentication.WebAssembly.Msal`</span><span class="sxs-lookup"><span data-stu-id="55af3-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="55af3-191">Le paquet fournit un ensemble de primitifs qui aident l’application authentifier les utilisateurs et obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="55af3-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="55af3-192">Si vous ajoutez de l’authentification à une application, ajoutez manuellement le paquet au fichier de projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="55af3-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="55af3-193">Remplacez `{VERSION}` dans la référence du `Microsoft.AspNetCore.Blazor.Templates` paquet précédent <xref:blazor/get-started> avec la version du paquet indiquée dans l’article.</span><span class="sxs-lookup"><span data-stu-id="55af3-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="55af3-194">Le `Microsoft.Authentication.WebAssembly.Msal` paquet ajoute transitivement le `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquet à l’application.</span><span class="sxs-lookup"><span data-stu-id="55af3-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="55af3-195">Support de service d’authentification</span><span class="sxs-lookup"><span data-stu-id="55af3-195">Authentication service support</span></span>

<span data-ttu-id="55af3-196">La prise en charge de l’authentification des utilisateurs est enregistrée dans le conteneur de service avec la `AddMsalAuthentication` méthode d’extension fournie par le `Microsoft.Authentication.WebAssembly.Msal` paquet.</span><span class="sxs-lookup"><span data-stu-id="55af3-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="55af3-197">Cette méthode met en place tous les services requis pour que l’application interagit avec le fournisseur d’identité (IP).</span><span class="sxs-lookup"><span data-stu-id="55af3-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="55af3-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="55af3-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="55af3-199">La `AddMsalAuthentication` méthode accepte un rappel pour configurer les paramètres nécessaires pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="55af3-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="55af3-200">Les valeurs requises pour configurer l’application peuvent être obtenues à partir de la configuration Azure Portal AAD lorsque vous enregistrez l’application.</span><span class="sxs-lookup"><span data-stu-id="55af3-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="55af3-201">Portée symbolique d’accès</span><span class="sxs-lookup"><span data-stu-id="55af3-201">Access token scopes</span></span>

<span data-ttu-id="55af3-202">Les portées symboliques d’accès par défaut représentent la liste des portées symboliques d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="55af3-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="55af3-203">Inclus par défaut dans le signe en demande.</span><span class="sxs-lookup"><span data-stu-id="55af3-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="55af3-204">Utilisé pour fournir un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="55af3-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="55af3-205">Toutes les portées doivent appartenir à la même application par les règles Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="55af3-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="55af3-206">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API au besoin :</span><span class="sxs-lookup"><span data-stu-id="55af3-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="55af3-207">Si le portail Azure fournit une portée URI et **que l’application jette une exception non gérée** lorsqu’elle reçoit une réponse non autorisée *401* de l’API, essayez d’utiliser une portée URI qui n’inclut pas le système et l’hôte.</span><span class="sxs-lookup"><span data-stu-id="55af3-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="55af3-208">Par exemple, le portail Azure peut fournir l’un des formats URI de portée suivants :</span><span class="sxs-lookup"><span data-stu-id="55af3-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="55af3-209">Fournir la portée URI sans le régime et l’hôte:</span><span class="sxs-lookup"><span data-stu-id="55af3-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="55af3-210">Pour plus d’informations, consultez <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="55af3-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="55af3-211">Fichier d’importations</span><span class="sxs-lookup"><span data-stu-id="55af3-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="55af3-212">Page d'index</span><span class="sxs-lookup"><span data-stu-id="55af3-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="55af3-213">Composant de l’application</span><span class="sxs-lookup"><span data-stu-id="55af3-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="55af3-214">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="55af3-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="55af3-215">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="55af3-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="55af3-216">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="55af3-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="55af3-217">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="55af3-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="55af3-218">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="55af3-218">Run the app</span></span>

<span data-ttu-id="55af3-219">Exécutez l’application à partir du projet Server.</span><span class="sxs-lookup"><span data-stu-id="55af3-219">Run the app from the Server project.</span></span> <span data-ttu-id="55af3-220">Lorsque vous utilisez Visual Studio, sélectionnez le projet Server dans **Solution Explorer** et sélectionnez le bouton **Run** dans la barre d’outils ou démarrez l’application à partir du menu **Debug.**</span><span class="sxs-lookup"><span data-stu-id="55af3-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="55af3-221">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="55af3-221">Additional resources</span></span>

* [<span data-ttu-id="55af3-222">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="55af3-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="55af3-223">Didacticiel : créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="55af3-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="55af3-224">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="55af3-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
