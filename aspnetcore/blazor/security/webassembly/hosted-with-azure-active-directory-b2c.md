---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: dd7b7881ac44f8e80d2b32617594bca259fe08bc
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202777"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e252a-102">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e252a-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e252a-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e252a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e252a-104">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="e252a-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e252a-105">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="e252a-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e252a-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="e252a-106">Create a tenant</span></span>

<span data-ttu-id="e252a-107">Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="e252a-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="e252a-108">Enregistrez l’instance de AAD B2C (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale).</span><span class="sxs-lookup"><span data-stu-id="e252a-108">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="e252a-109">L’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="e252a-109">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e252a-110">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="e252a-110">Register a server API app</span></span>

<span data-ttu-id="e252a-111">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="e252a-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="e252a-112">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="e252a-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="e252a-113">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e252a-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e252a-114">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e252a-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="e252a-115">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="e252a-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e252a-116">Vérifiez que **Permissions**  >  **les autorisations accordent le consentement de l’administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="e252a-116">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e252a-117">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="e252a-117">Select **Register**.</span></span>

<span data-ttu-id="e252a-118">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="e252a-118">Record the following information:</span></span>

* <span data-ttu-id="e252a-119">*Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="e252a-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="e252a-120">Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="e252a-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="e252a-121">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="e252a-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="e252a-122">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="e252a-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e252a-123">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="e252a-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e252a-124">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e252a-125">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e252a-126">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e252a-127">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="e252a-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e252a-128">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="e252a-128">Select **Add scope**.</span></span>

<span data-ttu-id="e252a-129">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="e252a-129">Record the following information:</span></span>

