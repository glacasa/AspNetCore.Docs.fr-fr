---
title: Vue d’ensemble de l’authentification ASP.NET Core
author: mjrousos
description: En savoir plus sur l’authentification dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632640"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="febf7-103">Vue d’ensemble de l’authentification ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="febf7-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="febf7-104">Par [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="febf7-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="febf7-105">L’authentification est le processus qui consiste à déterminer l’identité d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="febf7-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="febf7-106">L' [autorisation](xref:security/authorization/introduction) est le processus qui consiste à déterminer si un utilisateur a accès à une ressource.</span><span class="sxs-lookup"><span data-stu-id="febf7-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="febf7-107">Dans ASP.NET Core, l’authentification est gérée par le `IAuthenticationService` , qui est utilisé par l' [intergiciel (middleware](xref:fundamentals/middleware/index)) d’authentification.</span><span class="sxs-lookup"><span data-stu-id="febf7-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="febf7-108">Le service d’authentification utilise des gestionnaires d’authentification inscrits pour effectuer des actions liées à l’authentification.</span><span class="sxs-lookup"><span data-stu-id="febf7-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="febf7-109">Voici quelques exemples d’actions liées à l’authentification :</span><span class="sxs-lookup"><span data-stu-id="febf7-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="febf7-110">Authentification d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="febf7-110">Authenticating a user.</span></span>
* <span data-ttu-id="febf7-111">Réponse quand un utilisateur non authentifié tente d’accéder à une ressource restreinte.</span><span class="sxs-lookup"><span data-stu-id="febf7-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="febf7-112">Les gestionnaires d’authentification inscrits et leurs options de configuration sont appelés « schémas ».</span><span class="sxs-lookup"><span data-stu-id="febf7-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="febf7-113">Les schémas d’authentification sont spécifiés en inscrivant les services d’authentification dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="febf7-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="febf7-114">En appelant une méthode d’extension spécifique au schéma après un appel à `services.AddAuthentication` (tel que `AddJwtBearer` ou `AddCookie` , par exemple).</span><span class="sxs-lookup"><span data-stu-id="febf7-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="febf7-115">Ces méthodes d’extension utilisent [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) pour inscrire des schémas avec les paramètres appropriés.</span><span class="sxs-lookup"><span data-stu-id="febf7-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="febf7-116">Moins fréquemment, en appelant [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directement.</span><span class="sxs-lookup"><span data-stu-id="febf7-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="febf7-117">Par exemple, le code suivant inscrit les services d’authentification et les gestionnaires pour cookie et les schémas d’authentification du porteur JWT :</span><span class="sxs-lookup"><span data-stu-id="febf7-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="febf7-118">Le `AddAuthentication` paramètre `JwtBearerDefaults.AuthenticationScheme` est le nom du schéma à utiliser par défaut lorsqu’un schéma spécifique n’est pas demandé.</span><span class="sxs-lookup"><span data-stu-id="febf7-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="febf7-119">Si plusieurs schémas sont utilisés, les stratégies d’autorisation (ou attributs d’autorisation) peuvent [spécifier le schéma d’authentification (ou les schémas)](xref:security/authorization/limitingidentitybyscheme) dont ils dépendent pour authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="febf7-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="febf7-120">Dans l’exemple ci-dessus, le cookie schéma d’authentification peut être utilisé en spécifiant son nom ( `CookieAuthenticationDefaults.AuthenticationScheme` par défaut, bien qu’un autre nom puisse être fourni lors de l’appel de `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="febf7-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="febf7-121">Dans certains cas, l’appel à `AddAuthentication` est automatiquement effectué par d’autres méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="febf7-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="febf7-122">Par exemple, lors de l’utilisation de [ASP.NET Core Identity](xref:security/authentication/identity) , `AddAuthentication` est appelé en interne.</span><span class="sxs-lookup"><span data-stu-id="febf7-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="febf7-123">L’intergiciel d’authentification est ajouté dans `Startup.Configure` en appelant la <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> méthode d’extension sur le de l’application `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="febf7-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="febf7-124">`UseAuthentication`L’appel de enregistre l’intergiciel (middleware) qui utilise les schémas d’authentification précédemment enregistrés.</span><span class="sxs-lookup"><span data-stu-id="febf7-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="febf7-125">Appelez `UseAuthentication` avant tout middleware dépendant des utilisateurs authentifiés.</span><span class="sxs-lookup"><span data-stu-id="febf7-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="febf7-126">Lorsque vous utilisez le routage de point de terminaison, l’appel à `UseAuthentication` doit atteindre :</span><span class="sxs-lookup"><span data-stu-id="febf7-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="febf7-127">Après `UseRouting` , afin que les informations de routage soient disponibles pour les décisions d’authentification.</span><span class="sxs-lookup"><span data-stu-id="febf7-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="febf7-128">Avant `UseEndpoints` , afin que les utilisateurs soient authentifiés avant d’accéder aux points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="febf7-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="febf7-129">Concepts d’authentification</span><span class="sxs-lookup"><span data-stu-id="febf7-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="febf7-130">Schéma d'authentification</span><span class="sxs-lookup"><span data-stu-id="febf7-130">Authentication scheme</span></span>

<span data-ttu-id="febf7-131">Un schéma d’authentification est un nom qui correspond à :</span><span class="sxs-lookup"><span data-stu-id="febf7-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="febf7-132">Gestionnaire d'authentifications.</span><span class="sxs-lookup"><span data-stu-id="febf7-132">An authentication handler.</span></span>
* <span data-ttu-id="febf7-133">Options de configuration de cette instance spécifique du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="febf7-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="febf7-134">Les schémas sont utiles comme un mécanisme permettant de faire référence aux comportements d’authentification, de stimulation et d’interdiction du gestionnaire associé.</span><span class="sxs-lookup"><span data-stu-id="febf7-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="febf7-135">Par exemple, une stratégie d’autorisation peut utiliser des noms de schémas pour spécifier le (s) schéma (s) d’authentification à utiliser pour authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="febf7-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="febf7-136">Lors de la configuration de l’authentification, il est courant de spécifier le schéma d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="febf7-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="febf7-137">Le schéma par défaut est utilisé, sauf si une ressource demande un schéma spécifique.</span><span class="sxs-lookup"><span data-stu-id="febf7-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="febf7-138">Il est également possible d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="febf7-138">It's also possible to:</span></span>

* <span data-ttu-id="febf7-139">Spécifiez différents schémas par défaut à utiliser pour les actions Authenticate, Challenge et interdire.</span><span class="sxs-lookup"><span data-stu-id="febf7-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="febf7-140">Combiner plusieurs schémas en un seul à l’aide de [modèles de stratégie](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="febf7-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="febf7-141">Gestionnaire d’authentification</span><span class="sxs-lookup"><span data-stu-id="febf7-141">Authentication handler</span></span>

<span data-ttu-id="febf7-142">Un gestionnaire d’authentification :</span><span class="sxs-lookup"><span data-stu-id="febf7-142">An authentication handler:</span></span>

* <span data-ttu-id="febf7-143">Est un type qui implémente le comportement d’un schéma.</span><span class="sxs-lookup"><span data-stu-id="febf7-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="febf7-144">Est dérivée de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="febf7-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="febf7-145">A la responsabilité principale d’authentifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="febf7-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="febf7-146">En fonction de la configuration du schéma d’authentification et du contexte de la requête entrante, les gestionnaires d’authentification :</span><span class="sxs-lookup"><span data-stu-id="febf7-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="febf7-147">Construisez <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> des objets représentant l’identité de l’utilisateur si l’authentification réussit.</span><span class="sxs-lookup"><span data-stu-id="febf7-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="febf7-148">Retourne’no result’ou’Failure’si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="febf7-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="febf7-149">Utilisez des méthodes pour les actions de stimulation et de interdiction lorsque les utilisateurs tentent d’accéder aux ressources :</span><span class="sxs-lookup"><span data-stu-id="febf7-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="febf7-150">Ils ne sont pas autorisés à accéder (interdire).</span><span class="sxs-lookup"><span data-stu-id="febf7-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="febf7-151">Lorsqu’ils ne sont pas authentifiés (Challenge).</span><span class="sxs-lookup"><span data-stu-id="febf7-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="febf7-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="febf7-152">Authenticate</span></span>

<span data-ttu-id="febf7-153">L’action d’authentification d’un schéma d’authentification est responsable de la construction de l’identité de l’utilisateur en fonction du contexte de la requête.</span><span class="sxs-lookup"><span data-stu-id="febf7-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="febf7-154">Elle retourne un <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> qui indique si l’authentification a réussi et, le cas échéant, l’identité de l’utilisateur dans un ticket d’authentification.</span><span class="sxs-lookup"><span data-stu-id="febf7-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="febf7-155">Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="febf7-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="febf7-156">Les exemples d’authentification sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="febf7-156">Authenticate examples include:</span></span>

* <span data-ttu-id="febf7-157">Un cookie schéma d’authentification qui construit l’identité de l’utilisateur à partir de cookie s.</span><span class="sxs-lookup"><span data-stu-id="febf7-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="febf7-158">Un modèle de porteur JWT qui désérialise et valide un jeton de porteur JWT pour construire l’identité de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="febf7-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="febf7-159">Problème</span><span class="sxs-lookup"><span data-stu-id="febf7-159">Challenge</span></span>

<span data-ttu-id="febf7-160">Une demande d’authentification est appelée par l’autorisation lorsqu’un utilisateur non authentifié demande un point de terminaison qui requiert une authentification.</span><span class="sxs-lookup"><span data-stu-id="febf7-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="febf7-161">Une demande d’authentification est émise, par exemple, lorsqu’un utilisateur anonyme demande une ressource restreinte ou clique sur un lien de connexion.</span><span class="sxs-lookup"><span data-stu-id="febf7-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="febf7-162">L’autorisation appelle un défi à l’aide du ou des schémas d’authentification spécifiés, ou la valeur par défaut si aucun n’est spécifié.</span><span class="sxs-lookup"><span data-stu-id="febf7-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="febf7-163">Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="febf7-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="febf7-164">Les exemples de demande d’authentification sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="febf7-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="febf7-165">cookieSchéma d’authentification redirigeant l’utilisateur vers une page de connexion.</span><span class="sxs-lookup"><span data-stu-id="febf7-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="febf7-166">Un modèle de porteur JWT renvoyant un résultat 401 avec un `www-authenticate: bearer` en-tête.</span><span class="sxs-lookup"><span data-stu-id="febf7-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="febf7-167">Une action de stimulation doit permettre à l’utilisateur de connaître le mécanisme d’authentification à utiliser pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="febf7-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="febf7-168">Disant</span><span class="sxs-lookup"><span data-stu-id="febf7-168">Forbid</span></span>

<span data-ttu-id="febf7-169">L’action interdire d’un schéma d’authentification est appelée par l’autorisation lorsqu’un utilisateur authentifié tente d’accéder à une ressource à laquelle il n’est pas autorisé à accéder.</span><span class="sxs-lookup"><span data-stu-id="febf7-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="febf7-170">Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="febf7-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="febf7-171">Les exemples d’authentifications interdits sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="febf7-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="febf7-172">cookieSchéma d’authentification redirigeant l’utilisateur vers une page indiquant que l’accès a été interdit.</span><span class="sxs-lookup"><span data-stu-id="febf7-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="febf7-173">Un schéma de porteur JWT renvoyant un résultat 403.</span><span class="sxs-lookup"><span data-stu-id="febf7-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="febf7-174">Schéma d’authentification personnalisé redirigeant vers une page dans laquelle l’utilisateur peut demander l’accès à la ressource.</span><span class="sxs-lookup"><span data-stu-id="febf7-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="febf7-175">Une action interdire peut informer l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="febf7-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="febf7-176">Ils sont authentifiés.</span><span class="sxs-lookup"><span data-stu-id="febf7-176">They are authenticated.</span></span>
* <span data-ttu-id="febf7-177">Ils ne sont pas autorisés à accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="febf7-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="febf7-178">Consultez les liens suivants pour connaître les différences entre la stimulation et l’interdiction :</span><span class="sxs-lookup"><span data-stu-id="febf7-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="febf7-179">[Défier et interdire l’utilisation d’un gestionnaire de ressources opérationnelles](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="febf7-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="febf7-180">[Différences entre la stimulation et](xref:security/authorization/secure-data#challenge)l’interdiction.</span><span class="sxs-lookup"><span data-stu-id="febf7-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="febf7-181">Fournisseurs d’authentification par locataire</span><span class="sxs-lookup"><span data-stu-id="febf7-181">Authentication providers per tenant</span></span>

<span data-ttu-id="febf7-182">ASP.NET Core Framework n’a pas de solution intégrée pour l’authentification mutualisée.</span><span class="sxs-lookup"><span data-stu-id="febf7-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="febf7-183">Bien qu’il soit possible pour les clients d’en écrire un, à l’aide des fonctionnalités intégrées, nous recommandons aux clients d’examiner le [noyau du verger](https://www.orchardcore.net/) à cet effet.</span><span class="sxs-lookup"><span data-stu-id="febf7-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="febf7-184">Noyau du verger :</span><span class="sxs-lookup"><span data-stu-id="febf7-184">Orchard Core is:</span></span>

* <span data-ttu-id="febf7-185">Une infrastructure d’application modulaire et mutualisée Open source générée avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="febf7-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="febf7-186">Un système de gestion de contenu (CMS) basé sur ce Framework d’application.</span><span class="sxs-lookup"><span data-stu-id="febf7-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="febf7-187">Consultez la source [principale du verger](https://github.com/OrchardCMS/OrchardCore) pour obtenir un exemple de fournisseurs d’authentification par client.</span><span class="sxs-lookup"><span data-stu-id="febf7-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="febf7-188">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="febf7-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="febf7-189">Exiger globalement des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="febf7-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)