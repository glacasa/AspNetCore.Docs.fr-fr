---
title: Utiliser cookie l’authentification sans ASP.NET Core Identity
author: rick-anderson
description: Découvrez comment utiliser cookie l’authentification sans ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 24ba49828db08fdd67723c81ac0c8d9981ab3404
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945413"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="cfed2-103">Utiliser cookie l’authentification sans ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cfed2-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="cfed2-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cfed2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfed2-105">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="cfed2-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="cfed2-106">Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="cfed2-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="cfed2-107">Pour plus d'informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="cfed2-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="cfed2-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfed2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cfed2-109">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="cfed2-110">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="cfed2-111">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="cfed2-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="cfed2-112">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="cfed2-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="cfed2-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="cfed2-113">Configuration</span></span>

<span data-ttu-id="cfed2-114">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour [Microsoft. AspNetCore. Authentication. Cookie package s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="cfed2-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="cfed2-115">Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) d’authentification avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="cfed2-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="cfed2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="cfed2-117">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="cfed2-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="cfed2-118">L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="cfed2-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="cfed2-119">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="cfed2-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="cfed2-120">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="cfed2-121">Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).</span><span class="sxs-lookup"><span data-stu-id="cfed2-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="cfed2-122">La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="cfed2-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="cfed2-123">cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="cfed2-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="cfed2-124">Pour plus d'informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="cfed2-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="cfed2-125">Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes.</span><span class="sxs-lookup"><span data-stu-id="cfed2-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="cfed2-126">Appelez les `UseAuthentication` `UseAuthorization` méthodes et avant d’appeler `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="cfed2-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="cfed2-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="cfed2-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="cfed2-128">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="cfed2-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="cfed2-129">Cookie Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="cfed2-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="cfed2-130">L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="cfed2-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="cfed2-131">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfed2-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="cfed2-132">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="cfed2-133">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="cfed2-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="cfed2-134">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="cfed2-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="cfed2-135">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cfed2-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="cfed2-136">Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="cfed2-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="cfed2-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="cfed2-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="cfed2-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="cfed2-138">Cookie.SameSite</span></span> | <span data-ttu-id="cfed2-139">Résultante Cookie . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="cfed2-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="cfed2-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-140">SameSiteMode.None</span></span>     | <span data-ttu-id="cfed2-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-141">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-142">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-143">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-144">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-145">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-146">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="cfed2-147">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="cfed2-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-148">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-149">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-150">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-151">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-152">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-153">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="cfed2-154">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="cfed2-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-155">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-156">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-157">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-158">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="cfed2-159">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="cfed2-160">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="cfed2-161">Créer une authentification cookie</span><span class="sxs-lookup"><span data-stu-id="cfed2-161">Create an authentication cookie</span></span>

