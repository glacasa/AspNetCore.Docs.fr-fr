---
title: Utiliser l’authentification par cookie sans ASP.NET CoreIdentity
author: rick-anderson
description: Découvrez comment utiliser l’authentification par cookie sans IdentityASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773842"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="260da-103">Utiliser l’authentification par cookie sans ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="260da-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="260da-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="260da-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="260da-105">ASP.NET Core identité est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="260da-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="260da-106">Toutefois, il est possible d’utiliser un fournisseur d’authentification basé sur des cookies sans ASP.NET Core d’identité.</span><span class="sxs-lookup"><span data-stu-id="260da-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="260da-107">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="260da-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="260da-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="260da-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="260da-109">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="260da-110">Utilisez l' **Email** adresse `maria.rodriguez@contoso.com` de messagerie et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="260da-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="260da-111">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="260da-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="260da-112">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="260da-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="260da-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="260da-113">Configuration</span></span>

<span data-ttu-id="260da-114">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="260da-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="260da-115">Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> d' <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> authentification avec les méthodes et :</span><span class="sxs-lookup"><span data-stu-id="260da-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="260da-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="260da-117">`AuthenticationScheme`est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="260da-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="260da-118">L’affectation `AuthenticationScheme` de la valeur à [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="260da-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="260da-119">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="260da-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="260da-120">Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="260da-121">Lorsqu’un schéma d’authentification de cookie n' <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>est pas fourni `CookieAuthenticationDefaults.AuthenticationScheme` à, il utilise (« cookies »).</span><span class="sxs-lookup"><span data-stu-id="260da-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="260da-122">La propriété du <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie d’authentification a la `true` valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="260da-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="260da-123">Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="260da-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="260da-124">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="260da-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="260da-125">Dans `Startup.Configure`, appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes.</span><span class="sxs-lookup"><span data-stu-id="260da-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="260da-126">Appelez les `UseAuthentication` méthodes `UseAuthorization` et avant d' `UseEndpoints`appeler :</span><span class="sxs-lookup"><span data-stu-id="260da-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="260da-127">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="260da-128">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification `Startup.ConfigureServices` dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="260da-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="260da-129">Intergiciel de stratégie de cookie</span><span class="sxs-lookup"><span data-stu-id="260da-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="260da-130">L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="260da-131">L’ajout de l’intergiciel au pipeline de traitement de l'&mdash;application est sensible à l’ordre. il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="260da-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="260da-132">À <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> utiliser fourni à l’intergiciel (middleware) de stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="260da-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="260da-133">La valeur <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> par défaut `SameSiteMode.Lax` est d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="260da-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="260da-134">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict`, définissez le `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="260da-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="260da-135">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="260da-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="260da-136">Le paramètre d’intergiciel de stratégie de `MinimumSameSitePolicy` cookie pour peut affecter la `Cookie.SameSite` valeur `CookieAuthenticationOptions` de dans les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="260da-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="260da-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="260da-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="260da-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="260da-138">Cookie.SameSite</span></span> | <span data-ttu-id="260da-139">Paramètre de cookie. SameSite résultant</span><span class="sxs-lookup"><span data-stu-id="260da-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="260da-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-140">SameSiteMode.None</span></span>     | <span data-ttu-id="260da-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-141">SameSiteMode.None</span></span><br><span data-ttu-id="260da-142">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-143">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-144">SameSiteMode.None</span></span><br><span data-ttu-id="260da-145">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-146">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="260da-147">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="260da-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-148">SameSiteMode.None</span></span><br><span data-ttu-id="260da-149">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-150">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-151">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-152">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-153">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="260da-154">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="260da-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-155">SameSiteMode.None</span></span><br><span data-ttu-id="260da-156">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-157">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-158">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="260da-159">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="260da-160">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="260da-161">Créer un cookie d’authentification</span><span class="sxs-lookup"><span data-stu-id="260da-161">Create an authentication cookie</span></span>

<span data-ttu-id="260da-162">Pour créer un cookie contenant des informations sur l’utilisateur <xref:System.Security.Claims.ClaimsPrincipal>, construisez un.</span><span class="sxs-lookup"><span data-stu-id="260da-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="260da-163">Les informations utilisateur sont sérialisées et stockées dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="260da-164">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les s <xref:System.Security.Claims.Claim>requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="260da-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="260da-165">`SignInAsync`crée un cookie chiffré et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="260da-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="260da-166">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="260da-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="260da-167">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="260da-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="260da-168">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="260da-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="260da-169">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="260da-169">Sign out</span></span>

<span data-ttu-id="260da-170">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="260da-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="260da-171">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="260da-172">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="260da-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="260da-173">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="260da-173">React to back-end changes</span></span>

<span data-ttu-id="260da-174">Une fois qu’un cookie est créé, le cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="260da-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="260da-175">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="260da-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="260da-176">Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="260da-177">L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.</span><span class="sxs-lookup"><span data-stu-id="260da-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="260da-178">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="260da-179">La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="260da-180">Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="260da-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="260da-181">Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="260da-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="260da-182">Dans l’exemple d’application, la base de données `IUserRepository` est implémentée `LastChanged` dans et stocke une valeur.</span><span class="sxs-lookup"><span data-stu-id="260da-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="260da-183">Lorsqu’un utilisateur est mis à jour dans la base `LastChanged` de données, la valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="260da-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="260da-184">Afin d’invalider un cookie lorsque la base de données change en fonction `LastChanged` de la valeur, créez le cookie `LastChanged` avec une revendication contenant `LastChanged` la valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="260da-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="260da-185">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="260da-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="260da-186">Voici un exemple d’implémentation de `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="260da-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="260da-187">Inscrivez l’instance des événements lors de l’inscription du `Startup.ConfigureServices` service de cookie dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="260da-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="260da-188">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour `CustomCookieAuthenticationEvents` votre classe :</span><span class="sxs-lookup"><span data-stu-id="260da-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="260da-189">Imaginez une situation dans laquelle le nom de l’utilisateur est&mdash;mis à jour une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="260da-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="260da-190">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non `context.ReplacePrincipal` destructrice, appelez `context.ShouldRenew` et affectez à `true`la propriété la valeur.</span><span class="sxs-lookup"><span data-stu-id="260da-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="260da-191">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="260da-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="260da-192">La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="260da-193">Cookies persistants</span><span class="sxs-lookup"><span data-stu-id="260da-193">Persistent cookies</span></span>

<span data-ttu-id="260da-194">Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="260da-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="260da-195">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="260da-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="260da-196">L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="260da-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="260da-197">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="260da-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="260da-198">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.</span><span class="sxs-lookup"><span data-stu-id="260da-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="260da-199">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="260da-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="260da-200">Expiration absolue des cookies</span><span class="sxs-lookup"><span data-stu-id="260da-200">Absolute cookie expiration</span></span>

<span data-ttu-id="260da-201">Une heure d’expiration absolue peut être définie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>avec.</span><span class="sxs-lookup"><span data-stu-id="260da-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="260da-202">Pour créer un cookie persistant, `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="260da-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="260da-203">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="260da-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="260da-204">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="260da-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="260da-205">L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="260da-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="260da-206">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="260da-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="260da-207">ASP.NET Core identité est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="260da-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="260da-208">Toutefois, il est possible d’utiliser un fournisseur d’authentification d’authentification basée sur les cookies sans ASP.NET Core d’identité.</span><span class="sxs-lookup"><span data-stu-id="260da-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="260da-209">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="260da-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="260da-210">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="260da-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="260da-211">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="260da-212">Utilisez l' **Email** adresse `maria.rodriguez@contoso.com` de messagerie et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="260da-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="260da-213">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="260da-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="260da-214">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="260da-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="260da-215">Configuration</span><span class="sxs-lookup"><span data-stu-id="260da-215">Configuration</span></span>

<span data-ttu-id="260da-216">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour le package [Microsoft. AspNetCore. Authentication. Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="260da-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="260da-217">Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="260da-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="260da-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="260da-219">`AuthenticationScheme`est utile lorsqu’il existe plusieurs instances d’authentification de cookie et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="260da-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="260da-220">L’affectation `AuthenticationScheme` de la valeur à [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « cookies » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="260da-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="260da-221">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="260da-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="260da-222">Le schéma d’authentification de l’application est différent du schéma d’authentification des cookies de l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="260da-223">Lorsqu’un schéma d’authentification de cookie n' <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>est pas fourni `CookieAuthenticationDefaults.AuthenticationScheme` à, il utilise (« cookies »).</span><span class="sxs-lookup"><span data-stu-id="260da-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="260da-224">La propriété du <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie d’authentification a la `true` valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="260da-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="260da-225">Les cookies d’authentification sont autorisés lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="260da-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="260da-226">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="260da-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="260da-227">Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification `HttpContext.User` qui définit la propriété.</span><span class="sxs-lookup"><span data-stu-id="260da-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="260da-228">Appelez la `UseAuthentication` méthode avant d' `UseMvcWithDefaultRoute` appeler `UseMvc`ou :</span><span class="sxs-lookup"><span data-stu-id="260da-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="260da-229">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="260da-230">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification `Startup.ConfigureServices` dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="260da-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="260da-231">Intergiciel de stratégie de cookie</span><span class="sxs-lookup"><span data-stu-id="260da-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="260da-232">L’intergiciel (middleware) de [stratégie de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) active les fonctionnalités de stratégie de cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="260da-233">L’ajout de l’intergiciel au pipeline de traitement de l'&mdash;application est sensible à l’ordre. il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="260da-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="260da-234">À <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> utiliser fourni à l’intergiciel (middleware) de stratégie de cookie pour contrôler les caractéristiques globales du traitement des cookies et raccorder des gestionnaires de traitement des cookies lorsque les cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="260da-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="260da-235">La valeur <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> par défaut `SameSiteMode.Lax` est d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="260da-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="260da-236">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict`, définissez le `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="260da-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="260da-237">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de sécurité des cookies pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="260da-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="260da-238">Le paramètre d’intergiciel de stratégie de `MinimumSameSitePolicy` cookie pour peut affecter la `Cookie.SameSite` valeur `CookieAuthenticationOptions` de dans les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="260da-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="260da-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="260da-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="260da-240">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="260da-240">Cookie.SameSite</span></span> | <span data-ttu-id="260da-241">Paramètre de cookie. SameSite résultant</span><span class="sxs-lookup"><span data-stu-id="260da-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="260da-242">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-242">SameSiteMode.None</span></span>     | <span data-ttu-id="260da-243">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-243">SameSiteMode.None</span></span><br><span data-ttu-id="260da-244">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-245">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-246">SameSiteMode.None</span></span><br><span data-ttu-id="260da-247">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-248">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="260da-249">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="260da-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-250">SameSiteMode.None</span></span><br><span data-ttu-id="260da-251">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-252">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-253">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-254">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-255">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="260da-256">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="260da-257">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="260da-257">SameSiteMode.None</span></span><br><span data-ttu-id="260da-258">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="260da-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="260da-259">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="260da-260">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="260da-261">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="260da-262">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="260da-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="260da-263">Créer un cookie d’authentification</span><span class="sxs-lookup"><span data-stu-id="260da-263">Create an authentication cookie</span></span>

<span data-ttu-id="260da-264">Pour créer un cookie contenant des informations sur l’utilisateur <xref:System.Security.Claims.ClaimsPrincipal>, construisez un.</span><span class="sxs-lookup"><span data-stu-id="260da-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="260da-265">Les informations utilisateur sont sérialisées et stockées dans le cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="260da-266">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les s <xref:System.Security.Claims.Claim>requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="260da-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="260da-267">`SignInAsync`crée un cookie chiffré et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="260da-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="260da-268">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="260da-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="260da-269">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="260da-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="260da-270">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="260da-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="260da-271">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="260da-271">Sign out</span></span>

<span data-ttu-id="260da-272">Pour déconnecter l’utilisateur actuel et supprimer son cookie, appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="260da-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="260da-273">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « cookies ») n’est pas utilisé comme modèle (par exemple, « ContosoCookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="260da-274">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="260da-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="260da-275">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="260da-275">React to back-end changes</span></span>

<span data-ttu-id="260da-276">Une fois qu’un cookie est créé, le cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="260da-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="260da-277">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="260da-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="260da-278">Le système d’authentification de cookie de l’application continue à traiter les demandes en fonction du cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="260da-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="260da-279">L’utilisateur reste connecté à l’application tant que le cookie d’authentification est valide.</span><span class="sxs-lookup"><span data-stu-id="260da-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="260da-280">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l’identité du cookie.</span><span class="sxs-lookup"><span data-stu-id="260da-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="260da-281">La validation du cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="260da-282">Une approche de la validation de cookie est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="260da-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="260da-283">Si la base de données n’a pas été modifiée depuis l’émission du cookie de l’utilisateur, il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="260da-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="260da-284">Dans l’exemple d’application, la base de données `IUserRepository` est implémentée `LastChanged` dans et stocke une valeur.</span><span class="sxs-lookup"><span data-stu-id="260da-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="260da-285">Lorsqu’un utilisateur est mis à jour dans la base `LastChanged` de données, la valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="260da-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="260da-286">Afin d’invalider un cookie lorsque la base de données change en fonction `LastChanged` de la valeur, créez le cookie `LastChanged` avec une revendication contenant `LastChanged` la valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="260da-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="260da-287">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="260da-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="260da-288">Voici un exemple d’implémentation de `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="260da-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="260da-289">Inscrivez l’instance des événements lors de l’inscription du `Startup.ConfigureServices` service de cookie dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="260da-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="260da-290">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour `CustomCookieAuthenticationEvents` votre classe :</span><span class="sxs-lookup"><span data-stu-id="260da-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="260da-291">Imaginez une situation dans laquelle le nom de l’utilisateur est&mdash;mis à jour une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="260da-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="260da-292">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non `context.ReplacePrincipal` destructrice, appelez `context.ShouldRenew` et affectez à `true`la propriété la valeur.</span><span class="sxs-lookup"><span data-stu-id="260da-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="260da-293">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="260da-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="260da-294">La validation des cookies d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="260da-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="260da-295">Cookies persistants</span><span class="sxs-lookup"><span data-stu-id="260da-295">Persistent cookies</span></span>

<span data-ttu-id="260da-296">Vous pouvez souhaiter que le cookie soit rendu persistant entre les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="260da-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="260da-297">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="260da-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="260da-298">L’extrait de code suivant crée une identité et un cookie correspondant qui subsiste à travers les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="260da-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="260da-299">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="260da-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="260da-300">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie une fois qu’il a redémarré.</span><span class="sxs-lookup"><span data-stu-id="260da-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="260da-301">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="260da-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="260da-302">Expiration absolue des cookies</span><span class="sxs-lookup"><span data-stu-id="260da-302">Absolute cookie expiration</span></span>

<span data-ttu-id="260da-303">Une heure d’expiration absolue peut être définie <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>avec.</span><span class="sxs-lookup"><span data-stu-id="260da-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="260da-304">Pour créer un cookie persistant, `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="260da-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="260da-305">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="260da-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="260da-306">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="260da-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="260da-307">L’extrait de code suivant crée une identité et un cookie correspondant qui dure 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="260da-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="260da-308">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="260da-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="260da-309">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="260da-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="260da-310">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="260da-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
