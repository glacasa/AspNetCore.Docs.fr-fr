---
title: Empêcher les attaques de falsification de requête intersites (XSRF/CSRF) dans ASP.NET Core
author: steve-smith
description: Découvrez comment empêcher les attaques contre les applications Web où un site Web malveillant peut influencer l’interaction entre un navigateur client et l’application.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 4e7e7a89daaee533f648efdb2c621399225f57be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774002"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="ec476-103">Empêcher les attaques de falsification de requête intersites (XSRF/CSRF) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec476-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="ec476-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ec476-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ec476-105">La falsification de requête intersite (également appelée XSRF ou CSRF) est une attaque contre les applications hébergées sur le Web, dans laquelle une application Web malveillante peut influencer l’interaction entre un navigateur client et une application Web qui approuve ce navigateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="ec476-106">Ces attaques sont possibles, car les navigateurs Web envoient automatiquement certains types de jetons d’authentification à chaque demande adressée à un site Web.</span><span class="sxs-lookup"><span data-stu-id="ec476-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="ec476-107">Cette forme d’exploitation est également connue sous le nom d' *attaque en un clic* ou d’une *session* , car l’attaque tire parti de la session précédemment authentifiée de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="ec476-108">Exemple d’attaque CSRF :</span><span class="sxs-lookup"><span data-stu-id="ec476-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="ec476-109">Un utilisateur se connecte `www.good-banking-site.com` à à l’aide de l’authentification par formulaire.</span><span class="sxs-lookup"><span data-stu-id="ec476-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="ec476-110">Le serveur authentifie l’utilisateur et émet une réponse qui comprend un cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ec476-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="ec476-111">Le site est vulnérable aux attaques, car il approuve les demandes qu’il reçoit avec un cookie d’authentification valide.</span><span class="sxs-lookup"><span data-stu-id="ec476-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="ec476-112">L’utilisateur visite un site malveillant, `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="ec476-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="ec476-113">Le site malveillant, `www.bad-crook-site.com`, contient un formulaire HTML semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="ec476-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="ec476-114">Notez que le formulaire est `action` publié sur le site vulnérable, et non sur le site malveillant.</span><span class="sxs-lookup"><span data-stu-id="ec476-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="ec476-115">Il s’agit de la partie « inter-sites » de CSRF.</span><span class="sxs-lookup"><span data-stu-id="ec476-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="ec476-116">L’utilisateur sélectionne le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="ec476-116">The user selects the submit button.</span></span> <span data-ttu-id="ec476-117">Le navigateur effectue la demande et intègre automatiquement le cookie d’authentification pour le domaine demandé `www.good-banking-site.com`,.</span><span class="sxs-lookup"><span data-stu-id="ec476-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="ec476-118">La demande s’exécute sur `www.good-banking-site.com` le serveur avec le contexte d’authentification de l’utilisateur et peut effectuer toute action qu’un utilisateur authentifié est autorisé à effectuer.</span><span class="sxs-lookup"><span data-stu-id="ec476-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="ec476-119">En plus du scénario dans lequel l’utilisateur sélectionne le bouton pour envoyer le formulaire, le site malveillant peut :</span><span class="sxs-lookup"><span data-stu-id="ec476-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="ec476-120">Exécutez un script qui envoie automatiquement le formulaire.</span><span class="sxs-lookup"><span data-stu-id="ec476-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="ec476-121">Envoyer l’envoi de formulaire en tant que requête AJAX.</span><span class="sxs-lookup"><span data-stu-id="ec476-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="ec476-122">Masquez le formulaire en utilisant CSS.</span><span class="sxs-lookup"><span data-stu-id="ec476-122">Hide the form using CSS.</span></span>