* <span data-ttu-id="e252a-130">URI ID d’application (par exemple,, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="e252a-130">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e252a-131">Étendue par défaut (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="e252a-131">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="e252a-132">L’URI ID d’application peut nécessiter une configuration spéciale dans l’application cliente, qui est décrite dans la section [étendues de jetons d’accès](#access-token-scopes) plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e252a-132">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e252a-133">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="e252a-133">Register a client app</span></span>

<span data-ttu-id="e252a-134">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *application cliente* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="e252a-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="e252a-135">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="e252a-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="e252a-136">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e252a-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e252a-137">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e252a-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="e252a-138">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="e252a-138">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e252a-139">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="e252a-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e252a-140">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="e252a-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e252a-141">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application serveur dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="e252a-141">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e252a-142">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="e252a-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e252a-143">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="e252a-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e252a-144">Vérifiez que **Permissions**  >  **les autorisations accordent le consentement de l’administrateur à OpenID et que les autorisations offline_access** sont activées.</span><span class="sxs-lookup"><span data-stu-id="e252a-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e252a-145">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="e252a-145">Select **Register**.</span></span>

<span data-ttu-id="e252a-146">Enregistrez l’ID de l’application (client) (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="e252a-147">Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="e252a-147">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e252a-148">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="e252a-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e252a-149">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="e252a-149">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e252a-150">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="e252a-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e252a-151">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="e252a-151">Select the **Save** button.</span></span>

<span data-ttu-id="e252a-152">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="e252a-152">In **API permissions**:</span></span>

1. <span data-ttu-id="e252a-153">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="e252a-153">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e252a-154">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e252a-154">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e252a-155">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="e252a-155">Open the **API** list.</span></span>
1. <span data-ttu-id="e252a-156">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-156">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e252a-157">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="e252a-157">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e252a-158">Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** .</span><span class="sxs-lookup"><span data-stu-id="e252a-158">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="e252a-159">Sélectionnez **Oui** pour confirmer l’opération.</span><span class="sxs-lookup"><span data-stu-id="e252a-159">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e252a-160">Dans la **page**d'  >  **Azure ad B2C**des  >  **flux utilisateur**:</span><span class="sxs-lookup"><span data-stu-id="e252a-160">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e252a-161">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="e252a-161">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e252a-162">Au minimum, sélectionnez l' **Application claims**  >  attribut utilisateur**nom complet** des revendications d’application pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-162">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="e252a-163">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e252a-164">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="e252a-164">Create the app</span></span>

<span data-ttu-id="e252a-165">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e252a-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="e252a-166">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="e252a-166">Placeholder</span></span>                   | <span data-ttu-id="e252a-167">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="e252a-167">Azure portal name</span></span>                                     | <span data-ttu-id="e252a-168">Exemple</span><span class="sxs-lookup"><span data-stu-id="e252a-168">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="e252a-169">Instance</span><span class="sxs-lookup"><span data-stu-id="e252a-169">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="e252a-170">ID de l’application (client) pour l' *application cliente*</span><span class="sxs-lookup"><span data-stu-id="e252a-170">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="e252a-171">Nom de l’étendue</span><span class="sxs-lookup"><span data-stu-id="e252a-171">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="e252a-172">ID de l’application (client) pour l’application *API serveur*</span><span class="sxs-lookup"><span data-stu-id="e252a-172">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="e252a-173">URI d’ID d’application ([Voir la remarque](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="e252a-173">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="e252a-174">Workflow d’inscription/de connexion</span><span class="sxs-lookup"><span data-stu-id="e252a-174">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="e252a-175">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="e252a-175">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="e252a-176">L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="e252a-176">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e252a-177">Passez l’URI ID d’application à l' `app-id-uri` option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="e252a-177">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="e252a-178">En outre, l’étendue configurée par le Blazor modèle hébergé peut avoir l’hôte URI ID d’application répété.</span><span class="sxs-lookup"><span data-stu-id="e252a-178">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="e252a-179">Vérifiez que l’étendue configurée pour la `DefaultAccessTokenScopes` collection est correcte dans `Program.Main` ( `Program.cs` ) de l' *application cliente*.</span><span class="sxs-lookup"><span data-stu-id="e252a-179">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="e252a-180">Dans le portail Azure, l’URI de redirection Web des configurations de plateforme d’authentification *de l’application cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="e252a-180">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e252a-181">Si l' *application cliente* est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="e252a-181">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e252a-182">Si le port n’a pas été configuré précédemment avec le port connu *de l’application cliente* , revenez à l’inscription de l' *application cliente* dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="e252a-182">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e252a-183">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="e252a-183">Server app configuration</span></span>

<span data-ttu-id="e252a-184">*Cette section se rapporte à l’application de la solution **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="e252a-184">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e252a-185">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="e252a-185">Authentication package</span></span>

<span data-ttu-id="e252a-186">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) Package :</span><span class="sxs-lookup"><span data-stu-id="e252a-186">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e252a-187">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="e252a-187">Authentication service support</span></span>

<span data-ttu-id="e252a-188">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="e252a-188">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e252a-189">La <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :</span><span class="sxs-lookup"><span data-stu-id="e252a-189">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="e252a-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="e252a-190"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="e252a-191">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="e252a-191">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e252a-192">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="e252a-192">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="e252a-193">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="e252a-193">User.Identity.Name</span></span>

<span data-ttu-id="e252a-194">Par défaut, le `User.Identity.Name` n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="e252a-194">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="e252a-195">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e252a-195">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e252a-196">Paramètres d’application</span><span class="sxs-lookup"><span data-stu-id="e252a-196">App settings</span></span>

<span data-ttu-id="e252a-197">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="e252a-197">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="e252a-198">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e252a-198">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="e252a-199">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e252a-199">WeatherForecast controller</span></span>

<span data-ttu-id="e252a-200">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="e252a-200">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="e252a-201">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="e252a-201">It's **important** to understand that:</span></span>

* <span data-ttu-id="e252a-202">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="e252a-202">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e252a-203">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="e252a-203">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e252a-204">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="e252a-204">Client app configuration</span></span>

<span data-ttu-id="e252a-205">*Cette section se rapporte à l’application de la solution **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="e252a-205">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e252a-206">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="e252a-206">Authentication package</span></span>

<span data-ttu-id="e252a-207">Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="e252a-207">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="e252a-208">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="e252a-208">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e252a-209">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="e252a-209">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="e252a-210">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="e252a-210">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e252a-211">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="e252a-211">Authentication service support</span></span>

<span data-ttu-id="e252a-212">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="e252a-212">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="e252a-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e252a-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="e252a-214">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="e252a-214">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="e252a-215">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) Package.</span><span class="sxs-lookup"><span data-stu-id="e252a-215">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="e252a-216">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="e252a-216">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e252a-217">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e252a-217">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e252a-218">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="e252a-218">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e252a-219">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="e252a-219">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="e252a-220">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="e252a-220">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="e252a-221">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e252a-221">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="e252a-222">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="e252a-222">Access token scopes</span></span>

<span data-ttu-id="e252a-223">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="e252a-223">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e252a-224">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="e252a-224">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e252a-225">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="e252a-225">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e252a-226">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="e252a-226">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e252a-227">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="e252a-227">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="e252a-228">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="e252a-228">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e252a-229">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e252a-229">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e252a-230">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="e252a-230">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="e252a-231">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="e252a-231">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="e252a-232">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="e252a-232">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e252a-233">Page d'index</span><span class="sxs-lookup"><span data-stu-id="e252a-233">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e252a-234">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="e252a-234">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e252a-235">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e252a-235">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e252a-236">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e252a-236">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e252a-237">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="e252a-237">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e252a-238">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="e252a-238">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e252a-239">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="e252a-239">Run the app</span></span>

<span data-ttu-id="e252a-240">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="e252a-240">Run the app from the Server project.</span></span> <span data-ttu-id="e252a-241">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="e252a-241">When using Visual Studio, either:</span></span>

* <span data-ttu-id="e252a-242">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="e252a-242">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="e252a-243">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="e252a-243">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e252a-244">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e252a-244">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e252a-245">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="e252a-245">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e252a-246">Tutoriel : Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e252a-246">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e252a-247">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="e252a-247">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
