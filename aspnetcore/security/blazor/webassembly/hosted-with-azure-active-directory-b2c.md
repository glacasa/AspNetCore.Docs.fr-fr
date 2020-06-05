---
title: Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b369bf0e9b20bcb87345e3e10c314ae6227464d1
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84215085"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="00c51-102">Sécuriser une Blazor application hébergée par l’ASP.net Core Webassembly avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="00c51-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="00c51-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00c51-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00c51-104">Cet article explique comment créer une Blazor application autonome Webassembly qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="00c51-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="00c51-105">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="00c51-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="00c51-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="00c51-106">Create a tenant</span></span>

<span data-ttu-id="00c51-107">Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="00c51-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="00c51-108">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="00c51-108">Record the following information:</span></span>

* <span data-ttu-id="00c51-109">AAD B2C instance (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale)</span><span class="sxs-lookup"><span data-stu-id="00c51-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="00c51-110">AAD B2C domaine de locataire (par exemple, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="00c51-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="00c51-111">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="00c51-111">Register a server API app</span></span>

<span data-ttu-id="00c51-112">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="00c51-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="00c51-113">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="00c51-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="00c51-114">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="00c51-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="00c51-115">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="00c51-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="00c51-116">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="00c51-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="00c51-117">Vérifiez que **Permissions**  >  **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="00c51-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="00c51-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="00c51-118">Select **Register**.</span></span>

<span data-ttu-id="00c51-119">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="00c51-119">Record the following information:</span></span>

* <span data-ttu-id="00c51-120">*Application API serveur* ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="00c51-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="00c51-121">ID de répertoire (ID de locataire) (par exemple, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="00c51-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="00c51-122">Domaine du locataire AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="00c51-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="00c51-123">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="00c51-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="00c51-124">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="00c51-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="00c51-125">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="00c51-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="00c51-126">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="00c51-127">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="00c51-128">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="00c51-129">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="00c51-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="00c51-130">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="00c51-130">Select **Add scope**.</span></span>

<span data-ttu-id="00c51-131">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="00c51-131">Record the following information:</span></span>

