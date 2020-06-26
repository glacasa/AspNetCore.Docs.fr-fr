---
title: Utiliser l’authentification par cookie sans ASP.NET CoreIdentity
author: rick-anderson
description: Découvrez comment utiliser l’authentification par cookie sans ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408823"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="33c3e-103">Utiliser l’authentification par cookie sans ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="33c3e-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="33c3e-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33c3e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33c3e-105">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="33c3e-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="33c3e-106">Toutefois, un fournisseur d’authentification basé sur des cookies sans ASP.NET Core Identity peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="33c3e-107">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="33c3e-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="33c3e-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33c3e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="33c3e-109">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="33c3e-110">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="33c3e-111">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="33c3e-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="33c3e-112">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="33c3e-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="33c3e-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="33c3e-113">Configuration</span></span>

<span data-ttu-id="33c3e-114">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="33c3e-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="33c3e-115">Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) d’authentification avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="33c3e-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="33c3e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="33c3e-117">`AuthenticationScheme`est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="33c3e-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="33c3e-118">L’affectation de la `AuthenticationScheme` valeur à [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="33c3e-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="33c3e-119">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="33c3e-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="33c3e-120">Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="33c3e-121">Lorsqu’un schéma d’authentification de cookie n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« cookies »).</span><span class="sxs-lookup"><span data-stu-id="33c3e-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="33c3e-122">La propriété du cookie d’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="33c3e-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="33c3e-123">Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="33c3e-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="33c3e-124">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="33c3e-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="33c3e-125">Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes.</span><span class="sxs-lookup"><span data-stu-id="33c3e-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="33c3e-126">Appelez les `UseAuthentication` `UseAuthorization` méthodes et avant d’appeler `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="33c3e-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="33c3e-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="33c3e-128">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="33c3e-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="33c3e-129">Intergiciel de stratégie de cookie</span><span class="sxs-lookup"><span data-stu-id="33c3e-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="33c3e-130">L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="33c3e-131">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="33c3e-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="33c3e-132">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="33c3e-133">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="33c3e-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="33c3e-134">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="33c3e-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="33c3e-135">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="33c3e-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="33c3e-136">Le paramètre d’intergiciel de stratégie de cookie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="33c3e-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="33c3e-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="33c3e-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="33c3e-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="33c3e-138">Cookie.SameSite</span></span> | <span data-ttu-id="33c3e-139">Paramètre de cookie. SameSite résultant</span><span class="sxs-lookup"><span data-stu-id="33c3e-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="33c3e-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-140">SameSiteMode.None</span></span>     | <span data-ttu-id="33c3e-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-141">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-142">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-143">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-144">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-145">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-146">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="33c3e-147">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="33c3e-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-148">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-149">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-150">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-151">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-152">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-153">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="33c3e-154">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="33c3e-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-155">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-156">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-157">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-158">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="33c3e-159">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="33c3e-160">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="33c3e-161">Créer un cookie d’authentification</span><span class="sxs-lookup"><span data-stu-id="33c3e-161">Create an authentication cookie</span></span>

<span data-ttu-id="33c3e-162">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="33c3e-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="33c3e-163">Les informations utilisateur sont sérialisées et stockées dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="33c3e-164">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="33c3e-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="33c3e-165">`SignInAsync`crée un cookie chiffré et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="33c3e-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="33c3e-166">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="33c3e-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>est utilisé uniquement sur quelques chemins spécifiques par défaut, par exemple, le chemin d’accès de connexion et les chemins d’accès de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="33c3e-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="33c3e-168">Pour plus d’informations, consultez la [source CookieAuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="33c3e-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="33c3e-169">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="33c3e-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="33c3e-170">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="33c3e-171">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="33c3e-171">Sign out</span></span>

<span data-ttu-id="33c3e-172">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="33c3e-173">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="33c3e-174">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="33c3e-175">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="33c3e-175">React to back-end changes</span></span>

<span data-ttu-id="33c3e-176">Une fois qu’un cookie est créé, le cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="33c3e-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="33c3e-177">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="33c3e-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="33c3e-178">Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="33c3e-179">L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.</span><span class="sxs-lookup"><span data-stu-id="33c3e-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="33c3e-180">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="33c3e-181">La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="33c3e-182">Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="33c3e-183">Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="33c3e-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="33c3e-184">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="33c3e-185">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="33c3e-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="33c3e-186">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="33c3e-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="33c3e-187">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="33c3e-188">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="33c3e-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="33c3e-189">Inscrivez l’instance des événements lors de l’inscription du service de cookie dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="33c3e-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="33c3e-190">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="33c3e-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="33c3e-191">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="33c3e-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="33c3e-192">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="33c3e-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="33c3e-193">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="33c3e-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="33c3e-194">La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="33c3e-195">Cookies persistants</span><span class="sxs-lookup"><span data-stu-id="33c3e-195">Persistent cookies</span></span>

<span data-ttu-id="33c3e-196">Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="33c3e-197">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="33c3e-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="33c3e-198">L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="33c3e-199">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="33c3e-200">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.</span><span class="sxs-lookup"><span data-stu-id="33c3e-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="33c3e-201">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="33c3e-202">Expiration absolue des cookies</span><span class="sxs-lookup"><span data-stu-id="33c3e-202">Absolute cookie expiration</span></span>