<span data-ttu-id="cfed2-162">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="cfed2-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="cfed2-163">Les informations utilisateur sont sérialisées et stockées dans le cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="cfed2-164">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="cfed2-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="cfed2-165">`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="cfed2-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="cfed2-166">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cfed2-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="cfed2-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> est utilisé uniquement sur quelques chemins spécifiques par défaut, par exemple, le chemin d’accès de connexion et les chemins d’accès de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="cfed2-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="cfed2-168">Pour plus d’informations, consultez la [ Cookie source AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="cfed2-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="cfed2-169">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="cfed2-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="cfed2-170">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="cfed2-171">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="cfed2-171">Sign out</span></span>

<span data-ttu-id="cfed2-172">Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="cfed2-173">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="cfed2-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="cfed2-174">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cfed2-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="cfed2-175">Lorsque le navigateur se ferme, il supprime automatiquement les cookie s basés sur une session (s non persistantes cookie ), mais aucun cookie s n’est effacé lorsqu’un onglet individuel est fermé.</span><span class="sxs-lookup"><span data-stu-id="cfed2-175">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="cfed2-176">Le serveur n’est pas averti des événements de fermeture de tabulation ou de navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-176">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="cfed2-177">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="cfed2-177">React to back-end changes</span></span>

<span data-ttu-id="cfed2-178">Une fois cookie créé, cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-178">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="cfed2-179">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="cfed2-179">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="cfed2-180">Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-180">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="cfed2-181">L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.</span><span class="sxs-lookup"><span data-stu-id="cfed2-181">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="cfed2-182">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-182">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="cfed2-183">La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-183">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="cfed2-184">Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-184">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="cfed2-185">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="cfed2-185">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="cfed2-186">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-186">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="cfed2-187">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="cfed2-187">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="cfed2-188">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="cfed2-188">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="cfed2-189">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-189">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="cfed2-190">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="cfed2-190">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="cfed2-191">Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="cfed2-191">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cfed2-192">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="cfed2-192">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="cfed2-193">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-193">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="cfed2-194">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="cfed2-194">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="cfed2-195">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="cfed2-195">The approach described here is triggered on every request.</span></span> <span data-ttu-id="cfed2-196">La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-196">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="cfed2-197">Persistant cookie s</span><span class="sxs-lookup"><span data-stu-id="cfed2-197">Persistent cookies</span></span>

<span data-ttu-id="cfed2-198">Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-198">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="cfed2-199">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="cfed2-199">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="cfed2-200">L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-200">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="cfed2-201">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-201">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="cfed2-202">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="cfed2-202">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="cfed2-203">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-203">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="cfed2-204">cookieExpiration absolue</span><span class="sxs-lookup"><span data-stu-id="cfed2-204">Absolute cookie expiration</span></span>

<span data-ttu-id="cfed2-205">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="cfed2-205">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="cfed2-206">Pour créer un persistant cookie , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="cfed2-206">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="cfed2-207">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="cfed2-207">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="cfed2-208">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="cfed2-208">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="cfed2-209">L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="cfed2-209">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="cfed2-210">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-210">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfed2-211">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="cfed2-211">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="cfed2-212">Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="cfed2-212">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="cfed2-213">Pour plus d'informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="cfed2-213">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="cfed2-214">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfed2-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cfed2-215">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-215">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="cfed2-216">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-216">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="cfed2-217">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="cfed2-217">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="cfed2-218">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="cfed2-218">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="cfed2-219">Configuration</span><span class="sxs-lookup"><span data-stu-id="cfed2-219">Configuration</span></span>

<span data-ttu-id="cfed2-220">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour [Microsoft. AspNetCore. Authentication. Cookie package s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="cfed2-220">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="cfed2-221">Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="cfed2-221">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="cfed2-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="cfed2-223">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="cfed2-223">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="cfed2-224">L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="cfed2-224">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="cfed2-225">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="cfed2-225">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="cfed2-226">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-226">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="cfed2-227">Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).</span><span class="sxs-lookup"><span data-stu-id="cfed2-227">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="cfed2-228">La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="cfed2-228">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="cfed2-229">cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="cfed2-229">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="cfed2-230">Pour plus d'informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="cfed2-230">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="cfed2-231">Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="cfed2-231">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="cfed2-232">Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="cfed2-232">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="cfed2-233">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="cfed2-233">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="cfed2-234">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="cfed2-234">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="cfed2-235">Cookie Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="cfed2-235">Cookie Policy Middleware</span></span>

<span data-ttu-id="cfed2-236">L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="cfed2-236">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="cfed2-237">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfed2-237">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="cfed2-238">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-238">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="cfed2-239">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="cfed2-239">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="cfed2-240">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="cfed2-240">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="cfed2-241">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cfed2-241">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="cfed2-242">Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="cfed2-242">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="cfed2-243">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="cfed2-243">MinimumSameSitePolicy</span></span> | <span data-ttu-id="cfed2-244">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="cfed2-244">Cookie.SameSite</span></span> | <span data-ttu-id="cfed2-245">Résultante Cookie . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="cfed2-245">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="cfed2-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-246">SameSiteMode.None</span></span>     | <span data-ttu-id="cfed2-247">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-247">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-248">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-248">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-249">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-249">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-250">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-251">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-252">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-252">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="cfed2-253">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-253">SameSiteMode.Lax</span></span>      | <span data-ttu-id="cfed2-254">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-254">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-255">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-256">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-256">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-257">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-258">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-259">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-259">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="cfed2-260">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-260">SameSiteMode.Strict</span></span>   | <span data-ttu-id="cfed2-261">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="cfed2-261">SameSiteMode.None</span></span><br><span data-ttu-id="cfed2-262">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="cfed2-262">SameSiteMode.Lax</span></span><br><span data-ttu-id="cfed2-263">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-263">SameSiteMode.Strict</span></span> | <span data-ttu-id="cfed2-264">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="cfed2-265">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-265">SameSiteMode.Strict</span></span><br><span data-ttu-id="cfed2-266">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="cfed2-266">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="cfed2-267">Créer une authentification cookie</span><span class="sxs-lookup"><span data-stu-id="cfed2-267">Create an authentication cookie</span></span>

<span data-ttu-id="cfed2-268">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="cfed2-268">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="cfed2-269">Les informations utilisateur sont sérialisées et stockées dans le cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-269">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="cfed2-270">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="cfed2-270">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="cfed2-271">`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="cfed2-271">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="cfed2-272">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cfed2-272">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="cfed2-273">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="cfed2-273">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="cfed2-274">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-274">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="cfed2-275">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="cfed2-275">Sign out</span></span>

<span data-ttu-id="cfed2-276">Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-276">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="cfed2-277">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="cfed2-277">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="cfed2-278">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cfed2-278">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="cfed2-279">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="cfed2-279">React to back-end changes</span></span>

<span data-ttu-id="cfed2-280">Une fois cookie créé, cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-280">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="cfed2-281">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="cfed2-281">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="cfed2-282">Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="cfed2-282">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="cfed2-283">L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.</span><span class="sxs-lookup"><span data-stu-id="cfed2-283">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="cfed2-284">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-284">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="cfed2-285">La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-285">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="cfed2-286">Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-286">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="cfed2-287">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="cfed2-287">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="cfed2-288">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-288">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="cfed2-289">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="cfed2-289">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="cfed2-290">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="cfed2-290">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="cfed2-291">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-291">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="cfed2-292">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="cfed2-292">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="cfed2-293">Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="cfed2-293">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cfed2-294">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="cfed2-294">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="cfed2-295">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cfed2-295">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="cfed2-296">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="cfed2-296">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="cfed2-297">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="cfed2-297">The approach described here is triggered on every request.</span></span> <span data-ttu-id="cfed2-298">La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="cfed2-298">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="cfed2-299">Persistant cookie s</span><span class="sxs-lookup"><span data-stu-id="cfed2-299">Persistent cookies</span></span>

<span data-ttu-id="cfed2-300">Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-300">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="cfed2-301">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="cfed2-301">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="cfed2-302">L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cfed2-302">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="cfed2-303">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-303">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="cfed2-304">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="cfed2-304">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="cfed2-305">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="cfed2-305">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="cfed2-306">cookieExpiration absolue</span><span class="sxs-lookup"><span data-stu-id="cfed2-306">Absolute cookie expiration</span></span>

<span data-ttu-id="cfed2-307">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="cfed2-307">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="cfed2-308">Pour créer un persistant cookie , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="cfed2-308">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="cfed2-309">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="cfed2-309">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="cfed2-310">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="cfed2-310">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="cfed2-311">L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="cfed2-311">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="cfed2-312">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="cfed2-312">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cfed2-313">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cfed2-313">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="cfed2-314">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="cfed2-314">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