* <span data-ttu-id="00c51-132">URI ID d’application (par exemple,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="00c51-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="00c51-133">Étendue par défaut (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="00c51-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="00c51-134">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="00c51-134">Register a client app</span></span>

<span data-ttu-id="00c51-135">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *application cliente* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="00c51-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="00c51-136">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="00c51-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="00c51-137">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="00c51-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="00c51-138">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="00c51-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="00c51-139">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="00c51-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="00c51-140">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="00c51-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="00c51-141">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="00c51-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="00c51-142">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application serveur dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="00c51-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="00c51-143">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="00c51-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="00c51-144">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="00c51-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="00c51-145">Vérifiez que **Permissions**  >  **les autorisations accordent le droit administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="00c51-145">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="00c51-146">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="00c51-146">Select **Register**.</span></span>

<span data-ttu-id="00c51-147">Enregistrez l’ID d’application (ID client) (par exemple, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="00c51-148">Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="00c51-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="00c51-149">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="00c51-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="00c51-150">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="00c51-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="00c51-151">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="00c51-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="00c51-152">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="00c51-152">Select the **Save** button.</span></span>

<span data-ttu-id="00c51-153">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="00c51-153">In **API permissions**:</span></span>

1. <span data-ttu-id="00c51-154">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="00c51-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="00c51-155">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor serveur AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="00c51-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="00c51-156">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="00c51-156">Open the **API** list.</span></span>
1. <span data-ttu-id="00c51-157">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="00c51-158">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="00c51-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="00c51-159">Sélectionnez le bouton **Grant admin content for {locataire Name}** .</span><span class="sxs-lookup"><span data-stu-id="00c51-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="00c51-160">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="00c51-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="00c51-161">Dans la **page**d'  >  **Azure ad B2C**des  >  **flux utilisateur**:</span><span class="sxs-lookup"><span data-stu-id="00c51-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="00c51-162">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="00c51-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="00c51-163">Au minimum, sélectionnez l’attribut utilisateur des **revendications d’application**  >  **nom complet** pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="00c51-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="00c51-164">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="00c51-165">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="00c51-165">Create the app</span></span>

<span data-ttu-id="00c51-166">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="00c51-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="00c51-167">Pour spécifier l’emplacement de sortie, qui crée un dossier de projet s’il n’existe pas, incluez l’option de sortie dans la commande avec un chemin d’accès (par exemple, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="00c51-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="00c51-168">Le nom du dossier devient également une partie du nom du projet.</span><span class="sxs-lookup"><span data-stu-id="00c51-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="00c51-169">Passez l’URI ID d’application à l' `app-id-uri` option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="00c51-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="00c51-170">En outre, l’étendue configurée par le Blazor modèle hébergé peut avoir l’hôte URI ID d’application répété.</span><span class="sxs-lookup"><span data-stu-id="00c51-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="00c51-171">Confirmez que l’étendue configurée pour la `DefaultAccessTokenScopes` collection est correcte dans `Program.Main` (*Program.cs*) de l' *application cliente*.</span><span class="sxs-lookup"><span data-stu-id="00c51-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="00c51-172">Dans le portail Azure, l’URI de redirection Web des configurations de plateforme d’authentification *de l’application cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="00c51-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="00c51-173">Si l' *application cliente* est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="00c51-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="00c51-174">Si le port n’a pas été configuré précédemment avec le port connu *de l’application cliente* , revenez à l’inscription de l' *application cliente* dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="00c51-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="00c51-175">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="00c51-175">Server app configuration</span></span>

<span data-ttu-id="00c51-176">*Cette section se rapporte à l’application **serveur** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="00c51-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="00c51-177">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="00c51-177">Authentication package</span></span>

<span data-ttu-id="00c51-178">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le package [Microsoft. AspNetCore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) :</span><span class="sxs-lookup"><span data-stu-id="00c51-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="00c51-179">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="00c51-179">Authentication service support</span></span>

<span data-ttu-id="00c51-180">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="00c51-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="00c51-181">La <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :</span><span class="sxs-lookup"><span data-stu-id="00c51-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="00c51-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="00c51-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="00c51-183">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="00c51-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="00c51-184">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="00c51-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="00c51-185">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="00c51-185">User.Identity.Name</span></span>

<span data-ttu-id="00c51-186">Par défaut, le `User.Identity.Name` n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="00c51-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="00c51-187">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="00c51-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="00c51-188">Paramètres d’application</span><span class="sxs-lookup"><span data-stu-id="00c51-188">App settings</span></span>

<span data-ttu-id="00c51-189">Le fichier *appSettings. JSON* contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="00c51-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="00c51-190">Exemple :</span><span class="sxs-lookup"><span data-stu-id="00c51-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="00c51-191">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="00c51-191">WeatherForecast controller</span></span>

<span data-ttu-id="00c51-192">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="00c51-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="00c51-193">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="00c51-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="00c51-194">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="00c51-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="00c51-195">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor application webassembly sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="00c51-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="00c51-196">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="00c51-196">Client app configuration</span></span>

<span data-ttu-id="00c51-197">*Cette section se rapporte à l’application **cliente** de la solution.*</span><span class="sxs-lookup"><span data-stu-id="00c51-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="00c51-198">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="00c51-198">Authentication package</span></span>

<span data-ttu-id="00c51-199">Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="00c51-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="00c51-200">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="00c51-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="00c51-201">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="00c51-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="00c51-202">Le package [Microsoft. Authentication. Webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ajoute transitivement le package [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) à l’application.</span><span class="sxs-lookup"><span data-stu-id="00c51-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="00c51-203">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="00c51-203">Authentication service support</span></span>

<span data-ttu-id="00c51-204">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="00c51-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="00c51-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="00c51-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="00c51-206">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le package [Microsoft. Authentication. webassembly. MSAL](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="00c51-206">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="00c51-207">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="00c51-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="00c51-208">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="00c51-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="00c51-209">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="00c51-209">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="00c51-210">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="00c51-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="00c51-211">La configuration est fournie par le fichier *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="00c51-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="00c51-212">Exemple :</span><span class="sxs-lookup"><span data-stu-id="00c51-212">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="00c51-213">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="00c51-213">Access token scopes</span></span>

<span data-ttu-id="00c51-214">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="00c51-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="00c51-215">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="00c51-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="00c51-216">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="00c51-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="00c51-217">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="00c51-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="00c51-218">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="00c51-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="00c51-219">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="00c51-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="00c51-220">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="00c51-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="00c51-221">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="00c51-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="00c51-222">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="00c51-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="00c51-223">Page d'index</span><span class="sxs-lookup"><span data-stu-id="00c51-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="00c51-224">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="00c51-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="00c51-225">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="00c51-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="00c51-226">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="00c51-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="00c51-227">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="00c51-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="00c51-228">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="00c51-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="00c51-229">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="00c51-229">Run the app</span></span>

<span data-ttu-id="00c51-230">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="00c51-230">Run the app from the Server project.</span></span> <span data-ttu-id="00c51-231">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="00c51-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="00c51-232">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="00c51-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="00c51-233">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="00c51-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="00c51-234">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="00c51-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="00c51-235">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="00c51-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="00c51-236">Didacticiel : créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="00c51-236">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="00c51-237">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="00c51-237">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
