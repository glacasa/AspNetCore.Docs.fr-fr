---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: d2732819dd8f18da1f99965bb91e5eb3670ff4db
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445188"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="7ffa9-102">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7ffa9-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="7ffa9-103">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ffa9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ffa9-104">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="7ffa9-105">Inscrire des applications dans AAD et créer une solution</span><span class="sxs-lookup"><span data-stu-id="7ffa9-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="7ffa9-106">Créer un client</span><span class="sxs-lookup"><span data-stu-id="7ffa9-106">Create a tenant</span></span>

<span data-ttu-id="7ffa9-107">Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="7ffa9-108">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="7ffa9-108">Register a server API app</span></span>

<span data-ttu-id="7ffa9-109">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="7ffa9-110">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7ffa9-111">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7ffa9-112">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="7ffa9-113">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7ffa9-114">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="7ffa9-115">Désactivez **la case**  >  à cocher**accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7ffa9-116">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-116">Select **Register**.</span></span>

<span data-ttu-id="7ffa9-117">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-117">Record the following information:</span></span>

* <span data-ttu-id="7ffa9-118">*Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="7ffa9-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="7ffa9-119">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="7ffa9-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="7ffa9-120">Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="7ffa9-121">Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph**  >  **User. Read** , car l’application ne nécessite pas d’accès de connexion ou de profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="7ffa9-122">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="7ffa9-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="7ffa9-123">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="7ffa9-124">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="7ffa9-125">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7ffa9-126">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="7ffa9-127">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="7ffa9-128">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="7ffa9-129">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-129">Select **Add scope**.</span></span>

<span data-ttu-id="7ffa9-130">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-130">Record the following information:</span></span>

