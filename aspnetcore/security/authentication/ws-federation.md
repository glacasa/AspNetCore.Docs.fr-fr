---
title: Authentifier les utilisateurs avec WS-Federation dans ASP.NET Core
author: chlowell
description: Ce didacticiel montre comment utiliser WS-Federation dans une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: fede3887ad7dacd40cf3bb5d1b785392a9bc1480
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850459"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="4b7d0-103">Authentifier les utilisateurs avec WS-Federation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b7d0-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="4b7d0-104">Ce didacticiel montre comment permettre aux utilisateurs de se connecter avec un fournisseur d’authentification WS-Federation comme Services ADFS (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="4b7d0-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="4b7d0-105">Il utilise l’exemple d’application ASP.NET Core décrit dans [Facebook, Google et l’authentification du fournisseur externe](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="4b7d0-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="4b7d0-106">Pour les applications ASP.NET Core, la prise en charge WS-Federation est fournie par [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="4b7d0-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="4b7d0-107">Ce composant est porté à partir de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) et partage un grand nombre des mécanismes de ce composant.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="4b7d0-108">Toutefois, les composants diffèrent de deux manières importantes.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="4b7d0-109">Par défaut, le nouveau Middleware :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-109">By default, the new middleware:</span></span>

* <span data-ttu-id="4b7d0-110">N’autorise pas les connexions non sollicitées.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="4b7d0-111">Cette fonctionnalité du protocole WS-Federation est vulnérable aux attaques XSRF.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="4b7d0-112">Toutefois, il peut être activé à l' `AllowUnsolicitedLogins` aide de l’option.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="4b7d0-113">Ne vérifie pas les messages de connexion dans chaque publication de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="4b7d0-114">Seules les demandes à `CallbackPath` sont vérifiées pour les connexions. `CallbackPath` la valeur par `/signin-wsfed` défaut est, mais elle peut être modifiée via la propriété [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) héritée de la classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="4b7d0-115">Ce chemin d’accès peut être partagé avec d’autres fournisseurs d’authentification en activant l’option [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="4b7d0-116">Inscrire l’application auprès de Active Directory</span><span class="sxs-lookup"><span data-stu-id="4b7d0-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="4b7d0-117">Services AD FS (Active Directory Federation Services)</span><span class="sxs-lookup"><span data-stu-id="4b7d0-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="4b7d0-118">Ouvrez l' **Assistant Ajout d’approbation de partie de confiance** à partir de la console de gestion ADFS :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Assistant Ajout d’approbation de partie de confiance : Bienvenue](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="4b7d0-120">Choisissez d’entrer les données manuellement :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-120">Choose to enter data manually:</span></span>

![Assistant Ajout d’approbation de partie de confiance : sélectionner une source de données](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="4b7d0-122">Entrez un nom d’affichage pour la partie de confiance.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="4b7d0-123">Le nom n’est pas important pour l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="4b7d0-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ne prend pas en charge le chiffrement de jeton. par conséquent, ne configurez pas un certificat de chiffrement de jeton :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Assistant Ajout d’approbation de partie de confiance : configurer le certificat](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="4b7d0-126">Activez la prise en charge du protocole WS-Federation passif, à l’aide de l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="4b7d0-127">Vérifiez que le port est correct pour l’application :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-127">Verify the port is correct for the app:</span></span>

![Assistant Ajout d’approbation de partie de confiance : configurer l’URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="4b7d0-129">Il doit s’agir d’une URL HTTPs.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="4b7d0-130">IIS Express pouvez fournir un certificat auto-signé lors de l’hébergement de l’application pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="4b7d0-131">Kestrel requiert une configuration manuelle des certificats.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="4b7d0-132">Pour plus d’informations, consultez la [documentation de Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="4b7d0-133">Cliquez sur **suivant** dans le reste de l’Assistant et **Fermez** à la fin.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="4b7d0-134">ASP.NET Core Identity requiert une revendication d' **ID de nom** .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="4b7d0-135">Ajoutez-en une à partir de la boîte de dialogue **modifier les règles de revendication** :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Modifier les règles de revendication](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="4b7d0-137">Dans l' **Assistant Ajouter une règle de revendication de transformation**, laissez le modèle par défaut **Envoyer les attributs LDAP en tant que revendications** sélectionné, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="4b7d0-138">Ajoutez une règle mappant l’attribut **Sam-Account-Name** LDAP à la revendication de **nom ID** sortante :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Assistant Ajout de règle de revendication de transformation : configurer la règle de revendication](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="4b7d0-140">Cliquez sur **Terminer** > **OK** dans la fenêtre **modifier les règles de revendication** .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="4b7d0-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="4b7d0-141">Azure Active Directory</span></span>

* <span data-ttu-id="4b7d0-142">Accédez au panneau inscriptions des applications du locataire AAD.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="4b7d0-143">Cliquez sur **nouvelle inscription d’application**:</span><span class="sxs-lookup"><span data-stu-id="4b7d0-143">Click **New application registration**:</span></span>

![Azure Active Directory : inscriptions d’applications](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="4b7d0-145">Entrez un nom pour l’inscription de l’application.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-145">Enter a name for the app registration.</span></span> <span data-ttu-id="4b7d0-146">Cela n’est pas important pour l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="4b7d0-147">Entrez l’URL d’écoute de l’application en tant qu' **URL de connexion**:</span><span class="sxs-lookup"><span data-stu-id="4b7d0-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory : créer l’inscription de l’application](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="4b7d0-149">Cliquez sur **points de terminaison** et notez l’URL du document de **métadonnées de Fédération** .</span><span class="sxs-lookup"><span data-stu-id="4b7d0-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="4b7d0-150">Il s’agit de `MetadataAddress`l’intergiciel (middleware) WS-Federation :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory : points de terminaison](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="4b7d0-152">Accédez à la nouvelle inscription d’application.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-152">Navigate to the new app registration.</span></span> <span data-ttu-id="4b7d0-153">Cliquez sur**Propriétés** des **paramètres** > et prenez note de l' **URI ID d’application**.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="4b7d0-154">Il s’agit de `Wtrealm`l’intergiciel (middleware) WS-Federation :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory : propriétés d’inscription de l’application](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="4b7d0-156">Utiliser WS-Federation sans ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4b7d0-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="4b7d0-157">L’intergiciel (middleware) WS-Federation peut être Identityutilisé sans.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="4b7d0-158">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4b7d0-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="4b7d0-159">Ajouter WS-Federation comme fournisseur de connexion externe pour ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4b7d0-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="4b7d0-160">Ajoutez une dépendance sur [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) au projet.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="4b7d0-161">Ajouter WS-Federation à `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4b7d0-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="4b7d0-162">Se connecter avec WS-Federation</span><span class="sxs-lookup"><span data-stu-id="4b7d0-162">Log in with WS-Federation</span></span>

<span data-ttu-id="4b7d0-163">Accédez à l’application, puis cliquez sur le lien **se connecter** dans l’en-tête de navigation.</span><span class="sxs-lookup"><span data-stu-id="4b7d0-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="4b7d0-164">Vous avez la possibilité de vous connecter avec WsFederation : ![connexion à la page](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="4b7d0-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="4b7d0-165">Avec ADFS comme fournisseur, le bouton redirige vers une page de connexion ADFS : page de connexion ![ADFS.](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="4b7d0-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="4b7d0-166">Avec Azure Active Directory en tant que fournisseur, le bouton redirige vers une page de connexion AAD : ![page de connexion AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="4b7d0-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="4b7d0-167">Une connexion réussie pour un nouvel utilisateur redirige vers la page d’inscription de l’utilisateur de l’application ![: page inscrire](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="4b7d0-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