<span data-ttu-id="33c3e-203">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="33c3e-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="33c3e-204">Pour créer un cookie persistant, `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="33c3e-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="33c3e-205">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="33c3e-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="33c3e-206">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="33c3e-207">L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="33c3e-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="33c3e-208">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="33c3e-209">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="33c3e-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="33c3e-210">Toutefois, un fournisseur d’authentification d’authentification basée sur les cookies sans ASP.NET Core Identity peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="33c3e-211">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="33c3e-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="33c3e-212">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33c3e-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="33c3e-213">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="33c3e-214">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="33c3e-215">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="33c3e-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="33c3e-216">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="33c3e-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="33c3e-217">Configuration</span><span class="sxs-lookup"><span data-stu-id="33c3e-217">Configuration</span></span>

<span data-ttu-id="33c3e-218">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="33c3e-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="33c3e-219">Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="33c3e-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="33c3e-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="33c3e-221">`AuthenticationScheme`est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="33c3e-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="33c3e-222">L’affectation de la `AuthenticationScheme` valeur à [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="33c3e-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="33c3e-223">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="33c3e-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="33c3e-224">Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="33c3e-225">Lorsqu’un schéma d’authentification de cookie n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« cookies »).</span><span class="sxs-lookup"><span data-stu-id="33c3e-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="33c3e-226">La propriété du cookie d’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="33c3e-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="33c3e-227">Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="33c3e-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="33c3e-228">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="33c3e-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="33c3e-229">Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="33c3e-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="33c3e-230">Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="33c3e-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="33c3e-231">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="33c3e-232">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="33c3e-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="33c3e-233">Intergiciel de stratégie de cookie</span><span class="sxs-lookup"><span data-stu-id="33c3e-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="33c3e-234">L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="33c3e-235">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="33c3e-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="33c3e-236">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="33c3e-237">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="33c3e-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="33c3e-238">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="33c3e-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="33c3e-239">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="33c3e-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="33c3e-240">Le paramètre d’intergiciel de stratégie de cookie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="33c3e-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="33c3e-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="33c3e-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="33c3e-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="33c3e-242">Cookie.SameSite</span></span> | <span data-ttu-id="33c3e-243">Paramètre de cookie. SameSite résultant</span><span class="sxs-lookup"><span data-stu-id="33c3e-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="33c3e-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-244">SameSiteMode.None</span></span>     | <span data-ttu-id="33c3e-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-245">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-246">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-247">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-248">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-249">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-250">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="33c3e-251">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="33c3e-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-252">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-253">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-254">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-255">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-256">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-257">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="33c3e-258">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="33c3e-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="33c3e-259">SameSiteMode.None</span></span><br><span data-ttu-id="33c3e-260">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="33c3e-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="33c3e-261">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="33c3e-262">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="33c3e-263">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="33c3e-264">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="33c3e-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="33c3e-265">Créer un cookie d’authentification</span><span class="sxs-lookup"><span data-stu-id="33c3e-265">Create an authentication cookie</span></span>

<span data-ttu-id="33c3e-266">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="33c3e-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="33c3e-267">Les informations utilisateur sont sérialisées et stockées dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="33c3e-268">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="33c3e-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="33c3e-269">`SignInAsync`crée un cookie chiffré et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="33c3e-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="33c3e-270">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="33c3e-271">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="33c3e-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="33c3e-272">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="33c3e-273">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="33c3e-273">Sign out</span></span>

<span data-ttu-id="33c3e-274">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="33c3e-275">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="33c3e-276">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="33c3e-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="33c3e-277">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="33c3e-277">React to back-end changes</span></span>

<span data-ttu-id="33c3e-278">Une fois qu’un cookie est créé, le cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="33c3e-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="33c3e-279">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="33c3e-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="33c3e-280">Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="33c3e-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="33c3e-281">L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.</span><span class="sxs-lookup"><span data-stu-id="33c3e-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="33c3e-282">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="33c3e-283">La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="33c3e-284">Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="33c3e-285">Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="33c3e-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="33c3e-286">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="33c3e-287">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="33c3e-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="33c3e-288">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="33c3e-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="33c3e-289">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="33c3e-290">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="33c3e-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="33c3e-291">Inscrivez l’instance des événements lors de l’inscription du service de cookie dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="33c3e-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="33c3e-292">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="33c3e-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="33c3e-293">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="33c3e-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="33c3e-294">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="33c3e-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="33c3e-295">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="33c3e-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="33c3e-296">La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="33c3e-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="33c3e-297">Cookies persistants</span><span class="sxs-lookup"><span data-stu-id="33c3e-297">Persistent cookies</span></span>

<span data-ttu-id="33c3e-298">Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="33c3e-299">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="33c3e-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="33c3e-300">L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="33c3e-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="33c3e-301">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="33c3e-302">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.</span><span class="sxs-lookup"><span data-stu-id="33c3e-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="33c3e-303">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="33c3e-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="33c3e-304">Expiration absolue des cookies</span><span class="sxs-lookup"><span data-stu-id="33c3e-304">Absolute cookie expiration</span></span>

<span data-ttu-id="33c3e-305">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="33c3e-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="33c3e-306">Pour créer un cookie persistant, `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="33c3e-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="33c3e-307">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="33c3e-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="33c3e-308">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="33c3e-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="33c3e-309">L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="33c3e-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="33c3e-310">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="33c3e-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="33c3e-311">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="33c3e-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="33c3e-312">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="33c3e-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
