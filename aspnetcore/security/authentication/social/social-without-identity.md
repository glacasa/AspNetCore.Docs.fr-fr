---
title: Authentification Facebook, Google et fournisseur externe sans ASP.NET CoreIdentity
author: rick-anderson
description: Explication de l’utilisation de l’authentification utilisateur Facebook, Google, Twitter, etc. sans IdentityASP.net core.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775737"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="95137-103">Utiliser l’authentification du fournisseur de connexion sociale sans ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="95137-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="95137-104">Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95137-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95137-105"><xref:security/authentication/social/index>décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes.</span><span class="sxs-lookup"><span data-stu-id="95137-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="95137-106">L’approche décrite dans cette rubrique comprend des Identity ASP.net core en tant que fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="95137-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="95137-107">Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.net Core Identity.</span><span class="sxs-lookup"><span data-stu-id="95137-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="95137-108">Cela est utile pour les applications qui n’ont pas besoin de toutes les Identityfonctionnalités de ASP.net Core, mais nécessitent toujours une intégration avec un fournisseur d’authentification externe approuvé.</span><span class="sxs-lookup"><span data-stu-id="95137-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="95137-109">Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="95137-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="95137-110">L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google.</span><span class="sxs-lookup"><span data-stu-id="95137-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="95137-111">Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="95137-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="95137-112">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="95137-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="95137-113">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="95137-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="95137-114">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="95137-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="95137-115">Autres fournisseurs</span><span class="sxs-lookup"><span data-stu-id="95137-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="95137-116">Configuration</span><span class="sxs-lookup"><span data-stu-id="95137-116">Configuration</span></span>

<span data-ttu-id="95137-117">Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>l' <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>application avec <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> les méthodes, et :</span><span class="sxs-lookup"><span data-stu-id="95137-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="95137-118">L’appel à <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> définit le de <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>l’application.</span><span class="sxs-lookup"><span data-stu-id="95137-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="95137-119">`DefaultScheme` Est le schéma par défaut utilisé par les méthodes `HttpContext` d’extension d’authentification suivantes :</span><span class="sxs-lookup"><span data-stu-id="95137-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="95137-120">La définition de l' `DefaultScheme` application sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« cookies ») configure l’application pour utiliser les cookies comme schéma par défaut pour ces méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="95137-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="95137-121">La définition de l' <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> application sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels `ChallengeAsync`à.</span><span class="sxs-lookup"><span data-stu-id="95137-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="95137-122">`DefaultChallengeScheme`remplace `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="95137-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="95137-123">Pour <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> obtenir des propriétés supplémentaires qui remplacent `DefaultScheme` lorsqu’elles sont définies, consultez.</span><span class="sxs-lookup"><span data-stu-id="95137-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="95137-124">Dans `Startup.Configure`, appelez `UseAuthentication` et `UseAuthorization` entre appelant `UseRouting` et `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="95137-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="95137-125">Cela définit la `HttpContext.User` propriété et exécute l’intergiciel (middleware) des autorisations pour les demandes :</span><span class="sxs-lookup"><span data-stu-id="95137-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="95137-126">Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="95137-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="95137-127">Pour en savoir plus sur l’authentification par <xref:security/authentication/cookie>cookie, consultez.</span><span class="sxs-lookup"><span data-stu-id="95137-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="95137-128">Appliquer l’autorisation</span><span class="sxs-lookup"><span data-stu-id="95137-128">Apply authorization</span></span>

<span data-ttu-id="95137-129">Testez la configuration d’authentification de l’application `AuthorizeAttribute` en appliquant l’attribut à un contrôleur, une action ou une page.</span><span class="sxs-lookup"><span data-stu-id="95137-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="95137-130">Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :</span><span class="sxs-lookup"><span data-stu-id="95137-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="95137-131">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="95137-131">Sign out</span></span>