* <span data-ttu-id="7ffa9-131">URI ID d’application (par exemple,, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="7ffa9-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="7ffa9-132">Étendue par défaut (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="7ffa9-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="7ffa9-133">L’URI ID d’application peut nécessiter une configuration spéciale dans l’application cliente, qui est décrite dans la section [étendues de jetons d’accès](#access-token-scopes) plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="7ffa9-134">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="7ffa9-134">Register a client app</span></span>

<span data-ttu-id="7ffa9-135">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application cliente* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="7ffa9-136">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7ffa9-137">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="7ffa9-138">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="7ffa9-139">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7ffa9-140">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7ffa9-141">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7ffa9-142">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7ffa9-143">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application serveur dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7ffa9-144">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7ffa9-145">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7ffa9-146">Désactivez **la case**  >  à cocher**accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7ffa9-147">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-147">Select **Register**.</span></span>

<span data-ttu-id="7ffa9-148">Enregistrez l’ID de l’application *cliente* (client) (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="7ffa9-149">Dans **Authentication**le  >  **Platform configurations**  >  **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7ffa9-150">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7ffa9-151">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7ffa9-152">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7ffa9-153">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-153">Select the **Save** button.</span></span>

<span data-ttu-id="7ffa9-154">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="7ffa9-154">In **API permissions**:</span></span>

1. <span data-ttu-id="7ffa9-155">Vérifiez que l’application a **Microsoft Graph**  >  autorisation**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="7ffa9-156">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="7ffa9-157">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7ffa9-158">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-158">Open the **API** list.</span></span>
1. <span data-ttu-id="7ffa9-159">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7ffa9-160">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="7ffa9-161">Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="7ffa9-162">Sélectionnez **Oui** pour confirmer l’opération.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="7ffa9-163">Créer l'application</span><span class="sxs-lookup"><span data-stu-id="7ffa9-163">Create the app</span></span>

<span data-ttu-id="7ffa9-164">Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="7ffa9-165">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="7ffa9-165">Placeholder</span></span>                  | <span data-ttu-id="7ffa9-166">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="7ffa9-166">Azure portal name</span></span>                                     | <span data-ttu-id="7ffa9-167">Exemple</span><span class="sxs-lookup"><span data-stu-id="7ffa9-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="7ffa9-168">ID de l’application (client) pour l' *application cliente*</span><span class="sxs-lookup"><span data-stu-id="7ffa9-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="7ffa9-169">Nom de l’étendue</span><span class="sxs-lookup"><span data-stu-id="7ffa9-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="7ffa9-170">ID de l’application (client) pour l’application *API serveur*</span><span class="sxs-lookup"><span data-stu-id="7ffa9-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="7ffa9-171">URI d’ID d’application ([Voir la remarque](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="7ffa9-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="7ffa9-172">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="7ffa9-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="7ffa9-173">ID de l’annuaire (locataire)</span><span class="sxs-lookup"><span data-stu-id="7ffa9-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="7ffa9-174">L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7ffa9-175">Passez l’URI ID d’application à l' `app-id-uri` option, mais notez qu’une modification de configuration peut être nécessaire dans l’application cliente, qui est décrite dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="7ffa9-176">Dans le portail Azure, l’URI de redirection Web des configurations de plateforme d’authentification *de l’application cliente* **Authentication**  >  **Platform configurations**  >  **Web**  >  **Redirect URI** est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7ffa9-177">Si l' *application cliente* est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7ffa9-178">Si le port n’a pas été configuré précédemment avec le port connu *de l’application cliente* , revenez à l’inscription de l' *application cliente* dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7ffa9-179">Configuration de l’application serveur</span><span class="sxs-lookup"><span data-stu-id="7ffa9-179">Server app configuration</span></span>

<span data-ttu-id="7ffa9-180">*Cette section se rapporte à l’application de la solution **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="7ffa9-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7ffa9-181">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7ffa9-181">Authentication package</span></span>

<span data-ttu-id="7ffa9-182">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) Package :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="7ffa9-183">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="7ffa9-183">Authentication service support</span></span>

<span data-ttu-id="7ffa9-184">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="7ffa9-185">La <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="7ffa9-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="7ffa9-187">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="7ffa9-188">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="7ffa9-189">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="7ffa9-189">User.Identity.Name</span></span>

<span data-ttu-id="7ffa9-190">Par défaut, l’API d’application serveur est renseignée `User.Identity.Name` avec la valeur du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` type de revendication (par exemple, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="7ffa9-191">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="7ffa9-192">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="7ffa9-192">App settings</span></span>

<span data-ttu-id="7ffa9-193">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="7ffa9-194">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-194">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="7ffa9-195">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="7ffa9-195">WeatherForecast controller</span></span>

<span data-ttu-id="7ffa9-196">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="7ffa9-197">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="7ffa9-198">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="7ffa9-199">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="7ffa9-200">Configuration de l’application cliente</span><span class="sxs-lookup"><span data-stu-id="7ffa9-200">Client app configuration</span></span>

<span data-ttu-id="7ffa9-201">*Cette section se rapporte à l’application de la solution **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="7ffa9-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7ffa9-202">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7ffa9-202">Authentication package</span></span>

<span data-ttu-id="7ffa9-203">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="7ffa9-204">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7ffa9-205">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7ffa9-206">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="7ffa9-207">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="7ffa9-207">Authentication service support</span></span>

<span data-ttu-id="7ffa9-208">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="7ffa9-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="7ffa9-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="7ffa9-210">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="7ffa9-211">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) Package.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="7ffa9-212">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="7ffa9-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7ffa9-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="7ffa9-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="7ffa9-214">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7ffa9-215">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="7ffa9-216">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="7ffa9-217">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="7ffa9-218">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7ffa9-218">Access token scopes</span></span>

<span data-ttu-id="7ffa9-219">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="7ffa9-220">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="7ffa9-221">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="7ffa9-222">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="7ffa9-223">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7ffa9-224">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7ffa9-225">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ffa9-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7ffa9-226">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="7ffa9-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="7ffa9-227">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="7ffa9-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7ffa9-228">Page d'index</span><span class="sxs-lookup"><span data-stu-id="7ffa9-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="7ffa9-229">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="7ffa9-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7ffa9-230">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7ffa9-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7ffa9-231">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7ffa9-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="7ffa9-232">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="7ffa9-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7ffa9-233">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="7ffa9-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7ffa9-234">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="7ffa9-234">Run the app</span></span>

<span data-ttu-id="7ffa9-235">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="7ffa9-235">Run the app from the Server project.</span></span> <span data-ttu-id="7ffa9-236">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="7ffa9-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7ffa9-237">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7ffa9-238">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="7ffa9-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7ffa9-239">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ffa9-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7ffa9-240">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="7ffa9-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7ffa9-241">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="7ffa9-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