<span data-ttu-id="ec476-123">Ces scénarios alternatifs ne nécessitent aucune action ou entrée de la part de l’utilisateur autre que la visite initiale du site malveillant.</span><span class="sxs-lookup"><span data-stu-id="ec476-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="ec476-124">L’utilisation de HTTPs n’empêche pas une attaque CSRF.</span><span class="sxs-lookup"><span data-stu-id="ec476-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="ec476-125">Le site malveillant peut envoyer une `https://www.good-banking-site.com/` demande tout aussi facilement qu’il peut envoyer une demande non sécurisée.</span><span class="sxs-lookup"><span data-stu-id="ec476-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="ec476-126">Certaines attaques ciblent des points de terminaison qui répondent aux demandes d’extraction, auquel cas une balise d’image peut être utilisée pour effectuer l’action.</span><span class="sxs-lookup"><span data-stu-id="ec476-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="ec476-127">Cette forme d’attaque est courante sur les sites de forum qui autorisent les images, mais qui bloquent JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec476-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="ec476-128">Les applications qui changent d’État sur les demandes d’extraction, où les variables ou les ressources sont modifiées, sont vulnérables aux attaques malveillantes.</span><span class="sxs-lookup"><span data-stu-id="ec476-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="ec476-129">**Demandes d’obtention dont l’état de modification n’est pas sécurisé. Une meilleure pratique consiste à ne jamais modifier l’état d’une requête d’extraction.**</span><span class="sxs-lookup"><span data-stu-id="ec476-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="ec476-130">Les attaques CSRF sont possibles pour les applications Web qui utilisent des cookies pour l’authentification, car :</span><span class="sxs-lookup"><span data-stu-id="ec476-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="ec476-131">Les navigateurs stockent les cookies émis par une application Web.</span><span class="sxs-lookup"><span data-stu-id="ec476-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="ec476-132">Les cookies stockés incluent des cookies de session pour les utilisateurs authentifiés.</span><span class="sxs-lookup"><span data-stu-id="ec476-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="ec476-133">Les navigateurs envoient tous les cookies associés à un domaine à l’application Web à chaque demande, quelle que soit la façon dont la demande à l’application a été générée dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="ec476-134">Toutefois, les attaques CSRF ne sont pas limitées à l’exploitation des cookies.</span><span class="sxs-lookup"><span data-stu-id="ec476-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="ec476-135">Par exemple, l’authentification de base et Digest est également vulnérable.</span><span class="sxs-lookup"><span data-stu-id="ec476-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="ec476-136">Une fois qu’un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les&dagger; informations d’identification jusqu’à la fin de la session.</span><span class="sxs-lookup"><span data-stu-id="ec476-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="ec476-137">&dagger;Dans ce contexte, la *session* fait référence à la session côté client au cours de laquelle l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="ec476-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="ec476-138">Elle n’est pas liée aux sessions côté serveur ou aux intergiciels (middleware) de [Session ASP.net Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="ec476-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="ec476-139">Les utilisateurs peuvent se protéger contre les vulnérabilités de CSRF en acceptant les précautions suivantes :</span><span class="sxs-lookup"><span data-stu-id="ec476-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="ec476-140">Déconnectez-vous de Web Apps une fois que vous avez fini de les utiliser.</span><span class="sxs-lookup"><span data-stu-id="ec476-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="ec476-141">Effacez régulièrement les cookies du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="ec476-142">Toutefois, les vulnérabilités CSRF sont fondamentalement un problème avec l’application Web, et non l’utilisateur final.</span><span class="sxs-lookup"><span data-stu-id="ec476-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="ec476-143">Concepts de base de l’authentification</span><span class="sxs-lookup"><span data-stu-id="ec476-143">Authentication fundamentals</span></span>

<span data-ttu-id="ec476-144">L’authentification basée sur les cookies est une forme d’authentification courante.</span><span class="sxs-lookup"><span data-stu-id="ec476-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="ec476-145">Les systèmes d’authentification basés sur les jetons augmentent en popularité, en particulier pour les applications à page unique (SPAs).</span><span class="sxs-lookup"><span data-stu-id="ec476-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="ec476-146">Authentification basée sur les cookies</span><span class="sxs-lookup"><span data-stu-id="ec476-146">Cookie-based authentication</span></span>