<span data-ttu-id="95137-132">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="95137-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="95137-133">Le code suivant ajoute un `Logout` gestionnaire de page à la page d' *index* :</span><span class="sxs-lookup"><span data-stu-id="95137-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="95137-134">Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="95137-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="95137-135">Le `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` l’application est utilisé comme un recul.</span><span class="sxs-lookup"><span data-stu-id="95137-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95137-136">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="95137-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95137-137"><xref:security/authentication/social/index>décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes.</span><span class="sxs-lookup"><span data-stu-id="95137-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="95137-138">L’approche décrite dans cette rubrique comprend des Identity ASP.net core en tant que fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="95137-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="95137-139">Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.net Core Identity.</span><span class="sxs-lookup"><span data-stu-id="95137-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="95137-140">Cela est utile pour les applications qui n’ont pas besoin de toutes les Identityfonctionnalités de ASP.net Core, mais nécessitent toujours une intégration avec un fournisseur d’authentification externe approuvé.</span><span class="sxs-lookup"><span data-stu-id="95137-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="95137-141">Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="95137-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="95137-142">L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google.</span><span class="sxs-lookup"><span data-stu-id="95137-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="95137-143">Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="95137-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="95137-144">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="95137-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="95137-145">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="95137-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="95137-146">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="95137-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="95137-147">Autres fournisseurs</span><span class="sxs-lookup"><span data-stu-id="95137-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="95137-148">Configuration</span><span class="sxs-lookup"><span data-stu-id="95137-148">Configuration</span></span>

<span data-ttu-id="95137-149">Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de `AddAuthentication`l' `AddCookie`application avec `AddGoogle` les méthodes, et :</span><span class="sxs-lookup"><span data-stu-id="95137-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="95137-150">L’appel à [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) définit le [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de l’application.</span><span class="sxs-lookup"><span data-stu-id="95137-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="95137-151">`DefaultScheme` Est le schéma par défaut utilisé par les méthodes `HttpContext` d’extension d’authentification suivantes :</span><span class="sxs-lookup"><span data-stu-id="95137-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="95137-152">La définition de l' `DefaultScheme` application sur [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« cookies ») configure l’application pour utiliser les cookies comme schéma par défaut pour ces méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="95137-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="95137-153">La définition de l' <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> application sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels `ChallengeAsync`à.</span><span class="sxs-lookup"><span data-stu-id="95137-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="95137-154">`DefaultChallengeScheme`remplace `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="95137-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="95137-155">Pour <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> obtenir des propriétés supplémentaires qui remplacent `DefaultScheme` lorsqu’elles sont définies, consultez.</span><span class="sxs-lookup"><span data-stu-id="95137-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="95137-156">Dans la `Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification `HttpContext.User` qui définit la propriété.</span><span class="sxs-lookup"><span data-stu-id="95137-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="95137-157">Appelez la `UseAuthentication` méthode avant d' `UseMvcWithDefaultRoute` appeler `UseMvc`ou :</span><span class="sxs-lookup"><span data-stu-id="95137-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="95137-158">Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="95137-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="95137-159">Pour en savoir plus sur l’authentification par <xref:security/authentication/cookie>cookie, consultez.</span><span class="sxs-lookup"><span data-stu-id="95137-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="95137-160">Appliquer l’autorisation</span><span class="sxs-lookup"><span data-stu-id="95137-160">Apply authorization</span></span>

<span data-ttu-id="95137-161">Testez la configuration d’authentification de l’application `AuthorizeAttribute` en appliquant l’attribut à un contrôleur, une action ou une page.</span><span class="sxs-lookup"><span data-stu-id="95137-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="95137-162">Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :</span><span class="sxs-lookup"><span data-stu-id="95137-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="95137-163">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="95137-163">Sign out</span></span>

<span data-ttu-id="95137-164">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="95137-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="95137-165">Le code suivant ajoute un `Logout` gestionnaire de page à la page d' *index* :</span><span class="sxs-lookup"><span data-stu-id="95137-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="95137-166">Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="95137-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="95137-167">Le `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` l’application est utilisé comme un recul.</span><span class="sxs-lookup"><span data-stu-id="95137-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95137-168">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="95137-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
