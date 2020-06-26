---
title: Authentification Facebook, Google et fournisseur externe sans ASP.NET CoreIdentity
author: rick-anderson
description: Explication de l’utilisation de l’authentification utilisateur Facebook, Google, Twitter, etc. sans ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406041"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="08456-103">Utiliser l’authentification du fournisseur de connexion sociale sans ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="08456-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="08456-104">Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="08456-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08456-105"><xref:security/authentication/social/index>décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes.</span><span class="sxs-lookup"><span data-stu-id="08456-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="08456-106">L’approche décrite dans cette rubrique comprend des ASP.NET Core Identity en tant que fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="08456-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="08456-107">Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="08456-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="08456-108">Cela est utile pour les applications qui n’ont pas besoin de toutes les fonctionnalités de ASP.NET Core Identity , mais nécessitent toujours une intégration avec un fournisseur d’authentification externe approuvé.</span><span class="sxs-lookup"><span data-stu-id="08456-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="08456-109">Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="08456-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="08456-110">L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google.</span><span class="sxs-lookup"><span data-stu-id="08456-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="08456-111">Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="08456-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="08456-112">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="08456-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="08456-113">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="08456-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="08456-114">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="08456-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="08456-115">Autres fournisseurs</span><span class="sxs-lookup"><span data-stu-id="08456-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="08456-116">Configuration</span><span class="sxs-lookup"><span data-stu-id="08456-116">Configuration</span></span>

<span data-ttu-id="08456-117">Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de l’application avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes, et <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="08456-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="08456-118">L’appel à <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> définit le de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="08456-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="08456-119">`DefaultScheme`Est le schéma par défaut utilisé par les `HttpContext` méthodes d’extension d’authentification suivantes :</span><span class="sxs-lookup"><span data-stu-id="08456-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="08456-120">La définition de l’application `DefaultScheme` sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« cookies ») configure l’application pour utiliser les cookies comme schéma par défaut pour ces méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="08456-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="08456-121">La définition de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels à `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="08456-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="08456-122">`DefaultChallengeScheme`remplace `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="08456-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="08456-123">Pour obtenir des <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> propriétés supplémentaires qui remplacent lorsqu’elles sont `DefaultScheme` définies, consultez.</span><span class="sxs-lookup"><span data-stu-id="08456-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="08456-124">Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` entre appelant `UseRouting` et `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="08456-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="08456-125">Cela définit la `HttpContext.User` propriété et exécute l’intergiciel (middleware) des autorisations pour les demandes :</span><span class="sxs-lookup"><span data-stu-id="08456-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="08456-126">Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="08456-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="08456-127">Pour en savoir plus sur l’authentification par cookie, consultez <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="08456-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="08456-128">Appliquer l’autorisation</span><span class="sxs-lookup"><span data-stu-id="08456-128">Apply authorization</span></span>

<span data-ttu-id="08456-129">Testez la configuration d’authentification de l’application en appliquant l' `AuthorizeAttribute` attribut à un contrôleur, une action ou une page.</span><span class="sxs-lookup"><span data-stu-id="08456-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="08456-130">Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :</span><span class="sxs-lookup"><span data-stu-id="08456-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="08456-131">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="08456-131">Sign out</span></span>

<span data-ttu-id="08456-132">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="08456-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="08456-133">Le code suivant ajoute un `Logout` Gestionnaire de page à la page d' *index* :</span><span class="sxs-lookup"><span data-stu-id="08456-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="08456-134">Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="08456-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="08456-135">Le de l' `DefaultScheme` application `CookieAuthenticationDefaults.AuthenticationScheme` est utilisé comme un recul.</span><span class="sxs-lookup"><span data-stu-id="08456-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08456-136">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="08456-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08456-137"><xref:security/authentication/social/index>décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes.</span><span class="sxs-lookup"><span data-stu-id="08456-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="08456-138">L’approche décrite dans cette rubrique comprend des ASP.NET Core Identity en tant que fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="08456-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="08456-139">Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="08456-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="08456-140">Cela est utile pour les applications qui n’ont pas besoin de toutes les fonctionnalités de ASP.NET Core Identity , mais nécessitent toujours une intégration avec un fournisseur d’authentification externe approuvé.</span><span class="sxs-lookup"><span data-stu-id="08456-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="08456-141">Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="08456-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="08456-142">L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google.</span><span class="sxs-lookup"><span data-stu-id="08456-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="08456-143">Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="08456-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="08456-144">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="08456-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="08456-145">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="08456-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="08456-146">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="08456-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="08456-147">Autres fournisseurs</span><span class="sxs-lookup"><span data-stu-id="08456-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="08456-148">Configuration</span><span class="sxs-lookup"><span data-stu-id="08456-148">Configuration</span></span>

<span data-ttu-id="08456-149">Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de l’application avec les `AddAuthentication` `AddCookie` méthodes, et `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="08456-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="08456-150">L’appel à [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) définit le [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de l’application.</span><span class="sxs-lookup"><span data-stu-id="08456-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="08456-151">`DefaultScheme`Est le schéma par défaut utilisé par les `HttpContext` méthodes d’extension d’authentification suivantes :</span><span class="sxs-lookup"><span data-stu-id="08456-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="08456-152">La définition de l’application `DefaultScheme` sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« cookies ») configure l’application pour utiliser les cookies comme schéma par défaut pour ces méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="08456-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="08456-153">La définition de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels à `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="08456-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="08456-154">`DefaultChallengeScheme`remplace `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="08456-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="08456-155">Pour obtenir des <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> propriétés supplémentaires qui remplacent lorsqu’elles sont `DefaultScheme` définies, consultez.</span><span class="sxs-lookup"><span data-stu-id="08456-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="08456-156">Dans la `Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="08456-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="08456-157">Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="08456-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="08456-158">Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="08456-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="08456-159">Pour en savoir plus sur l’authentification par cookie, consultez <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="08456-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="08456-160">Appliquer l’autorisation</span><span class="sxs-lookup"><span data-stu-id="08456-160">Apply authorization</span></span>

<span data-ttu-id="08456-161">Testez la configuration d’authentification de l’application en appliquant l' `AuthorizeAttribute` attribut à un contrôleur, une action ou une page.</span><span class="sxs-lookup"><span data-stu-id="08456-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="08456-162">Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :</span><span class="sxs-lookup"><span data-stu-id="08456-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="08456-163">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="08456-163">Sign out</span></span>

<span data-ttu-id="08456-164">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="08456-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="08456-165">Le code suivant ajoute un `Logout` Gestionnaire de page à la page d' *index* :</span><span class="sxs-lookup"><span data-stu-id="08456-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="08456-166">Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="08456-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="08456-167">Le de l' `DefaultScheme` application `CookieAuthenticationDefaults.AuthenticationScheme` est utilisé comme un recul.</span><span class="sxs-lookup"><span data-stu-id="08456-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08456-168">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="08456-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