<span data-ttu-id="ec476-147">Lorsqu’un utilisateur s’authentifie à l’aide de son nom d’utilisateur et de son mot de passe, il reçoit un jeton qui contient un ticket d’authentification pouvant être utilisé pour l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="ec476-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="ec476-148">Le jeton est stocké sous la forme d’un cookie qui accompagne chaque requête du client.</span><span class="sxs-lookup"><span data-stu-id="ec476-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="ec476-149">La génération et la validation de ce cookie sont effectuées par l’intergiciel (middleware) d’authentification des cookies.</span><span class="sxs-lookup"><span data-stu-id="ec476-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="ec476-150">L' [intergiciel](xref:fundamentals/middleware/index) sérialise un principal d’utilisateur dans un cookie chiffré.</span><span class="sxs-lookup"><span data-stu-id="ec476-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="ec476-151">Lors des demandes suivantes, l’intergiciel valide le cookie, recrée le principal et attribue le principal à la propriété [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="ec476-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="ec476-152">Authentification basée sur les jetons</span><span class="sxs-lookup"><span data-stu-id="ec476-152">Token-based authentication</span></span>

<span data-ttu-id="ec476-153">Lorsqu’un utilisateur est authentifié, il reçoit un jeton (et non un jeton anti-contrefaçon).</span><span class="sxs-lookup"><span data-stu-id="ec476-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="ec476-154">Le jeton contient des informations utilisateur sous la forme de [revendications](/dotnet/framework/security/claims-based-identity-model) ou d’un jeton de référence qui fait pointer l’application vers l’état utilisateur géré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ec476-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="ec476-155">Lorsqu’un utilisateur tente d’accéder à une ressource nécessitant une authentification, le jeton est envoyé à l’application avec un en-tête d’autorisation supplémentaire sous forme de jeton du porteur.</span><span class="sxs-lookup"><span data-stu-id="ec476-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="ec476-156">Cela rend l’application sans État.</span><span class="sxs-lookup"><span data-stu-id="ec476-156">This makes the app stateless.</span></span> <span data-ttu-id="ec476-157">Dans chaque requête suivante, le jeton est transmis dans la demande de validation côté serveur.</span><span class="sxs-lookup"><span data-stu-id="ec476-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="ec476-158">Ce jeton n’est pas *chiffré*. elle est *encodée*.</span><span class="sxs-lookup"><span data-stu-id="ec476-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="ec476-159">Sur le serveur, le jeton est décodé pour accéder à ses informations.</span><span class="sxs-lookup"><span data-stu-id="ec476-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="ec476-160">Pour envoyer le jeton sur les demandes suivantes, stockez le jeton dans le stockage local du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="ec476-161">Ne vous inquiétez pas de la vulnérabilité CSRF si le jeton est stocké dans le stockage local du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ec476-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="ec476-162">CSRF est un problème lorsque le jeton est stocké dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="ec476-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="ec476-163">Pour plus d’informations, consultez l’exemple de code GitHub problème [Spa ajoute deux cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="ec476-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="ec476-164">Plusieurs applications hébergées sur un domaine</span><span class="sxs-lookup"><span data-stu-id="ec476-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="ec476-165">Les environnements d’hébergement partagés sont vulnérables au détournement de session, à la CSRF de connexion et à d’autres attaques.</span><span class="sxs-lookup"><span data-stu-id="ec476-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="ec476-166">Bien `example1.contoso.net` que `example2.contoso.net` et soient des hôtes différents, il existe une relation d’approbation implicite `*.contoso.net` entre les hôtes sous le domaine.</span><span class="sxs-lookup"><span data-stu-id="ec476-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="ec476-167">Cette relation d’approbation implicite permet aux hôtes potentiellement non fiables d’affecter les cookies de l’autre (les stratégies de même origine qui régissent les demandes AJAX ne s’appliquent pas nécessairement aux cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="ec476-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="ec476-168">Les attaques qui exploitent des cookies approuvés entre des applications hébergées sur le même domaine peuvent être évitées en ne partageant pas de domaines.</span><span class="sxs-lookup"><span data-stu-id="ec476-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="ec476-169">Lorsque chaque application est hébergée sur son propre domaine, il n’existe aucune relation d’approbation de cookie implicite à exploiter.</span><span class="sxs-lookup"><span data-stu-id="ec476-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="ec476-170">Configuration de l’anti-contrefaçon ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec476-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="ec476-171">ASP.NET Core implémente l’anti-falsification à l’aide de la [protection des données ASP.net Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="ec476-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ec476-172">La pile de protection des données doit être configurée pour fonctionner dans une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="ec476-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="ec476-173">Pour plus d’informations, consultez Configuration de la [protection des données](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="ec476-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec476-174">L’intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque l’une des API suivantes est `Startup.ConfigureServices`appelée dans :</span><span class="sxs-lookup"><span data-stu-id="ec476-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec476-175">Un intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> est appelé dans`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ec476-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="ec476-176">Dans ASP.NET Core 2,0 ou version ultérieure, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons anti-contrefaçon dans des éléments de formulaire HTML.</span><span class="sxs-lookup"><span data-stu-id="ec476-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="ec476-177">Le balisage suivant dans un fichier Razor génère automatiquement des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="ec476-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="ec476-178">De même, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) génère des jetons anti-contrefaçon par défaut si la méthode du formulaire n’est pas obtenue.</span><span class="sxs-lookup"><span data-stu-id="ec476-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="ec476-179">La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML se produit `<form>` lorsque la balise contient l' `method="post"` attribut et que l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="ec476-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="ec476-180">L’attribut action est vide (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="ec476-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="ec476-181">L’attribut action n’est pas`<form method="post">`fourni ().</span><span class="sxs-lookup"><span data-stu-id="ec476-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="ec476-182">La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML peut être désactivée :</span><span class="sxs-lookup"><span data-stu-id="ec476-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="ec476-183">Désactivez explicitement les jetons anti-contrefaçon `asp-antiforgery` avec l’attribut :</span><span class="sxs-lookup"><span data-stu-id="ec476-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="ec476-184">L’élément de formulaire est exclu du balisage tag à l’aide du tag Helper [! symbol opt-out](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="ec476-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="ec476-185">Supprimez `FormTagHelper` de la vue.</span><span class="sxs-lookup"><span data-stu-id="ec476-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="ec476-186">`FormTagHelper` Peut être supprimé d’une vue en ajoutant la directive suivante à la vue Razor :</span><span class="sxs-lookup"><span data-stu-id="ec476-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="ec476-187">Les [Razor pages](xref:razor-pages/index) sont automatiquement protégées à partir de XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="ec476-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="ec476-188">Pour plus d’informations, consultez [XSRF/CSRF et Razor pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="ec476-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="ec476-189">L’approche la plus courante pour la protection contre les attaques CSRF consiste à utiliser le *modèle de jeton du synchronisateur* (STP).</span><span class="sxs-lookup"><span data-stu-id="ec476-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="ec476-190">Le protocole STP est utilisé lorsque l’utilisateur demande une page avec des données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="ec476-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="ec476-191">Le serveur envoie un jeton associé à l’identité de l’utilisateur actuel au client.</span><span class="sxs-lookup"><span data-stu-id="ec476-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="ec476-192">Le client renvoie le jeton au serveur pour vérification.</span><span class="sxs-lookup"><span data-stu-id="ec476-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="ec476-193">Si le serveur reçoit un jeton qui ne correspond pas à l’identité de l’utilisateur authentifié, la demande est rejetée.</span><span class="sxs-lookup"><span data-stu-id="ec476-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="ec476-194">Le jeton est unique et imprévisible.</span><span class="sxs-lookup"><span data-stu-id="ec476-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="ec476-195">Le jeton peut également être utilisé pour garantir le séquencement correct d’une série de requêtes (par exemple, en vérifiant la séquence de demande &ndash; de : &ndash; page 1 page 2 page 3).</span><span class="sxs-lookup"><span data-stu-id="ec476-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="ec476-196">Toutes les formes dans ASP.NET Core modèles MVC et Razor Pages génèrent des jetons anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="ec476-197">Les deux exemples de vue suivants génèrent des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="ec476-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="ec476-198">Ajoutez explicitement un jeton anti-contrefaçon à un `<form>` élément sans utiliser de balise tag avec le programme d' [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)assistance HTML :</span><span class="sxs-lookup"><span data-stu-id="ec476-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="ec476-199">Dans chacun des cas précédents, ASP.NET Core ajoute un champ de formulaire masqué semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="ec476-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="ec476-200">ASP.NET Core comprend trois [filtres](xref:mvc/controllers/filters) pour l’utilisation des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="ec476-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="ec476-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="ec476-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="ec476-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="ec476-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="ec476-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="ec476-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="ec476-204">Options anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="ec476-204">Antiforgery options</span></span>

<span data-ttu-id="ec476-205">Personnaliser les [options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) anti-contrefaçon `Startup.ConfigureServices`dans :</span><span class="sxs-lookup"><span data-stu-id="ec476-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="ec476-206">&dagger;Définissez les `Cookie` propriétés anti-contrefaçon à l’aide des propriétés de la classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="ec476-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="ec476-207">Option</span><span class="sxs-lookup"><span data-stu-id="ec476-207">Option</span></span> | <span data-ttu-id="ec476-208">Description</span><span class="sxs-lookup"><span data-stu-id="ec476-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ec476-209">Cookie</span><span class="sxs-lookup"><span data-stu-id="ec476-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="ec476-210">Détermine les paramètres utilisés pour créer les cookies anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="ec476-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="ec476-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="ec476-212">Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues.</span><span class="sxs-lookup"><span data-stu-id="ec476-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="ec476-213">HeaderName</span><span class="sxs-lookup"><span data-stu-id="ec476-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="ec476-214">Nom de l’en-tête utilisé par le système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="ec476-215">Si `null`la condition est, le système considère uniquement les données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="ec476-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="ec476-216">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="ec476-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="ec476-217">Spécifie s’il faut supprimer la `X-Frame-Options` génération de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="ec476-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="ec476-218">Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ».</span><span class="sxs-lookup"><span data-stu-id="ec476-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="ec476-219">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="ec476-219">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="ec476-220">Option</span><span class="sxs-lookup"><span data-stu-id="ec476-220">Option</span></span> | <span data-ttu-id="ec476-221">Description</span><span class="sxs-lookup"><span data-stu-id="ec476-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ec476-222">Cookie</span><span class="sxs-lookup"><span data-stu-id="ec476-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="ec476-223">Détermine les paramètres utilisés pour créer les cookies anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="ec476-224">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="ec476-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="ec476-225">Le domaine du cookie.</span><span class="sxs-lookup"><span data-stu-id="ec476-225">The domain of the cookie.</span></span> <span data-ttu-id="ec476-226">La valeur par défaut est `null`.</span><span class="sxs-lookup"><span data-stu-id="ec476-226">Defaults to `null`.</span></span> <span data-ttu-id="ec476-227">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ec476-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ec476-228">L’alternative recommandée est cookie. domain.</span><span class="sxs-lookup"><span data-stu-id="ec476-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="ec476-229">CookieName</span><span class="sxs-lookup"><span data-stu-id="ec476-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="ec476-230">Nom du cookie.</span><span class="sxs-lookup"><span data-stu-id="ec476-230">The name of the cookie.</span></span> <span data-ttu-id="ec476-231">Si la valeur n’est pas définie, le système génère un nom unique à partir de [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (». AspNetCore. anti-contrefaçon.»).</span><span class="sxs-lookup"><span data-stu-id="ec476-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="ec476-232">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ec476-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ec476-233">L’alternative recommandée est Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="ec476-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="ec476-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="ec476-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="ec476-235">Chemin d’accès défini sur le cookie.</span><span class="sxs-lookup"><span data-stu-id="ec476-235">The path set on the cookie.</span></span> <span data-ttu-id="ec476-236">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ec476-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ec476-237">L’alternative recommandée est cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="ec476-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="ec476-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="ec476-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="ec476-239">Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues.</span><span class="sxs-lookup"><span data-stu-id="ec476-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="ec476-240">HeaderName</span><span class="sxs-lookup"><span data-stu-id="ec476-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="ec476-241">Nom de l’en-tête utilisé par le système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="ec476-242">Si `null`la condition est, le système considère uniquement les données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="ec476-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="ec476-243">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="ec476-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="ec476-244">Spécifie si le système anti-contrefaçon exige le protocole HTTPs.</span><span class="sxs-lookup"><span data-stu-id="ec476-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="ec476-245">Si `true`la, les demandes non-HTTPS échouent.</span><span class="sxs-lookup"><span data-stu-id="ec476-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="ec476-246">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="ec476-246">Defaults to `false`.</span></span> <span data-ttu-id="ec476-247">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ec476-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ec476-248">L’alternative recommandée consiste à définir cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="ec476-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="ec476-249">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="ec476-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="ec476-250">Spécifie s’il faut supprimer la `X-Frame-Options` génération de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="ec476-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="ec476-251">Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ».</span><span class="sxs-lookup"><span data-stu-id="ec476-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="ec476-252">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="ec476-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="ec476-253">Pour plus d’informations, consultez [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="ec476-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="ec476-254">Configurer des fonctionnalités anti-contrefaçon avec IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="ec476-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="ec476-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fournit l’API pour configurer les fonctionnalités anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="ec476-256">`IAntiforgery`peut être demandé dans la `Configure` méthode de la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="ec476-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="ec476-257">L’exemple suivant utilise l’intergiciel (middleware) de la page d’hébergement de l’application pour générer un jeton anti-contrefaçon et l’envoyer dans la réponse en tant que cookie (à l’aide de la Convention d’affectation de noms angulaire par défaut décrite plus loin dans cette rubrique) :</span><span class="sxs-lookup"><span data-stu-id="ec476-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="ec476-258">Exiger une validation anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="ec476-258">Require antiforgery validation</span></span>

<span data-ttu-id="ec476-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) est un filtre d’action qui peut être appliqué à une action individuelle, à un contrôleur ou globalement.</span><span class="sxs-lookup"><span data-stu-id="ec476-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="ec476-260">Les demandes adressées aux actions auxquelles ce filtre est appliqué sont bloquées, sauf si la demande comprend un jeton anti-contrefaçon valide.</span><span class="sxs-lookup"><span data-stu-id="ec476-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="ec476-261">L' `ValidateAntiForgeryToken` attribut requiert un jeton pour les demandes aux méthodes d’action qu’il marque, y compris les requêtes http d’extraction.</span><span class="sxs-lookup"><span data-stu-id="ec476-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="ec476-262">Si l' `ValidateAntiForgeryToken` attribut est appliqué sur les contrôleurs de l’application, il peut être substitué par `IgnoreAntiforgeryToken` l’attribut.</span><span class="sxs-lookup"><span data-stu-id="ec476-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="ec476-263">ASP.NET Core ne prend pas en charge l’ajout de jetons anti-contrefaçon pour la récupération automatique des demandes.</span><span class="sxs-lookup"><span data-stu-id="ec476-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="ec476-264">Valider automatiquement les jetons anti-contrefaçon pour les méthodes HTTP non sécurisées uniquement</span><span class="sxs-lookup"><span data-stu-id="ec476-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="ec476-265">ASP.NET Core applications ne génèrent pas de jetons anti-contrefaçon pour les méthodes HTTP sécurisées (obtenir, tête, OPTIONS et TRACE).</span><span class="sxs-lookup"><span data-stu-id="ec476-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="ec476-266">Au lieu d’appliquer globalement l' `ValidateAntiForgeryToken` attribut, puis de le remplacer par `IgnoreAntiforgeryToken` des attributs, l’attribut [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="ec476-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="ec476-267">Cet attribut fonctionne de la même façon `ValidateAntiForgeryToken` que l’attribut, sauf qu’il ne requiert pas de jetons pour les demandes effectuées à l’aide des méthodes http suivantes :</span><span class="sxs-lookup"><span data-stu-id="ec476-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="ec476-268">GET</span><span class="sxs-lookup"><span data-stu-id="ec476-268">GET</span></span>
* <span data-ttu-id="ec476-269">HEAD</span><span class="sxs-lookup"><span data-stu-id="ec476-269">HEAD</span></span>
* <span data-ttu-id="ec476-270">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="ec476-270">OPTIONS</span></span>
* <span data-ttu-id="ec476-271">TRACE</span><span class="sxs-lookup"><span data-stu-id="ec476-271">TRACE</span></span>

<span data-ttu-id="ec476-272">Nous vous recommandons d' `AutoValidateAntiforgeryToken` utiliser large pour les scénarios non-API.</span><span class="sxs-lookup"><span data-stu-id="ec476-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="ec476-273">Cela garantit que les actions de publication sont protégées par défaut.</span><span class="sxs-lookup"><span data-stu-id="ec476-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="ec476-274">L’alternative consiste à ignorer les jetons anti-contrefaçon par défaut, sauf `ValidateAntiForgeryToken` si est appliqué à des méthodes d’action individuelles.</span><span class="sxs-lookup"><span data-stu-id="ec476-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="ec476-275">Dans ce scénario, il est plus probable qu’une méthode d’action de publication reste non protégée par erreur, laissant l’application vulnérable aux attaques CSRF.</span><span class="sxs-lookup"><span data-stu-id="ec476-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="ec476-276">Toutes les publications doivent envoyer le jeton anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="ec476-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="ec476-277">Les API ne disposent pas d’un mécanisme automatique pour l’envoi de la partie non-cookie du jeton.</span><span class="sxs-lookup"><span data-stu-id="ec476-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="ec476-278">L’implémentation dépend probablement de l’implémentation du code client.</span><span class="sxs-lookup"><span data-stu-id="ec476-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="ec476-279">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="ec476-279">Some examples are shown below:</span></span>

<span data-ttu-id="ec476-280">Exemple de niveau de classe :</span><span class="sxs-lookup"><span data-stu-id="ec476-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="ec476-281">Exemple global :</span><span class="sxs-lookup"><span data-stu-id="ec476-281">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec476-282">services. AddMvc (options = options de>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="ec476-282">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="ec476-283">Remplacer les attributs globaux ou anti-contrefaçon de contrôleur</span><span class="sxs-lookup"><span data-stu-id="ec476-283">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="ec476-284">Le filtre [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) est utilisé pour éliminer la nécessité d’un jeton anti-contrefaçon pour une action donnée (ou un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="ec476-284">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="ec476-285">Lorsqu’il est appliqué, ce filtre remplace `ValidateAntiForgeryToken` les `AutoValidateAntiforgeryToken` filtres et spécifiés à un niveau supérieur (globalement ou sur un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="ec476-285">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="ec476-286">Actualiser les jetons après l’authentification</span><span class="sxs-lookup"><span data-stu-id="ec476-286">Refresh tokens after authentication</span></span>

<span data-ttu-id="ec476-287">Les jetons doivent être actualisés une fois l’utilisateur authentifié, en redirigeant l’utilisateur vers une page d’affichage Razor ou de pages.</span><span class="sxs-lookup"><span data-stu-id="ec476-287">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="ec476-288">JavaScript, AJAX et SPAs</span><span class="sxs-lookup"><span data-stu-id="ec476-288">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="ec476-289">Dans les applications HTML traditionnelles, les jetons anti-contrefaçon sont transmis au serveur à l’aide de champs de formulaire masqués.</span><span class="sxs-lookup"><span data-stu-id="ec476-289">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="ec476-290">Dans les applications basées sur JavaScript modernes et en SPAs, de nombreuses demandes sont effectuées par programme.</span><span class="sxs-lookup"><span data-stu-id="ec476-290">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="ec476-291">Ces demandes AJAX peuvent utiliser d’autres techniques (comme les en-têtes de demande ou les cookies) pour envoyer le jeton.</span><span class="sxs-lookup"><span data-stu-id="ec476-291">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="ec476-292">Si les cookies sont utilisés pour stocker les jetons d’authentification et pour authentifier les demandes d’API sur le serveur, CSRF est un problème potentiel.</span><span class="sxs-lookup"><span data-stu-id="ec476-292">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="ec476-293">Si le stockage local est utilisé pour stocker le jeton, la vulnérabilité CSRF peut être atténuée car les valeurs du stockage local ne sont pas envoyées automatiquement au serveur avec chaque demande.</span><span class="sxs-lookup"><span data-stu-id="ec476-293">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="ec476-294">Par conséquent, l’utilisation du stockage local pour stocker le jeton anti-contrefaçon sur le client et l’envoi du jeton en tant qu’en-tête de demande est une approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="ec476-294">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="ec476-295">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ec476-295">JavaScript</span></span>

<span data-ttu-id="ec476-296">À l’aide de JavaScript avec des vues, le jeton peut être créé à l’aide d’un service à partir de la vue.</span><span class="sxs-lookup"><span data-stu-id="ec476-296">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="ec476-297">Injectez le service [Microsoft. AspNetCore. anticontrefaçon. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) dans la vue et appelez [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="ec476-297">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="ec476-298">Cette approche élimine le besoin de traiter directement les cookies du serveur ou de les lire à partir du client.</span><span class="sxs-lookup"><span data-stu-id="ec476-298">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="ec476-299">L’exemple précédent utilise JavaScript pour lire la valeur du champ masqué pour l’en-tête de publication AJAX.</span><span class="sxs-lookup"><span data-stu-id="ec476-299">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="ec476-300">JavaScript peut également accéder à des jetons dans des cookies et utiliser le contenu du cookie pour créer un en-tête avec la valeur du jeton.</span><span class="sxs-lookup"><span data-stu-id="ec476-300">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="ec476-301">En supposant que le script demande à envoyer le jeton dans un `X-CSRF-TOKEN`en-tête appelé, configurez le service anti `X-CSRF-TOKEN` -contrefaçon pour Rechercher l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="ec476-301">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="ec476-302">L’exemple suivant utilise JavaScript pour effectuer une demande AJAX avec l’en-tête approprié :</span><span class="sxs-lookup"><span data-stu-id="ec476-302">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="ec476-303">AngularJS</span><span class="sxs-lookup"><span data-stu-id="ec476-303">AngularJS</span></span>

<span data-ttu-id="ec476-304">AngularJS utilise une convention pour traiter CSRF.</span><span class="sxs-lookup"><span data-stu-id="ec476-304">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="ec476-305">Si le serveur envoie un cookie avec le nom `XSRF-TOKEN`, le service `$http` AngularJS ajoute la valeur de cookie à un en-tête lorsqu’il envoie une demande au serveur.</span><span class="sxs-lookup"><span data-stu-id="ec476-305">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="ec476-306">Ce processus est automatique.</span><span class="sxs-lookup"><span data-stu-id="ec476-306">This process is automatic.</span></span> <span data-ttu-id="ec476-307">L’en-tête n’a pas besoin d’être défini explicitement dans le client.</span><span class="sxs-lookup"><span data-stu-id="ec476-307">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="ec476-308">Le nom d’en `X-XSRF-TOKEN`-tête est.</span><span class="sxs-lookup"><span data-stu-id="ec476-308">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="ec476-309">Le serveur doit détecter cet en-tête et valider son contenu.</span><span class="sxs-lookup"><span data-stu-id="ec476-309">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="ec476-310">Pour que ASP.NET Core API fonctionne avec cette Convention au démarrage de votre application :</span><span class="sxs-lookup"><span data-stu-id="ec476-310">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="ec476-311">Configurez votre application pour fournir un jeton dans un `XSRF-TOKEN`cookie appelé.</span><span class="sxs-lookup"><span data-stu-id="ec476-311">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="ec476-312">Configurez le service anti-contrefaçon pour rechercher un en `X-XSRF-TOKEN`-tête nommé.</span><span class="sxs-lookup"><span data-stu-id="ec476-312">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="ec476-313">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ec476-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="ec476-314">Étendre l’anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="ec476-314">Extend antiforgery</span></span>

<span data-ttu-id="ec476-315">Le type [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permet aux développeurs d’étendre le comportement du système anti-CSRF en arrondissant les données supplémentaires dans chaque jeton.</span><span class="sxs-lookup"><span data-stu-id="ec476-315">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="ec476-316">La méthode [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré.</span><span class="sxs-lookup"><span data-stu-id="ec476-316">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="ec476-317">Un responsable de l’implémentation peut retourner un horodateur, une valeur à usage unique ou toute autre valeur, puis appeler [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) pour valider ces données lors de la validation du jeton.</span><span class="sxs-lookup"><span data-stu-id="ec476-317">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="ec476-318">Le nom d’utilisateur du client étant déjà incorporé dans les jetons générés, il n’est pas nécessaire d’inclure ces informations.</span><span class="sxs-lookup"><span data-stu-id="ec476-318">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="ec476-319">Si un jeton contient des données supplémentaires, mais `IAntiForgeryAdditionalDataProvider` qu’aucun n’est configuré, les données supplémentaires ne sont pas validées.</span><span class="sxs-lookup"><span data-stu-id="ec476-319">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec476-320">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ec476-320">Additional resources</span></span>

* <span data-ttu-id="ec476-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) sur le projet OWASP ( [Open Web Application Security](https://www.owasp.org/index.php/Main_Page) ).</span><span class="sxs-lookup"><span data-stu-id="ec476-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
