---
title: Activer les demandes d’origine croisée (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS en tant que norme pour autoriser ou rejeter les demandes d’origine croisée dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642696"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="5867f-103">Activer les demandes d’origine croisée (CORS) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5867f-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5867f-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5867f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="5867f-105">Cet article montre comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5867f-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="5867f-106">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="5867f-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5867f-107">Cette restriction est appelée la *même politique d’origine*.</span><span class="sxs-lookup"><span data-stu-id="5867f-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5867f-108">La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="5867f-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5867f-109">Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application.</span><span class="sxs-lookup"><span data-stu-id="5867f-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="5867f-110">Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="5867f-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="5867f-111">[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :</span><span class="sxs-lookup"><span data-stu-id="5867f-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="5867f-112">Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="5867f-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="5867f-113">N’est **pas** une fonction de sécurité, CORS détend la sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="5867f-114">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="5867f-115">Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="5867f-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="5867f-116">Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="5867f-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="5867f-117">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="5867f-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="5867f-118">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5867f-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="5867f-119">Même origine</span><span class="sxs-lookup"><span data-stu-id="5867f-119">Same origin</span></span>

<span data-ttu-id="5867f-120">Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="5867f-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="5867f-121">Ces deux URL ont la même origine :</span><span class="sxs-lookup"><span data-stu-id="5867f-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="5867f-122">Ces URL ont des origines différentes des deux URL précédentes :</span><span class="sxs-lookup"><span data-stu-id="5867f-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="5867f-123">`https://example.net`&ndash; Domaine différent</span><span class="sxs-lookup"><span data-stu-id="5867f-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="5867f-124">`https://www.example.com/foo.html`&ndash; Sous-comté différent</span><span class="sxs-lookup"><span data-stu-id="5867f-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="5867f-125">`http://example.com/foo.html`&ndash; Différents schémas</span><span class="sxs-lookup"><span data-stu-id="5867f-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="5867f-126">`https://example.com:9000/foo.html`&ndash; Port différent</span><span class="sxs-lookup"><span data-stu-id="5867f-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="5867f-127">Activez CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-127">Enable CORS</span></span>

<span data-ttu-id="5867f-128">Il y a trois façons de permettre à CORS :</span><span class="sxs-lookup"><span data-stu-id="5867f-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="5867f-129">Dans middleware en utilisant une [politique nommée](#np) ou la politique [par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="5867f-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="5867f-130">Utilisation [du routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="5867f-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="5867f-131">Avec l’attribut [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="5867f-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="5867f-132">L’utilisation [de l’attribut [EnableCors]](#attr) avec une stratégie nommée fournit le meilleur contrôle en limitant les points de terminaison qui prennent en charge CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="5867f-133">Chaque approche est détaillée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="5867f-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="5867f-134">CORS avec la politique nommée et middleware</span><span class="sxs-lookup"><span data-stu-id="5867f-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="5867f-135">CORS Middleware traite les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="5867f-136">Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :</span><span class="sxs-lookup"><span data-stu-id="5867f-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="5867f-137">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5867f-137">The preceding code:</span></span>

* <span data-ttu-id="5867f-138">Définit le nom `_myAllowSpecificOrigins`de la politique à .</span><span class="sxs-lookup"><span data-stu-id="5867f-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="5867f-139">Le nom de la politique est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="5867f-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="5867f-140">Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension `_myAllowSpecificOrigins` et spécifie la politique corS.</span><span class="sxs-lookup"><span data-stu-id="5867f-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="5867f-141">`UseCors`ajoute le middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="5867f-142">L’appel `UseCors` doit être `UseRouting`placé `UseAuthorization`après, mais avant .</span><span class="sxs-lookup"><span data-stu-id="5867f-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="5867f-143">Pour plus d’informations, voir [Middleware commande](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="5867f-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="5867f-144">Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="5867f-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="5867f-145">Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet.</span><span class="sxs-lookup"><span data-stu-id="5867f-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="5867f-146">[Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.</span><span class="sxs-lookup"><span data-stu-id="5867f-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="5867f-147">Permet `_myAllowSpecificOrigins` la politique CORS pour tous les points de terminaison du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5867f-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="5867f-148">Voir [l’itinéraire de point de terminaison](#ecors) pour appliquer une stratégie CORS à des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="5867f-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="5867f-149">Avec le routage de point de terminaison, le middleware `UseEndpoints`CORS ***doit*** être configuré pour exécuter entre les appels vers `UseRouting` et .</span><span class="sxs-lookup"><span data-stu-id="5867f-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="5867f-150">Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="5867f-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="5867f-151">L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="5867f-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="5867f-152">Pour plus d’informations, consultez [les options de politique du CORS](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="5867f-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="5867f-153">Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être enchaînées, comme le montre le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5867f-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="5867f-154">Remarque : L’URL spécifiée **ne** `/`doit pas contenir une barre oblique de fuite ().</span><span class="sxs-lookup"><span data-stu-id="5867f-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="5867f-155">Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="5867f-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="5867f-156">CORS avec la politique par défaut et middleware</span><span class="sxs-lookup"><span data-stu-id="5867f-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="5867f-157">Le code mis en évidence suivant permet la stratégie CORS par défaut :</span><span class="sxs-lookup"><span data-stu-id="5867f-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="5867f-158">Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5867f-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="5867f-159">Activez Les Cors avec le routage de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="5867f-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="5867f-160">Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf).</span><span class="sxs-lookup"><span data-stu-id="5867f-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="5867f-161">Pour plus d’informations, voir [ce numéro GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="5867f-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="5867f-162">Avec le routage de point de terminaison, CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> peut être activé sur une base par point de terminaison à l’aide de l’ensemble des méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="5867f-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="5867f-163">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5867f-163">In the preceding code:</span></span>

* <span data-ttu-id="5867f-164">`app.UseCors`permet au middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="5867f-165">Parce qu’une stratégie par défaut `app.UseCors()` n’a pas été configurée, elle seule n’active pas CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="5867f-166">Les `/echo` points de terminaison et les points de terminaison du contrôleur permettent des demandes d’origine croisée à l’aide de la stratégie spécifiée.</span><span class="sxs-lookup"><span data-stu-id="5867f-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="5867f-167">Les `/echo2` points de terminaison et les pages Razor n’autorisent ***pas*** les demandes d’origine croisée parce qu’aucune stratégie de défaut n’a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="5867f-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="5867f-168">[L’attribut [DisableCors]](#dc) ne désactive ***pas*** corS qui `RequireCors`a été activé par l’itinéraire de point de terminaison avec .</span><span class="sxs-lookup"><span data-stu-id="5867f-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="5867f-169">Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le code de test similaire à la précédente.</span><span class="sxs-lookup"><span data-stu-id="5867f-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="5867f-170">Activez CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="5867f-170">Enable CORS with attributes</span></span>

<span data-ttu-id="5867f-171">Permettre à CORS [l’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et appliquer une stratégie nommée uniquement aux points de terminaison qui nécessitent corS fournit le meilleur contrôle.</span><span class="sxs-lookup"><span data-stu-id="5867f-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="5867f-172">[L’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale.</span><span class="sxs-lookup"><span data-stu-id="5867f-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="5867f-173">L’attribut `[EnableCors]` permet CORS pour certains points de terminaison, plutôt que tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="5867f-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="5867f-174">`[EnableCors]`spécifie la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="5867f-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="5867f-175">`[EnableCors("{Policy String}")]`spécifie une politique nommée.</span><span class="sxs-lookup"><span data-stu-id="5867f-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="5867f-176">L’attribut `[EnableCors]` peut être appliqué à :</span><span class="sxs-lookup"><span data-stu-id="5867f-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="5867f-177">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="5867f-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="5867f-178">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="5867f-178">Controller</span></span>
* <span data-ttu-id="5867f-179">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="5867f-179">Controller action method</span></span>

<span data-ttu-id="5867f-180">Différentes stratégies peuvent être appliquées aux contrôleurs, `[EnableCors]` aux modèles de pages ou aux méthodes d’action avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="5867f-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="5867f-181">Lorsque `[EnableCors]` l’attribut est appliqué à un contrôleur, un modèle de page ou une méthode d’action, et que corS est activé dans middleware, ***les deux*** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="5867f-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="5867f-182">***Nous vous recommandons de ne pas combiner les politiques. Utilisez l’attribut*** `[EnableCors]` ***ou middleware, pas les deux dans la même application.***</span><span class="sxs-lookup"><span data-stu-id="5867f-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="5867f-183">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="5867f-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="5867f-184">Le code suivant crée deux stratégies CORS :</span><span class="sxs-lookup"><span data-stu-id="5867f-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="5867f-185">Pour le meilleur contrôle de limiter les demandes corS :</span><span class="sxs-lookup"><span data-stu-id="5867f-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="5867f-186">Utiliser `[EnableCors("MyPolicy")]` avec une politique nommée.</span><span class="sxs-lookup"><span data-stu-id="5867f-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="5867f-187">Ne définissez pas une stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="5867f-187">Don't define a default policy.</span></span>
* <span data-ttu-id="5867f-188">N’utilisez pas [le routage de point final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="5867f-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="5867f-189">Le code de la section suivante répond à la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="5867f-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="5867f-190">Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="5867f-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="5867f-191">Désactiver le CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-191">Disable CORS</span></span>

<span data-ttu-id="5867f-192">[L’attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive ***pas*** le CORS qui a été activé par [l’itinéraire de point final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="5867f-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="5867f-193">Le code suivant définit la `"MyPolicy"`politique corS :</span><span class="sxs-lookup"><span data-stu-id="5867f-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="5867f-194">Le code suivant désactive `GetValues2` CORS pour l’action :</span><span class="sxs-lookup"><span data-stu-id="5867f-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="5867f-195">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5867f-195">The preceding code:</span></span>

* <span data-ttu-id="5867f-196">Ne permet pas CORS avec [itinéraire de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="5867f-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="5867f-197">Ne définit pas une [stratégie CORS par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="5867f-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="5867f-198">Utilise [[EnableCors("MyPolicy")]](#attr) pour `"MyPolicy"` activer la politique CORS pour le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5867f-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="5867f-199">Désactive le CORS pour la `GetValues2` méthode.</span><span class="sxs-lookup"><span data-stu-id="5867f-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="5867f-200">Consultez [Test CORS](#testc) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="5867f-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="5867f-201">Options de politique CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-201">CORS policy options</span></span>

<span data-ttu-id="5867f-202">Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :</span><span class="sxs-lookup"><span data-stu-id="5867f-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="5867f-203">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="5867f-204">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="5867f-205">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="5867f-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="5867f-206">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="5867f-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="5867f-207">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="5867f-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="5867f-208">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="5867f-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="5867f-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans .</span><span class="sxs-lookup"><span data-stu-id="5867f-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5867f-210">Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.</span><span class="sxs-lookup"><span data-stu-id="5867f-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="5867f-211">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-211">Set the allowed origins</span></span>

<span data-ttu-id="5867f-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ).</span><span class="sxs-lookup"><span data-stu-id="5867f-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="5867f-213">`AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5867f-214">Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal.</span><span class="sxs-lookup"><span data-stu-id="5867f-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5867f-215">Le service CORS renvoie une réponse CORS invalide lorsqu’une application est configurée avec les deux méthodes.</span><span class="sxs-lookup"><span data-stu-id="5867f-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="5867f-216">`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="5867f-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="5867f-217">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5867f-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="5867f-219">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="5867f-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="5867f-221">Permet n’importe quelle méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="5867f-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="5867f-222">Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="5867f-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="5867f-223">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="5867f-224">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="5867f-224">Set the allowed request headers</span></span>

<span data-ttu-id="5867f-225">Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande [en-têtes](https://xhr.spec.whatwg.org/#request), appelez et spécifier les en-têtes autorisés:</span><span class="sxs-lookup"><span data-stu-id="5867f-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="5867f-226">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="5867f-227">`AllowAnyHeader`affecte les demandes de prévol et l’en-tête [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="5867f-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="5867f-228">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5867f-229">Un match de politique CORS Middleware `WithHeaders` à des en-têtes `Access-Control-Request-Headers` spécifiques spécifiés par `WithHeaders`n’est possible que lorsque les en-têtes envoyés exactement correspondre les en-têtes indiqués dans .</span><span class="sxs-lookup"><span data-stu-id="5867f-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="5867f-230">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="5867f-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="5867f-231">CORS Middleware décline une demande de prévol `Content-Language` avec l’en-tête de demande suivant `WithHeaders`parce que ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas répertorié dans :</span><span class="sxs-lookup"><span data-stu-id="5867f-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="5867f-232">L’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5867f-233">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="5867f-234">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="5867f-234">Set the exposed response headers</span></span>

<span data-ttu-id="5867f-235">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="5867f-236">Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="5867f-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="5867f-237">Les en-têtes de réponse qui sont disponibles par défaut sont les :</span><span class="sxs-lookup"><span data-stu-id="5867f-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="5867f-238">La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse .</span><span class="sxs-lookup"><span data-stu-id="5867f-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="5867f-239">Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="5867f-240">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="5867f-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="5867f-241">Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR.</span><span class="sxs-lookup"><span data-stu-id="5867f-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="5867f-242">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="5867f-243">Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="5867f-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="5867f-244">Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.</span><span class="sxs-lookup"><span data-stu-id="5867f-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="5867f-245">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="5867f-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="5867f-246">Utilisation de jQuery:</span><span class="sxs-lookup"><span data-stu-id="5867f-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="5867f-247">Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="5867f-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="5867f-248">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="5867f-248">The server must allow the credentials.</span></span> <span data-ttu-id="5867f-249">Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="5867f-250">La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="5867f-251">Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="5867f-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="5867f-252">Autoriser les informations d’identification inter-origine est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="5867f-253">Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5867f-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="5867f-254">La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="5867f-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="5867f-255">Demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="5867f-255">Preflight requests</span></span>

<span data-ttu-id="5867f-256">Pour certaines demandes CORS, le navigateur envoie une demande [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant de faire la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="5867f-257">Cette demande est appelée une [demande de prévol](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="5867f-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="5867f-258">Le navigateur peut sauter la demande de prévol si ***toutes les*** conditions suivantes sont vraies:</span><span class="sxs-lookup"><span data-stu-id="5867f-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="5867f-259">La méthode de demande est GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="5867f-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="5867f-260">L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="5867f-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="5867f-261">L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="5867f-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="5867f-262">La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet.</span><span class="sxs-lookup"><span data-stu-id="5867f-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="5867f-263">La spécification CORS appelle ces en-têtes [auteur demande en-têtes](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="5867f-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="5867f-264">La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .</span><span class="sxs-lookup"><span data-stu-id="5867f-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="5867f-265">Ce qui suit est une réponse d’exemple similaire à la demande de prévol faite à partir du bouton **[Mettre le test]** dans la section [Test CORS](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="5867f-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="5867f-266">La demande de prévol utilise la méthode [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="5867f-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="5867f-267">Il peut inclure les en-têtes suivants:</span><span class="sxs-lookup"><span data-stu-id="5867f-267">It may include the following headers:</span></span>

* <span data-ttu-id="5867f-268">[Access-Control-Request-Méthode](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="5867f-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Liste des en-têtes de demande que l’application fixe sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="5867f-270">Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="5867f-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="5867f-271">Accès-Contrôle-Autoriser-Méthodes</span><span class="sxs-lookup"><span data-stu-id="5867f-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="5867f-272">Si la demande de prévol est refusée, l’application renvoie une `200 OK` réponse mais ne résout pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="5867f-273">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="5867f-274">Par exemple, consultez la section [Test CORS](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="5867f-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="5867f-275">À l’aide des outils F12, l’application console affiche une erreur similaire à celle de ce qui suit, selon le navigateur :</span><span class="sxs-lookup"><span data-stu-id="5867f-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="5867f-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy refuse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`la lecture de la ressource à distance à .</span><span class="sxs-lookup"><span data-stu-id="5867f-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="5867f-277">(Raison : La demande de CORS n’a pas été accueillie).</span><span class="sxs-lookup"><span data-stu-id="5867f-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="5867f-278">En savoir plus</span><span class="sxs-lookup"><span data-stu-id="5867f-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="5867f-279">Basé sur le chrome :https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5L’accèshttps://cors3.azurewebsites.netà aller chercher à ' d’origine ' a été bloqué par la politique de CORS : la réponse à la demande de prévol ne passe pas le contrôle d’accès : aucun en-tête ' Access-Control-Allow-Origin' n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="5867f-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="5867f-280">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="5867f-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="5867f-281">Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="5867f-282">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="5867f-283">Les navigateurs ne sont pas `Access-Control-Request-Headers`cohérents dans la façon dont ils se définissent .</span><span class="sxs-lookup"><span data-stu-id="5867f-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="5867f-284">Si l’un ou l’autre:</span><span class="sxs-lookup"><span data-stu-id="5867f-284">If either:</span></span>

* <span data-ttu-id="5867f-285">Les en-têtes sont réglés à autre chose que`"*"`</span><span class="sxs-lookup"><span data-stu-id="5867f-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="5867f-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>est appelé: Inclure `Accept` `Content-Type`au `Origin`moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="5867f-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="5867f-287">Code automatique de demande de prévol</span><span class="sxs-lookup"><span data-stu-id="5867f-287">Automatic preflight request code</span></span>

<span data-ttu-id="5867f-288">Lorsque la politique corS est appliquée soit :</span><span class="sxs-lookup"><span data-stu-id="5867f-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="5867f-289">Globalement `app.UseCors` en `Startup.Configure`appelant dans .</span><span class="sxs-lookup"><span data-stu-id="5867f-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="5867f-290">Utilisation `[EnableCors]` de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="5867f-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="5867f-291">ASP.NET Core répond à la demande options avant le vol.</span><span class="sxs-lookup"><span data-stu-id="5867f-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="5867f-292">L’activation de LA COR sur `RequireCors` une base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les demandes automatiques de prévol.</span><span class="sxs-lookup"><span data-stu-id="5867f-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="5867f-293">La section [Test CORS](#testc) de ce document démontre ce comportement.</span><span class="sxs-lookup"><span data-stu-id="5867f-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="5867f-294">[HttpOptions] attribut pour les demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="5867f-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="5867f-295">Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="5867f-296">Dans certains scénarios, ce n’est peut-être pas le cas.</span><span class="sxs-lookup"><span data-stu-id="5867f-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="5867f-297">Par exemple, utiliser [CORS avec itinéraire de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="5867f-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="5867f-298">Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des paramètres pour les demandes OPTIONS :</span><span class="sxs-lookup"><span data-stu-id="5867f-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="5867f-299">Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="5867f-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="5867f-300">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="5867f-300">Set the preflight expiration time</span></span>

<span data-ttu-id="5867f-301">L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="5867f-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="5867f-302">Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="5867f-303">Fonctionnement du CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-303">How CORS works</span></span>

<span data-ttu-id="5867f-304">Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.</span><span class="sxs-lookup"><span data-stu-id="5867f-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="5867f-305">CORS **n’est pas** une fonction de sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="5867f-306">CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="5867f-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="5867f-307">Par exemple, un acteur malveillant peut utiliser [le script cross-Site (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="5867f-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="5867f-308">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="5867f-309">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="5867f-310">Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="5867f-311">Par exemple, l’un des outils suivants affichera la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="5867f-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="5867f-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5867f-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="5867f-313">Postman</span><span class="sxs-lookup"><span data-stu-id="5867f-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="5867f-314">.NET HttpClient (en)</span><span class="sxs-lookup"><span data-stu-id="5867f-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="5867f-315">Un navigateur Web en entrant l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="5867f-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="5867f-316">C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.</span><span class="sxs-lookup"><span data-stu-id="5867f-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="5867f-317">Les navigateurs sans CORS ne peuvent pas faire de demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="5867f-318">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="5867f-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="5867f-319">JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="5867f-320">Les scripts sont autorisés à être chargés à l’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="5867f-321">Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="5867f-322">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="5867f-323">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="5867f-324">Le [bouton de test PUT](https://cors3.azurewebsites.net/test) sur l’échantillon déployé [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="5867f-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="5867f-325">Ce qui suit est un exemple d’une demande `https://cors1.azurewebsites.net/api/values`d’origine croisée du bouton de test [Valeurs](https://cors3.azurewebsites.net/) à .</span><span class="sxs-lookup"><span data-stu-id="5867f-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="5867f-326">L’en-tête: `Origin`</span><span class="sxs-lookup"><span data-stu-id="5867f-326">The `Origin` header:</span></span>

* <span data-ttu-id="5867f-327">Fournit le domaine du site qui fait la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="5867f-328">Est nécessaire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="5867f-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="5867f-329">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="5867f-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="5867f-330">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="5867f-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="5867f-331">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="5867f-331">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="5867f-332">Dans `OPTIONS` les demandes, le serveur définit `Access-Control-Allow-Origin: {allowed origin}` l’en-tête **de réponse** dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="5867f-333">Par exemple, [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé, Supprimer `OPTIONS` la demande de bouton [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) contient les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="5867f-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="5867f-334">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="5867f-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="5867f-335">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="5867f-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="5867f-336">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="5867f-336">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="5867f-337">Dans les **en-têtes de réponse**précédents, le serveur définit l’en-tête [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="5867f-338">La `https://cors1.azurewebsites.net` valeur de cet `Origin` en-tête correspond à l’en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="5867f-339">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> on appelle, la `Access-Control-Allow-Origin: *`valeur , la wildcard, est retournée.</span><span class="sxs-lookup"><span data-stu-id="5867f-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="5867f-340">`AllowAnyOrigin`permet n’importe quelle origine.</span><span class="sxs-lookup"><span data-stu-id="5867f-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="5867f-341">Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="5867f-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="5867f-342">Plus précisément, le navigateur interdit la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="5867f-343">Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.</span><span class="sxs-lookup"><span data-stu-id="5867f-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="5867f-344">Afficher les demandes OPTIONS</span><span class="sxs-lookup"><span data-stu-id="5867f-344">Display OPTIONS requests</span></span>

<span data-ttu-id="5867f-345">Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sur l’onglet réseau des outils F12.</span><span class="sxs-lookup"><span data-stu-id="5867f-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="5867f-346">Pour afficher les demandes OPTIONS dans ces navigateurs :</span><span class="sxs-lookup"><span data-stu-id="5867f-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="5867f-347">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="5867f-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="5867f-348">désactiver le drapeau.</span><span class="sxs-lookup"><span data-stu-id="5867f-348">disable the flag.</span></span>
* <span data-ttu-id="5867f-349">redémarrer.</span><span class="sxs-lookup"><span data-stu-id="5867f-349">restart.</span></span>

<span data-ttu-id="5867f-350">Firefox affiche les demandes OPTIONS par défaut.</span><span class="sxs-lookup"><span data-stu-id="5867f-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="5867f-351">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="5867f-351">CORS in IIS</span></span>

<span data-ttu-id="5867f-352">Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="5867f-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="5867f-353">Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="5867f-354">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-354">Test CORS</span></span>

<span data-ttu-id="5867f-355">[L’exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) a du code pour tester CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="5867f-356">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5867f-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="5867f-357">L’échantillon est un projet API avec Razor Pages ajouté:</span><span class="sxs-lookup"><span data-stu-id="5867f-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="5867f-358">`WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5867f-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="5867f-359">Ce `ValuesController` qui suit fournit les paramètres de test :</span><span class="sxs-lookup"><span data-stu-id="5867f-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="5867f-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le forfait [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet et affiche les informations d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="5867f-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="5867f-361">Testez le code d’échantillon précédent en utilisant l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5867f-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="5867f-362">Utilisez l’application [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)d’échantillon déployée à .</span><span class="sxs-lookup"><span data-stu-id="5867f-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="5867f-363">Il n’est pas nécessaire de télécharger l’échantillon.</span><span class="sxs-lookup"><span data-stu-id="5867f-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="5867f-364">Exécutez l’échantillon avec `dotnet run` `https://localhost:5001`l’utilisation de l’URL par défaut de .</span><span class="sxs-lookup"><span data-stu-id="5867f-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="5867f-365">Exécuter l’échantillon de Visual Studio avec le port réglé à `https://localhost:44398`44398 pour une URL de .</span><span class="sxs-lookup"><span data-stu-id="5867f-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="5867f-366">Utilisation d’un navigateur avec les outils F12 :</span><span class="sxs-lookup"><span data-stu-id="5867f-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="5867f-367">Sélectionnez le bouton **Valeurs** et examinez les en-têtes de l’onglet **Réseau.**</span><span class="sxs-lookup"><span data-stu-id="5867f-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="5867f-368">Sélectionnez le bouton **de test PUT.**</span><span class="sxs-lookup"><span data-stu-id="5867f-368">Select the **PUT test** button.</span></span> <span data-ttu-id="5867f-369">Voir [afficher les demandes d’instructions options](#options) sur l’affichage de la demande OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="5867f-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="5867f-370">Le **test PUT** crée deux demandes, une demande de prévol OPTIONS et la demande PUT.</span><span class="sxs-lookup"><span data-stu-id="5867f-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="5867f-371">Sélectionnez **`GetValues2 [DisableCors]`** le bouton pour déclencher une demande CORS ratée.</span><span class="sxs-lookup"><span data-stu-id="5867f-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="5867f-372">Comme mentionné dans le document, la réponse renvoie 200 succès, mais la demande corS n’est pas faite.</span><span class="sxs-lookup"><span data-stu-id="5867f-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="5867f-373">Sélectionnez l’onglet **Console** pour voir l’erreur CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="5867f-374">Selon le navigateur, une erreur similaire à ce qui suit s’affiche :</span><span class="sxs-lookup"><span data-stu-id="5867f-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="5867f-375">L’accès `'https://cors1.azurewebsites.net/api/values/GetValues2'` à `'https://cors3.azurewebsites.net'` aller chercher à partir de l’origine a été bloqué par la politique du CORS : aucun en-tête « Access-Control-Allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="5867f-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="5867f-376">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="5867f-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="5867f-377">Les points de terminaison compatibles CORS peuvent être testés avec un outil, comme [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), ou [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="5867f-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="5867f-378">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="5867f-379">Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="5867f-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="5867f-380">Il n’est pas nécessaire pour CORS Middleware de traiter la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="5867f-381">Les en-têtes corS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="5867f-382">La commande `curl` suivante utilise pour émettre une demande OPTIONS avec des informations :</span><span class="sxs-lookup"><span data-stu-id="5867f-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="5867f-383">Test CORS avec itinéraire de point de terminaison et [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="5867f-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="5867f-384">Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf).</span><span class="sxs-lookup"><span data-stu-id="5867f-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="5867f-385">Considérez le code suivant qui utilise [le routage de point final pour activer CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="5867f-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="5867f-386">Ce `TodoItems1Controller` qui suit fournit des paramètres de test :</span><span class="sxs-lookup"><span data-stu-id="5867f-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="5867f-387">Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=1) de [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé .</span><span class="sxs-lookup"><span data-stu-id="5867f-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="5867f-388">Les boutons **Supprimer [EnableCors]** et **GET [EnableCors]** réussissent, `[EnableCors]` parce que les critères d’évaluation ont et répondent aux demandes avant le vol.</span><span class="sxs-lookup"><span data-stu-id="5867f-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="5867f-389">Les autres critères d’évaluation échouent.</span><span class="sxs-lookup"><span data-stu-id="5867f-389">The other endpoints fails.</span></span> <span data-ttu-id="5867f-390">Le bouton **GET** échoue, car le [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :</span><span class="sxs-lookup"><span data-stu-id="5867f-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="5867f-391">Ce `TodoItems2Controller` qui suit fournit des points de terminaison similaires, mais comprend un code explicite pour répondre aux demandes OPTIONS :</span><span class="sxs-lookup"><span data-stu-id="5867f-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="5867f-392">Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=2) de l’échantillon déployé.</span><span class="sxs-lookup"><span data-stu-id="5867f-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="5867f-393">Dans la liste **d’abandon du contrôleur,** sélectionnez **Preflight,** puis **Set Controller**.</span><span class="sxs-lookup"><span data-stu-id="5867f-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="5867f-394">Tous les appels corS aux points `TodoItems2Controller` de terminaison réussissent.</span><span class="sxs-lookup"><span data-stu-id="5867f-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5867f-395">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5867f-395">Additional resources</span></span>

* [<span data-ttu-id="5867f-396">Partage des ressources inter-origine (CORS)</span><span class="sxs-lookup"><span data-stu-id="5867f-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="5867f-397">Commencer avec le module CORS DE l’IIS</span><span class="sxs-lookup"><span data-stu-id="5867f-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5867f-398">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5867f-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5867f-399">Cet article montre comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5867f-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="5867f-400">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="5867f-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5867f-401">Cette restriction est appelée la *même politique d’origine*.</span><span class="sxs-lookup"><span data-stu-id="5867f-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5867f-402">La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="5867f-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5867f-403">Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application.</span><span class="sxs-lookup"><span data-stu-id="5867f-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="5867f-404">Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="5867f-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="5867f-405">[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :</span><span class="sxs-lookup"><span data-stu-id="5867f-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="5867f-406">Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="5867f-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="5867f-407">N’est **pas** une fonction de sécurité, CORS détend la sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="5867f-408">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="5867f-409">Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="5867f-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="5867f-410">Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="5867f-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="5867f-411">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="5867f-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="5867f-412">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5867f-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="5867f-413">Même origine</span><span class="sxs-lookup"><span data-stu-id="5867f-413">Same origin</span></span>

<span data-ttu-id="5867f-414">Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="5867f-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="5867f-415">Ces deux URL ont la même origine :</span><span class="sxs-lookup"><span data-stu-id="5867f-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="5867f-416">Ces URL ont des origines différentes des deux URL précédentes :</span><span class="sxs-lookup"><span data-stu-id="5867f-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="5867f-417">`https://example.net`&ndash; Domaine différent</span><span class="sxs-lookup"><span data-stu-id="5867f-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="5867f-418">`https://www.example.com/foo.html`&ndash; Sous-comté différent</span><span class="sxs-lookup"><span data-stu-id="5867f-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="5867f-419">`http://example.com/foo.html`&ndash; Différents schémas</span><span class="sxs-lookup"><span data-stu-id="5867f-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="5867f-420">`https://example.com:9000/foo.html`&ndash; Port différent</span><span class="sxs-lookup"><span data-stu-id="5867f-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="5867f-421">Internet Explorer ne considère pas le port en comparant les origines.</span><span class="sxs-lookup"><span data-stu-id="5867f-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="5867f-422">CORS avec la politique nommée et middleware</span><span class="sxs-lookup"><span data-stu-id="5867f-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="5867f-423">CORS Middleware traite les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="5867f-424">Le code suivant permet CORS pour l’ensemble de l’application avec l’origine spécifiée:</span><span class="sxs-lookup"><span data-stu-id="5867f-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="5867f-425">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5867f-425">The preceding code:</span></span>

* <span data-ttu-id="5867f-426">Définit le nom\_de la politique à "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="5867f-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="5867f-427">Le nom de la politique est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="5867f-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="5867f-428">Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension, qui permet CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="5867f-429">Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="5867f-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="5867f-430">Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet.</span><span class="sxs-lookup"><span data-stu-id="5867f-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="5867f-431">[Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.</span><span class="sxs-lookup"><span data-stu-id="5867f-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="5867f-432">L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="5867f-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="5867f-433">Pour plus d’informations, voir [les options de politique CORS](#cpo) dans ce document .</span><span class="sxs-lookup"><span data-stu-id="5867f-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="5867f-434">La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut enchaîner les méthodes, comme le montre le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5867f-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="5867f-435">Remarque : L’URL **ne** doit`/`pas contenir de barre oblique de fuite ().</span><span class="sxs-lookup"><span data-stu-id="5867f-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="5867f-436">Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="5867f-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="5867f-437">Le code suivant applique les stratégies CORS à tous les points de terminaison des applications via CORS Middleware :</span><span class="sxs-lookup"><span data-stu-id="5867f-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="5867f-438">Remarque `UseCors` : doit `UseMvc`être appelée avant .</span><span class="sxs-lookup"><span data-stu-id="5867f-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="5867f-439">Voir [Activer CORS dans Razor Pages, contrôleurs et méthodes d’action](#ecors) pour appliquer la stratégie CORS au niveau de la page/contrôleur/action.</span><span class="sxs-lookup"><span data-stu-id="5867f-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="5867f-440">Consultez [Test CORS](#test) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="5867f-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="5867f-441">Activez CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="5867f-441">Enable CORS with attributes</span></span>

<span data-ttu-id="5867f-442">L’attribut [ &lbrack;&rbrack; EnableCors](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale.</span><span class="sxs-lookup"><span data-stu-id="5867f-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="5867f-443">L’attribut `[EnableCors]` permet CORS pour certains points de fin, plutôt que tous les points finaux.</span><span class="sxs-lookup"><span data-stu-id="5867f-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="5867f-444">Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` pour spécifier une stratégie.</span><span class="sxs-lookup"><span data-stu-id="5867f-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="5867f-445">L’attribut `[EnableCors]` peut être appliqué à :</span><span class="sxs-lookup"><span data-stu-id="5867f-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="5867f-446">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="5867f-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="5867f-447">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="5867f-447">Controller</span></span>
* <span data-ttu-id="5867f-448">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="5867f-448">Controller action method</span></span>

<span data-ttu-id="5867f-449">Vous pouvez appliquer différentes stratégies au contrôleur/page-modèle/action avec l’attribut. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="5867f-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="5867f-450">Lorsque `[EnableCors]` l’attribut est appliqué à un modèle/méthode d’action de contrôleurs/pages, et que le CORS est activé dans middleware, ***les deux*** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="5867f-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="5867f-451">Nous recommandons ***de ne pas*** combiner les politiques.</span><span class="sxs-lookup"><span data-stu-id="5867f-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="5867f-452">Utilisez `[EnableCors]` l’attribut ou middleware,**pas les deux**.</span><span class="sxs-lookup"><span data-stu-id="5867f-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="5867f-453">Lors `[EnableCors]`de l’utilisation, ne définissez **pas** une stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="5867f-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="5867f-454">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="5867f-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="5867f-455">Le code suivant crée une politique de `"AnotherPolicy"`défaut corS et une stratégie nommée :</span><span class="sxs-lookup"><span data-stu-id="5867f-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="5867f-456">Désactiver le CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-456">Disable CORS</span></span>

<span data-ttu-id="5867f-457">[ &lbrack;L’attribut&rbrack; DisableCors](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive CORS pour le contrôleur/page-modèle/action.</span><span class="sxs-lookup"><span data-stu-id="5867f-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="5867f-458">Options de politique CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-458">CORS policy options</span></span>

<span data-ttu-id="5867f-459">Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :</span><span class="sxs-lookup"><span data-stu-id="5867f-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="5867f-460">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="5867f-461">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="5867f-462">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="5867f-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="5867f-463">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="5867f-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="5867f-464">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="5867f-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="5867f-465">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="5867f-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="5867f-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans .</span><span class="sxs-lookup"><span data-stu-id="5867f-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5867f-467">Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.</span><span class="sxs-lookup"><span data-stu-id="5867f-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="5867f-468">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-468">Set the allowed origins</span></span>

<span data-ttu-id="5867f-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ).</span><span class="sxs-lookup"><span data-stu-id="5867f-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="5867f-470">`AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5867f-471">Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal.</span><span class="sxs-lookup"><span data-stu-id="5867f-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5867f-472">Pour une application sécurisée, spécifiez une liste exacte des origines si le client doit s’autoriser à accéder aux ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="5867f-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="5867f-473">`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="5867f-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="5867f-474">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5867f-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="5867f-476">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="5867f-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="5867f-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="5867f-478">Permet n’importe quelle méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="5867f-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="5867f-479">Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="5867f-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="5867f-480">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="5867f-481">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="5867f-481">Set the allowed request headers</span></span>

<span data-ttu-id="5867f-482">Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande *en-têtes*, appelez et spécifier les en-têtes autorisés:</span><span class="sxs-lookup"><span data-stu-id="5867f-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5867f-483">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5867f-484">Ce paramètre affecte les `Access-Control-Request-Headers` demandes de prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="5867f-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="5867f-485">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5867f-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5867f-486">CORS Middleware permet toujours d’envoyer quatre en-têtes dans le `Access-Control-Request-Headers` code indépendamment des valeurs configurées dans CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="5867f-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="5867f-487">Cette liste d’en-têtes comprend :</span><span class="sxs-lookup"><span data-stu-id="5867f-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="5867f-488">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="5867f-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="5867f-489">CORS Middleware répond avec succès à une demande de `Content-Language` prévol avec l’en-tête de demande suivant parce qu’il est toujours sur la liste blanche :</span><span class="sxs-lookup"><span data-stu-id="5867f-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="5867f-490">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="5867f-490">Set the exposed response headers</span></span>

<span data-ttu-id="5867f-491">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="5867f-492">Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="5867f-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="5867f-493">Les en-têtes de réponse qui sont disponibles par défaut sont les :</span><span class="sxs-lookup"><span data-stu-id="5867f-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="5867f-494">La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse .</span><span class="sxs-lookup"><span data-stu-id="5867f-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="5867f-495">Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="5867f-496">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="5867f-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="5867f-497">Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR.</span><span class="sxs-lookup"><span data-stu-id="5867f-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="5867f-498">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="5867f-499">Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="5867f-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="5867f-500">Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.</span><span class="sxs-lookup"><span data-stu-id="5867f-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="5867f-501">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="5867f-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="5867f-502">Utilisation de jQuery:</span><span class="sxs-lookup"><span data-stu-id="5867f-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="5867f-503">Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="5867f-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="5867f-504">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="5867f-504">The server must allow the credentials.</span></span> <span data-ttu-id="5867f-505">Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="5867f-506">La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="5867f-507">Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="5867f-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="5867f-508">Autoriser les informations d’identification inter-origine est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="5867f-509">Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5867f-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="5867f-510">La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="5867f-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="5867f-511">Demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="5867f-511">Preflight requests</span></span>

<span data-ttu-id="5867f-512">Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant de faire la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="5867f-513">Cette demande est appelée une *demande de prévol*.</span><span class="sxs-lookup"><span data-stu-id="5867f-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="5867f-514">Le navigateur peut sauter la demande de prévol si les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="5867f-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="5867f-515">La méthode de demande est GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="5867f-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="5867f-516">L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="5867f-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="5867f-517">L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="5867f-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="5867f-518">La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet.</span><span class="sxs-lookup"><span data-stu-id="5867f-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="5867f-519">La spécification CORS appelle ces en-têtes *auteur demande en-têtes*.</span><span class="sxs-lookup"><span data-stu-id="5867f-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="5867f-520">La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .</span><span class="sxs-lookup"><span data-stu-id="5867f-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="5867f-521">Voici un exemple de demande de prévol :</span><span class="sxs-lookup"><span data-stu-id="5867f-521">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="5867f-522">La demande avant vol utilise la méthode HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="5867f-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="5867f-523">Il comprend deux en-têtes spéciaux :</span><span class="sxs-lookup"><span data-stu-id="5867f-523">It includes two special headers:</span></span>

* <span data-ttu-id="5867f-524">`Access-Control-Request-Method`: La méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="5867f-525">`Access-Control-Request-Headers`: Une liste d’en-têtes de demande que l’application fixe sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="5867f-526">Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="5867f-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="5867f-527">Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="5867f-528">Voir [[HttpOptions] attribut pour les demandes de prévol](#pro).</span><span class="sxs-lookup"><span data-stu-id="5867f-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="5867f-529">Une demande de prévol corS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes qui sont envoyés avec la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="5867f-530">Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="5867f-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5867f-531">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5867f-532">Les navigateurs ne sont pas `Access-Control-Request-Headers`tout à fait cohérents dans la façon dont ils se définissent .</span><span class="sxs-lookup"><span data-stu-id="5867f-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="5867f-533">Si vous définissez des `"*"` en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>à autre chose `Accept` `Content-Type`que `Origin`(ou utiliser ), vous devriez inclure au moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="5867f-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="5867f-534">Ce qui suit est une réponse exemple à la demande de prévol (en supposant que le serveur permet la demande):</span><span class="sxs-lookup"><span data-stu-id="5867f-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="5867f-535">La réponse `Access-Control-Allow-Methods` comprend un en-tête qui `Access-Control-Allow-Headers` répertorie les méthodes autorisées et optionnellement un en-tête, qui répertorie les en-têtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="5867f-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="5867f-536">Si la demande de prévol réussit, le navigateur envoie la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="5867f-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="5867f-537">Si la demande de prévol est refusée, l’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5867f-538">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="5867f-539">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="5867f-539">Set the preflight expiration time</span></span>

<span data-ttu-id="5867f-540">L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="5867f-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="5867f-541">Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :</span><span class="sxs-lookup"><span data-stu-id="5867f-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="5867f-542">Fonctionnement du CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-542">How CORS works</span></span>

<span data-ttu-id="5867f-543">Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.</span><span class="sxs-lookup"><span data-stu-id="5867f-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="5867f-544">CORS **n’est pas** une fonction de sécurité.</span><span class="sxs-lookup"><span data-stu-id="5867f-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="5867f-545">CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="5867f-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="5867f-546">Par exemple, un acteur malveillant peut utiliser [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="5867f-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="5867f-547">Votre API n’est pas plus sûr en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="5867f-548">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="5867f-549">Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="5867f-550">Par exemple, l’un des outils suivants affichera la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="5867f-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="5867f-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5867f-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="5867f-552">Postman</span><span class="sxs-lookup"><span data-stu-id="5867f-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="5867f-553">.NET HttpClient (en)</span><span class="sxs-lookup"><span data-stu-id="5867f-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="5867f-554">Un navigateur Web en entrant l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="5867f-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="5867f-555">C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.</span><span class="sxs-lookup"><span data-stu-id="5867f-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="5867f-556">Les navigateurs (sans CORS) ne peuvent pas faire de demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="5867f-557">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="5867f-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="5867f-558">JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="5867f-559">Les scripts sont autorisés à être chargés à l’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="5867f-560">Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="5867f-561">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="5867f-562">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="5867f-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="5867f-563">Ce qui suit est un exemple d’une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="5867f-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="5867f-564">L’en-tête `Origin` fournit le domaine du site qui fait la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="5867f-565">L’en-tête `Origin` est nécessaire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="5867f-565">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="5867f-566">Si le serveur autorise la `Access-Control-Allow-Origin` demande, il définit l’en-tête dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="5867f-567">La valeur de cet `Origin` en-tête correspond soit à `"*"`l’en-tête de la demande ou est la valeur wildcard , ce qui signifie que toute origine est autorisée:</span><span class="sxs-lookup"><span data-stu-id="5867f-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="5867f-568">Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="5867f-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="5867f-569">Plus précisément, le navigateur interdit la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="5867f-570">Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.</span><span class="sxs-lookup"><span data-stu-id="5867f-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="5867f-571">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="5867f-571">Test CORS</span></span>

<span data-ttu-id="5867f-572">Pour tester CORS :</span><span class="sxs-lookup"><span data-stu-id="5867f-572">To test CORS:</span></span>

1. <span data-ttu-id="5867f-573">[Créer un projet API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="5867f-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="5867f-574">Alternativement, vous pouvez [télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5867f-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="5867f-575">Activez CORS à l’aide d’une des approches de ce document.</span><span class="sxs-lookup"><span data-stu-id="5867f-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="5867f-576">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5867f-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="5867f-577">`WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5867f-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="5867f-578">Créez un projet d’application web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="5867f-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="5867f-579">L’échantillon utilise Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5867f-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="5867f-580">Vous pouvez créer l’application web dans la même solution que le projet API.</span><span class="sxs-lookup"><span data-stu-id="5867f-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="5867f-581">Ajoutez le code mis en évidence suivant au fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5867f-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="5867f-582">Dans le code `url: 'https://<web app>.azurewebsites.net/api/values/1',` précédent, remplacez par l’URL de l’application déployée.</span><span class="sxs-lookup"><span data-stu-id="5867f-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="5867f-583">Déployer le projet API.</span><span class="sxs-lookup"><span data-stu-id="5867f-583">Deploy the API project.</span></span> <span data-ttu-id="5867f-584">Par exemple, [déployez-vous à Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="5867f-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="5867f-585">Exécutez les pages Razor ou l’application MVC depuis le bureau et cliquez sur le bouton **Test.**</span><span class="sxs-lookup"><span data-stu-id="5867f-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="5867f-586">Utilisez les outils F12 pour passer en revue les messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="5867f-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="5867f-587">Retirez l’origine `WithOrigins` locale et déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="5867f-588">Alternativement, exécutez l’application client avec un port différent.</span><span class="sxs-lookup"><span data-stu-id="5867f-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="5867f-589">Par exemple, dirigez-vous à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5867f-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="5867f-590">Test avec l’application client.</span><span class="sxs-lookup"><span data-stu-id="5867f-590">Test with the client app.</span></span> <span data-ttu-id="5867f-591">Les échecs de CORS renvoient une erreur, mais le message d’erreur n’est pas disponible pour JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5867f-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="5867f-592">Utilisez l’onglet console dans les outils F12 pour voir l’erreur.</span><span class="sxs-lookup"><span data-stu-id="5867f-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="5867f-593">Selon le navigateur, vous obtenez une erreur (dans la console d’outils F12) similaire à ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="5867f-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="5867f-594">Utilisation de Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="5867f-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="5867f-595">**SEC7120: [CORS] `https://localhost:44375` L’origine `https://localhost:44375` n’a pas trouvé dans l’en-tête de réponse Access-Control-Allow-Origin pour la ressource d’origine croisée à`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="5867f-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="5867f-596">Utilisation de Chrome:</span><span class="sxs-lookup"><span data-stu-id="5867f-596">Using Chrome:</span></span>

     <span data-ttu-id="5867f-597">**L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la politique corS: Aucun «Accès-Contrôle-Autoriser-Origin» en-tête n’est présent sur la ressource demandée.**</span><span class="sxs-lookup"><span data-stu-id="5867f-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="5867f-598">Les paramètres de terminaison compatibles CORS peuvent être testés avec un outil, comme [Fiddler](https://www.telerik.com/fiddler) ou [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="5867f-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="5867f-599">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="5867f-600">Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="5867f-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="5867f-601">Il n’est pas nécessaire pour CORS Middleware de traiter la demande.</span><span class="sxs-lookup"><span data-stu-id="5867f-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="5867f-602">Les en-têtes corS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="5867f-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="5867f-603">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="5867f-603">CORS in IIS</span></span>

<span data-ttu-id="5867f-604">Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="5867f-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="5867f-605">Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="5867f-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5867f-606">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5867f-606">Additional resources</span></span>

* [<span data-ttu-id="5867f-607">Partage des ressources inter-origine (CORS)</span><span class="sxs-lookup"><span data-stu-id="5867f-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="5867f-608">Commencer avec le module CORS DE l’IIS</span><span class="sxs-lookup"><span data-stu-id="5867f-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
