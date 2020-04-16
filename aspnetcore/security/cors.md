---
title: Activer les demandes d’origine croisée (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS en tant que norme pour autoriser ou rejeter les demandes d’origine croisée dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440907"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="c9715-103">Activer les demandes d’origine croisée (CORS) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9715-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9715-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c9715-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c9715-105">Cet article montre comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c9715-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c9715-106">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="c9715-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c9715-107">Cette restriction est appelée la *même politique d’origine*.</span><span class="sxs-lookup"><span data-stu-id="c9715-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c9715-108">La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="c9715-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c9715-109">Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application.</span><span class="sxs-lookup"><span data-stu-id="c9715-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="c9715-110">Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="c9715-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c9715-111">[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :</span><span class="sxs-lookup"><span data-stu-id="c9715-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c9715-112">Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="c9715-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c9715-113">N’est **pas** une fonction de sécurité, CORS détend la sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c9715-114">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c9715-115">Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="c9715-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c9715-116">Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="c9715-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c9715-117">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="c9715-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c9715-118">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9715-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c9715-119">Même origine</span><span class="sxs-lookup"><span data-stu-id="c9715-119">Same origin</span></span>

<span data-ttu-id="c9715-120">Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="c9715-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c9715-121">Ces deux URL ont la même origine :</span><span class="sxs-lookup"><span data-stu-id="c9715-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c9715-122">Ces URL ont des origines différentes des deux URL précédentes :</span><span class="sxs-lookup"><span data-stu-id="c9715-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c9715-123">`https://example.net`&ndash; Domaine différent</span><span class="sxs-lookup"><span data-stu-id="c9715-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c9715-124">`https://www.example.com/foo.html`&ndash; Sous-comté différent</span><span class="sxs-lookup"><span data-stu-id="c9715-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c9715-125">`http://example.com/foo.html`&ndash; Différents schémas</span><span class="sxs-lookup"><span data-stu-id="c9715-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c9715-126">`https://example.com:9000/foo.html`&ndash; Port différent</span><span class="sxs-lookup"><span data-stu-id="c9715-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="c9715-127">Activez CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-127">Enable CORS</span></span>

<span data-ttu-id="c9715-128">Il y a trois façons de permettre à CORS :</span><span class="sxs-lookup"><span data-stu-id="c9715-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="c9715-129">Dans middleware en utilisant une [politique nommée](#np) ou la politique [par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="c9715-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="c9715-130">Utilisation [du routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="c9715-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c9715-131">Avec l’attribut [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="c9715-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="c9715-132">L’utilisation [de l’attribut [EnableCors]](#attr) avec une stratégie nommée fournit le meilleur contrôle en limitant les points de terminaison qui prennent en charge CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="c9715-133">Chaque approche est détaillée dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="c9715-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c9715-134">CORS avec la politique nommée et middleware</span><span class="sxs-lookup"><span data-stu-id="c9715-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="c9715-135">CORS Middleware traite les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c9715-136">Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :</span><span class="sxs-lookup"><span data-stu-id="c9715-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="c9715-137">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c9715-137">The preceding code:</span></span>

* <span data-ttu-id="c9715-138">Définit le nom `_myAllowSpecificOrigins`de la politique à .</span><span class="sxs-lookup"><span data-stu-id="c9715-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="c9715-139">Le nom de la politique est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="c9715-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c9715-140">Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension `_myAllowSpecificOrigins` et spécifie la politique corS.</span><span class="sxs-lookup"><span data-stu-id="c9715-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="c9715-141">`UseCors`ajoute le middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="c9715-142">Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="c9715-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c9715-143">Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet.</span><span class="sxs-lookup"><span data-stu-id="c9715-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c9715-144">[Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.</span><span class="sxs-lookup"><span data-stu-id="c9715-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="c9715-145">Permet `_myAllowSpecificOrigins` la politique CORS pour tous les points de terminaison du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c9715-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="c9715-146">Voir [l’itinéraire de point de terminaison](#ecors) pour appliquer une stratégie CORS à des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c9715-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="c9715-147">Avec le routage de point de terminaison, le middleware `UseEndpoints`CORS ***doit*** être configuré pour exécuter entre les appels vers `UseRouting` et .</span><span class="sxs-lookup"><span data-stu-id="c9715-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="c9715-148">Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="c9715-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="c9715-149">L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="c9715-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c9715-150">Pour plus d’informations, consultez [les options de politique du CORS](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="c9715-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="c9715-151">Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être enchaînées, comme le montre le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c9715-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="c9715-152">Remarque : L’URL spécifiée **ne** `/`doit pas contenir une barre oblique de fuite ().</span><span class="sxs-lookup"><span data-stu-id="c9715-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c9715-153">Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="c9715-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="c9715-154">CORS avec la politique par défaut et middleware</span><span class="sxs-lookup"><span data-stu-id="c9715-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="c9715-155">Le code mis en évidence suivant permet la stratégie CORS par défaut :</span><span class="sxs-lookup"><span data-stu-id="c9715-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="c9715-156">Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c9715-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="c9715-157">Activez Les Cors avec le routage de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="c9715-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="c9715-158">Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf).</span><span class="sxs-lookup"><span data-stu-id="c9715-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c9715-159">Pour plus d’informations, voir [ce numéro GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="c9715-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="c9715-160">Avec le routage de point de terminaison, CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> peut être activé sur une base par point de terminaison à l’aide de l’ensemble des méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="c9715-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="c9715-161">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c9715-161">In the preceding code:</span></span>

* <span data-ttu-id="c9715-162">`app.UseCors`permet au middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="c9715-163">Parce qu’une stratégie par défaut `app.UseCors()` n’a pas été configurée, elle seule n’active pas CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="c9715-164">Les `/echo` points de terminaison et les points de terminaison du contrôleur permettent des demandes d’origine croisée à l’aide de la stratégie spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c9715-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="c9715-165">Les `/echo2` points de terminaison et les pages Razor n’autorisent ***pas*** les demandes d’origine croisée parce qu’aucune stratégie de défaut n’a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c9715-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="c9715-166">[L’attribut [DisableCors]](#dc) ne désactive ***pas*** corS qui `RequireCors`a été activé par l’itinéraire de point de terminaison avec .</span><span class="sxs-lookup"><span data-stu-id="c9715-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="c9715-167">Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le code de test similaire à la précédente.</span><span class="sxs-lookup"><span data-stu-id="c9715-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c9715-168">Activez CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="c9715-168">Enable CORS with attributes</span></span>

<span data-ttu-id="c9715-169">Permettre à CORS [l’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et appliquer une stratégie nommée uniquement aux points de terminaison qui nécessitent corS fournit le meilleur contrôle.</span><span class="sxs-lookup"><span data-stu-id="c9715-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="c9715-170">[L’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale.</span><span class="sxs-lookup"><span data-stu-id="c9715-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c9715-171">L’attribut `[EnableCors]` permet CORS pour certains points de terminaison, plutôt que tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="c9715-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="c9715-172">`[EnableCors]`spécifie la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="c9715-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="c9715-173">`[EnableCors("{Policy String}")]`spécifie une politique nommée.</span><span class="sxs-lookup"><span data-stu-id="c9715-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="c9715-174">L’attribut `[EnableCors]` peut être appliqué à :</span><span class="sxs-lookup"><span data-stu-id="c9715-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="c9715-175">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c9715-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="c9715-176">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="c9715-176">Controller</span></span>
* <span data-ttu-id="c9715-177">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="c9715-177">Controller action method</span></span>

<span data-ttu-id="c9715-178">Différentes stratégies peuvent être appliquées aux contrôleurs, `[EnableCors]` aux modèles de pages ou aux méthodes d’action avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="c9715-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="c9715-179">Lorsque `[EnableCors]` l’attribut est appliqué à un contrôleur, un modèle de page ou une méthode d’action, et que corS est activé dans middleware, ***les deux*** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="c9715-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c9715-180">***Nous vous recommandons de ne pas combiner les politiques. Utilisez l’attribut*** `[EnableCors]` ***ou middleware, pas les deux dans la même application.***</span><span class="sxs-lookup"><span data-stu-id="c9715-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="c9715-181">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="c9715-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c9715-182">Le code suivant crée deux stratégies CORS :</span><span class="sxs-lookup"><span data-stu-id="c9715-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="c9715-183">Pour le meilleur contrôle de limiter les demandes corS :</span><span class="sxs-lookup"><span data-stu-id="c9715-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="c9715-184">Utiliser `[EnableCors("MyPolicy")]` avec une politique nommée.</span><span class="sxs-lookup"><span data-stu-id="c9715-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="c9715-185">Ne définissez pas une stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="c9715-185">Don't define a default policy.</span></span>
* <span data-ttu-id="c9715-186">N’utilisez pas [le routage de point final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="c9715-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9715-187">Le code de la section suivante répond à la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="c9715-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="c9715-188">Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="c9715-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="c9715-189">Désactiver le CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-189">Disable CORS</span></span>

<span data-ttu-id="c9715-190">[L’attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive ***pas*** le CORS qui a été activé par [l’itinéraire de point final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="c9715-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9715-191">Le code suivant définit la `"MyPolicy"`politique corS :</span><span class="sxs-lookup"><span data-stu-id="c9715-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="c9715-192">Le code suivant désactive `GetValues2` CORS pour l’action :</span><span class="sxs-lookup"><span data-stu-id="c9715-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="c9715-193">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c9715-193">The preceding code:</span></span>

* <span data-ttu-id="c9715-194">Ne permet pas CORS avec [itinéraire de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="c9715-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c9715-195">Ne définit pas une [stratégie CORS par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="c9715-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="c9715-196">Utilise [[EnableCors("MyPolicy")]](#attr) pour `"MyPolicy"` activer la politique CORS pour le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c9715-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="c9715-197">Désactive le CORS pour la `GetValues2` méthode.</span><span class="sxs-lookup"><span data-stu-id="c9715-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="c9715-198">Consultez [Test CORS](#testc) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="c9715-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c9715-199">Options de politique CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-199">CORS policy options</span></span>

<span data-ttu-id="c9715-200">Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :</span><span class="sxs-lookup"><span data-stu-id="c9715-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c9715-201">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c9715-202">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c9715-203">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="c9715-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c9715-204">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="c9715-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c9715-205">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="c9715-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c9715-206">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="c9715-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c9715-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans .</span><span class="sxs-lookup"><span data-stu-id="c9715-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c9715-208">Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.</span><span class="sxs-lookup"><span data-stu-id="c9715-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c9715-209">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-209">Set the allowed origins</span></span>

<span data-ttu-id="c9715-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ).</span><span class="sxs-lookup"><span data-stu-id="c9715-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c9715-211">`AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c9715-212">Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal.</span><span class="sxs-lookup"><span data-stu-id="c9715-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c9715-213">Le service CORS renvoie une réponse CORS invalide lorsqu’une application est configurée avec les deux méthodes.</span><span class="sxs-lookup"><span data-stu-id="c9715-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="c9715-214">`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c9715-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c9715-215">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9715-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c9715-217">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c9715-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c9715-219">Permet n’importe quelle méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="c9715-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="c9715-220">Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c9715-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c9715-221">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c9715-222">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="c9715-222">Set the allowed request headers</span></span>

<span data-ttu-id="c9715-223">Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande [en-têtes](https://xhr.spec.whatwg.org/#request), appelez et spécifier les en-têtes autorisés:</span><span class="sxs-lookup"><span data-stu-id="c9715-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c9715-224">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c9715-225">`AllowAnyHeader`affecte les demandes de prévol et l’en-tête [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="c9715-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="c9715-226">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9715-227">Un match de politique CORS Middleware `WithHeaders` à des en-têtes `Access-Control-Request-Headers` spécifiques spécifiés par `WithHeaders`n’est possible que lorsque les en-têtes envoyés exactement correspondre les en-têtes indiqués dans .</span><span class="sxs-lookup"><span data-stu-id="c9715-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="c9715-228">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="c9715-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="c9715-229">CORS Middleware décline une demande de prévol `Content-Language` avec l’en-tête de demande suivant `WithHeaders`parce que ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas répertorié dans :</span><span class="sxs-lookup"><span data-stu-id="c9715-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="c9715-230">L’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c9715-231">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c9715-232">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="c9715-232">Set the exposed response headers</span></span>

<span data-ttu-id="c9715-233">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c9715-234">Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="c9715-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c9715-235">Les en-têtes de réponse qui sont disponibles par défaut sont les :</span><span class="sxs-lookup"><span data-stu-id="c9715-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c9715-236">La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse .</span><span class="sxs-lookup"><span data-stu-id="c9715-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c9715-237">Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c9715-238">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="c9715-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c9715-239">Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR.</span><span class="sxs-lookup"><span data-stu-id="c9715-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c9715-240">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c9715-241">Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9715-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c9715-242">Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.</span><span class="sxs-lookup"><span data-stu-id="c9715-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c9715-243">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="c9715-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c9715-244">Utilisation de jQuery:</span><span class="sxs-lookup"><span data-stu-id="c9715-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c9715-245">Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="c9715-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c9715-246">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="c9715-246">The server must allow the credentials.</span></span> <span data-ttu-id="c9715-247">Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="c9715-248">La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c9715-249">Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="c9715-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c9715-250">Autoriser les informations d’identification inter-origine est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c9715-251">Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c9715-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c9715-252">La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="c9715-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="c9715-253">Demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="c9715-253">Preflight requests</span></span>

<span data-ttu-id="c9715-254">Pour certaines demandes CORS, le navigateur envoie une demande [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant de faire la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="c9715-255">Cette demande est appelée une [demande de prévol](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="c9715-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="c9715-256">Le navigateur peut sauter la demande de prévol si ***toutes les*** conditions suivantes sont vraies:</span><span class="sxs-lookup"><span data-stu-id="c9715-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="c9715-257">La méthode de demande est GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="c9715-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c9715-258">L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="c9715-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c9715-259">L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="c9715-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c9715-260">La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet.</span><span class="sxs-lookup"><span data-stu-id="c9715-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c9715-261">La spécification CORS appelle ces en-têtes [auteur demande en-têtes](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="c9715-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="c9715-262">La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .</span><span class="sxs-lookup"><span data-stu-id="c9715-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c9715-263">Ce qui suit est une réponse d’exemple similaire à la demande de prévol faite à partir du bouton **[Mettre le test]** dans la section [Test CORS](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="c9715-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="c9715-264">La demande de prévol utilise la méthode [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="c9715-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="c9715-265">Il peut inclure les en-têtes suivants:</span><span class="sxs-lookup"><span data-stu-id="c9715-265">It may include the following headers:</span></span>

* <span data-ttu-id="c9715-266">[Access-Control-Request-Méthode](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c9715-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Liste des en-têtes de demande que l’application fixe sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c9715-268">Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="c9715-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="c9715-269">Accès-Contrôle-Autoriser-Méthodes</span><span class="sxs-lookup"><span data-stu-id="c9715-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="c9715-270">Si la demande de prévol est refusée, l’application renvoie une `200 OK` réponse mais ne résout pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="c9715-271">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="c9715-272">Par exemple, consultez la section [Test CORS](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="c9715-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="c9715-273">À l’aide des outils F12, l’application console affiche une erreur similaire à celle de ce qui suit, selon le navigateur :</span><span class="sxs-lookup"><span data-stu-id="c9715-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="c9715-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy refuse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`la lecture de la ressource à distance à .</span><span class="sxs-lookup"><span data-stu-id="c9715-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="c9715-275">(Raison : La demande de CORS n’a pas été accueillie).</span><span class="sxs-lookup"><span data-stu-id="c9715-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="c9715-276">En savoir plus</span><span class="sxs-lookup"><span data-stu-id="c9715-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="c9715-277">Basé sur le chrome :https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5L’accèshttps://cors3.azurewebsites.netà aller chercher à ' d’origine ' a été bloqué par la politique de CORS : la réponse à la demande de prévol ne passe pas le contrôle d’accès : aucun en-tête ' Access-Control-Allow-Origin' n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="c9715-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c9715-278">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="c9715-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="c9715-279">Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c9715-280">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c9715-281">Les navigateurs ne sont pas `Access-Control-Request-Headers`cohérents dans la façon dont ils se définissent .</span><span class="sxs-lookup"><span data-stu-id="c9715-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c9715-282">Si l’un ou l’autre:</span><span class="sxs-lookup"><span data-stu-id="c9715-282">If either:</span></span>

* <span data-ttu-id="c9715-283">Les en-têtes sont réglés à autre chose que`"*"`</span><span class="sxs-lookup"><span data-stu-id="c9715-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="c9715-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>est appelé: Inclure `Accept` `Content-Type`au `Origin`moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="c9715-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="c9715-285">Code automatique de demande de prévol</span><span class="sxs-lookup"><span data-stu-id="c9715-285">Automatic preflight request code</span></span>

<span data-ttu-id="c9715-286">Lorsque la politique corS est appliquée soit :</span><span class="sxs-lookup"><span data-stu-id="c9715-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="c9715-287">Globalement `app.UseCors` en `Startup.Configure`appelant dans .</span><span class="sxs-lookup"><span data-stu-id="c9715-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="c9715-288">Utilisation `[EnableCors]` de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="c9715-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="c9715-289">ASP.NET Core répond à la demande options avant le vol.</span><span class="sxs-lookup"><span data-stu-id="c9715-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="c9715-290">L’activation de LA COR sur `RequireCors` une base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les demandes automatiques de prévol.</span><span class="sxs-lookup"><span data-stu-id="c9715-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="c9715-291">La section [Test CORS](#testc) de ce document démontre ce comportement.</span><span class="sxs-lookup"><span data-stu-id="c9715-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="c9715-292">[HttpOptions] attribut pour les demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="c9715-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="c9715-293">Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="c9715-294">Dans certains scénarios, ce n’est peut-être pas le cas.</span><span class="sxs-lookup"><span data-stu-id="c9715-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="c9715-295">Par exemple, utiliser [CORS avec itinéraire de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="c9715-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9715-296">Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des paramètres pour les demandes OPTIONS :</span><span class="sxs-lookup"><span data-stu-id="c9715-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="c9715-297">Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="c9715-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c9715-298">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="c9715-298">Set the preflight expiration time</span></span>

<span data-ttu-id="c9715-299">L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="c9715-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c9715-300">Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c9715-301">Fonctionnement du CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-301">How CORS works</span></span>

<span data-ttu-id="c9715-302">Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9715-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c9715-303">CORS **n’est pas** une fonction de sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="c9715-304">CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="c9715-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c9715-305">Par exemple, un acteur malveillant peut utiliser [le script cross-Site (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="c9715-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c9715-306">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="c9715-307">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c9715-308">Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c9715-309">Par exemple, l’un des outils suivants affichera la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="c9715-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c9715-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c9715-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c9715-311">Postman</span><span class="sxs-lookup"><span data-stu-id="c9715-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c9715-312">.NET HttpClient (en)</span><span class="sxs-lookup"><span data-stu-id="c9715-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c9715-313">Un navigateur Web en entrant l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="c9715-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c9715-314">C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.</span><span class="sxs-lookup"><span data-stu-id="c9715-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c9715-315">Les navigateurs sans CORS ne peuvent pas faire de demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="c9715-316">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="c9715-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c9715-317">JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c9715-318">Les scripts sont autorisés à être chargés à l’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c9715-319">Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c9715-320">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c9715-321">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c9715-322">Le [bouton de test PUT](https://cors3.azurewebsites.net/test) sur l’échantillon déployé [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="c9715-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="c9715-323">Ce qui suit est un exemple d’une demande `https://cors1.azurewebsites.net/api/values`d’origine croisée du bouton de test [Valeurs](https://cors3.azurewebsites.net/) à .</span><span class="sxs-lookup"><span data-stu-id="c9715-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="c9715-324">L’en-tête: `Origin`</span><span class="sxs-lookup"><span data-stu-id="c9715-324">The `Origin` header:</span></span>

* <span data-ttu-id="c9715-325">Fournit le domaine du site qui fait la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="c9715-326">Est nécessaire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="c9715-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="c9715-327">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="c9715-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="c9715-328">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="c9715-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="c9715-329">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="c9715-329">**Request headers**</span></span>

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

<span data-ttu-id="c9715-330">Dans `OPTIONS` les demandes, le serveur définit `Access-Control-Allow-Origin: {allowed origin}` l’en-tête **de réponse** dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="c9715-331">Par exemple, [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé, Supprimer `OPTIONS` la demande de bouton [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) contient les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="c9715-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="c9715-332">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="c9715-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="c9715-333">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="c9715-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="c9715-334">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="c9715-334">**Request headers**</span></span>

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

<span data-ttu-id="c9715-335">Dans les **en-têtes de réponse**précédents, le serveur définit l’en-tête [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="c9715-336">La `https://cors1.azurewebsites.net` valeur de cet `Origin` en-tête correspond à l’en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="c9715-337">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> on appelle, la `Access-Control-Allow-Origin: *`valeur , la wildcard, est retournée.</span><span class="sxs-lookup"><span data-stu-id="c9715-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="c9715-338">`AllowAnyOrigin`permet n’importe quelle origine.</span><span class="sxs-lookup"><span data-stu-id="c9715-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="c9715-339">Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="c9715-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c9715-340">Plus précisément, le navigateur interdit la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c9715-341">Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.</span><span class="sxs-lookup"><span data-stu-id="c9715-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="c9715-342">Afficher les demandes OPTIONS</span><span class="sxs-lookup"><span data-stu-id="c9715-342">Display OPTIONS requests</span></span>

<span data-ttu-id="c9715-343">Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sur l’onglet réseau des outils F12.</span><span class="sxs-lookup"><span data-stu-id="c9715-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="c9715-344">Pour afficher les demandes OPTIONS dans ces navigateurs :</span><span class="sxs-lookup"><span data-stu-id="c9715-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="c9715-345">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="c9715-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="c9715-346">désactiver le drapeau.</span><span class="sxs-lookup"><span data-stu-id="c9715-346">disable the flag.</span></span>
* <span data-ttu-id="c9715-347">redémarrer.</span><span class="sxs-lookup"><span data-stu-id="c9715-347">restart.</span></span>

<span data-ttu-id="c9715-348">Firefox affiche les demandes OPTIONS par défaut.</span><span class="sxs-lookup"><span data-stu-id="c9715-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c9715-349">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="c9715-349">CORS in IIS</span></span>

<span data-ttu-id="c9715-350">Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="c9715-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c9715-351">Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="c9715-352">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-352">Test CORS</span></span>

<span data-ttu-id="c9715-353">[L’exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) a du code pour tester CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="c9715-354">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c9715-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="c9715-355">L’échantillon est un projet API avec Razor Pages ajouté:</span><span class="sxs-lookup"><span data-stu-id="c9715-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="c9715-356">`WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="c9715-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="c9715-357">Ce `ValuesController` qui suit fournit les paramètres de test :</span><span class="sxs-lookup"><span data-stu-id="c9715-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="c9715-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le forfait [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet et affiche les informations d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="c9715-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="c9715-359">Testez le code d’échantillon précédent en utilisant l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c9715-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="c9715-360">Utilisez l’application [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)d’échantillon déployée à .</span><span class="sxs-lookup"><span data-stu-id="c9715-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="c9715-361">Il n’est pas nécessaire de télécharger l’échantillon.</span><span class="sxs-lookup"><span data-stu-id="c9715-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="c9715-362">Exécutez l’échantillon avec `dotnet run` `https://localhost:5001`l’utilisation de l’URL par défaut de .</span><span class="sxs-lookup"><span data-stu-id="c9715-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="c9715-363">Exécuter l’échantillon de Visual Studio avec le port réglé à `https://localhost:44398`44398 pour une URL de .</span><span class="sxs-lookup"><span data-stu-id="c9715-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="c9715-364">Utilisation d’un navigateur avec les outils F12 :</span><span class="sxs-lookup"><span data-stu-id="c9715-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="c9715-365">Sélectionnez le bouton **Valeurs** et examinez les en-têtes de l’onglet **Réseau.**</span><span class="sxs-lookup"><span data-stu-id="c9715-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="c9715-366">Sélectionnez le bouton **de test PUT.**</span><span class="sxs-lookup"><span data-stu-id="c9715-366">Select the **PUT test** button.</span></span> <span data-ttu-id="c9715-367">Voir [afficher les demandes d’instructions options](#options) sur l’affichage de la demande OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="c9715-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="c9715-368">Le **test PUT** crée deux demandes, une demande de prévol OPTIONS et la demande PUT.</span><span class="sxs-lookup"><span data-stu-id="c9715-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="c9715-369">Sélectionnez **`GetValues2 [DisableCors]`** le bouton pour déclencher une demande CORS ratée.</span><span class="sxs-lookup"><span data-stu-id="c9715-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="c9715-370">Comme mentionné dans le document, la réponse renvoie 200 succès, mais la demande corS n’est pas faite.</span><span class="sxs-lookup"><span data-stu-id="c9715-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="c9715-371">Sélectionnez l’onglet **Console** pour voir l’erreur CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="c9715-372">Selon le navigateur, une erreur similaire à ce qui suit s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c9715-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="c9715-373">L’accès `'https://cors1.azurewebsites.net/api/values/GetValues2'` à `'https://cors3.azurewebsites.net'` aller chercher à partir de l’origine a été bloqué par la politique du CORS : aucun en-tête « Access-Control-Allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="c9715-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c9715-374">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="c9715-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="c9715-375">Les points de terminaison compatibles CORS peuvent être testés avec un outil, comme [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), ou [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="c9715-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c9715-376">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c9715-377">Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="c9715-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c9715-378">Il n’est pas nécessaire pour CORS Middleware de traiter la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c9715-379">Les en-têtes corS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="c9715-380">La commande `curl` suivante utilise pour émettre une demande OPTIONS avec des informations :</span><span class="sxs-lookup"><span data-stu-id="c9715-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="c9715-381">Test CORS avec itinéraire de point de terminaison et [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="c9715-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="c9715-382">Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf).</span><span class="sxs-lookup"><span data-stu-id="c9715-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c9715-383">Considérez le code suivant qui utilise [le routage de point final pour activer CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="c9715-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="c9715-384">Ce `TodoItems1Controller` qui suit fournit des paramètres de test :</span><span class="sxs-lookup"><span data-stu-id="c9715-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="c9715-385">Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=1) de [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé .</span><span class="sxs-lookup"><span data-stu-id="c9715-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="c9715-386">Les boutons **Supprimer [EnableCors]** et **GET [EnableCors]** réussissent, `[EnableCors]` parce que les critères d’évaluation ont et répondent aux demandes avant le vol.</span><span class="sxs-lookup"><span data-stu-id="c9715-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="c9715-387">Les autres critères d’évaluation échouent.</span><span class="sxs-lookup"><span data-stu-id="c9715-387">The other endpoints fails.</span></span> <span data-ttu-id="c9715-388">Le bouton **GET** échoue, car le [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :</span><span class="sxs-lookup"><span data-stu-id="c9715-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="c9715-389">Ce `TodoItems2Controller` qui suit fournit des points de terminaison similaires, mais comprend un code explicite pour répondre aux demandes OPTIONS :</span><span class="sxs-lookup"><span data-stu-id="c9715-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="c9715-390">Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=2) de l’échantillon déployé.</span><span class="sxs-lookup"><span data-stu-id="c9715-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="c9715-391">Dans la liste **d’abandon du contrôleur,** sélectionnez **Preflight,** puis **Set Controller**.</span><span class="sxs-lookup"><span data-stu-id="c9715-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="c9715-392">Tous les appels corS aux points `TodoItems2Controller` de terminaison réussissent.</span><span class="sxs-lookup"><span data-stu-id="c9715-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9715-393">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c9715-393">Additional resources</span></span>

* [<span data-ttu-id="c9715-394">Partage des ressources inter-origine (CORS)</span><span class="sxs-lookup"><span data-stu-id="c9715-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c9715-395">Commencer avec le module CORS DE l’IIS</span><span class="sxs-lookup"><span data-stu-id="c9715-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9715-396">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c9715-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c9715-397">Cet article montre comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c9715-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c9715-398">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="c9715-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c9715-399">Cette restriction est appelée la *même politique d’origine*.</span><span class="sxs-lookup"><span data-stu-id="c9715-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c9715-400">La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="c9715-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c9715-401">Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application.</span><span class="sxs-lookup"><span data-stu-id="c9715-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="c9715-402">Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="c9715-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c9715-403">[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :</span><span class="sxs-lookup"><span data-stu-id="c9715-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c9715-404">Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="c9715-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c9715-405">N’est **pas** une fonction de sécurité, CORS détend la sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c9715-406">Une API n’est pas plus sûre en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c9715-407">Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="c9715-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c9715-408">Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="c9715-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c9715-409">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="c9715-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c9715-410">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9715-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c9715-411">Même origine</span><span class="sxs-lookup"><span data-stu-id="c9715-411">Same origin</span></span>

<span data-ttu-id="c9715-412">Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="c9715-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c9715-413">Ces deux URL ont la même origine :</span><span class="sxs-lookup"><span data-stu-id="c9715-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c9715-414">Ces URL ont des origines différentes des deux URL précédentes :</span><span class="sxs-lookup"><span data-stu-id="c9715-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c9715-415">`https://example.net`&ndash; Domaine différent</span><span class="sxs-lookup"><span data-stu-id="c9715-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c9715-416">`https://www.example.com/foo.html`&ndash; Sous-comté différent</span><span class="sxs-lookup"><span data-stu-id="c9715-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c9715-417">`http://example.com/foo.html`&ndash; Différents schémas</span><span class="sxs-lookup"><span data-stu-id="c9715-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c9715-418">`https://example.com:9000/foo.html`&ndash; Port différent</span><span class="sxs-lookup"><span data-stu-id="c9715-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="c9715-419">Internet Explorer ne considère pas le port en comparant les origines.</span><span class="sxs-lookup"><span data-stu-id="c9715-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c9715-420">CORS avec la politique nommée et middleware</span><span class="sxs-lookup"><span data-stu-id="c9715-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="c9715-421">CORS Middleware traite les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c9715-422">Le code suivant permet CORS pour l’ensemble de l’application avec l’origine spécifiée:</span><span class="sxs-lookup"><span data-stu-id="c9715-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="c9715-423">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c9715-423">The preceding code:</span></span>

* <span data-ttu-id="c9715-424">Définit le nom\_de la politique à "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="c9715-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="c9715-425">Le nom de la politique est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="c9715-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c9715-426">Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension, qui permet CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="c9715-427">Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="c9715-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c9715-428">Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet.</span><span class="sxs-lookup"><span data-stu-id="c9715-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c9715-429">[Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.</span><span class="sxs-lookup"><span data-stu-id="c9715-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="c9715-430">L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="c9715-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c9715-431">Pour plus d’informations, voir [les options de politique CORS](#cpo) dans ce document .</span><span class="sxs-lookup"><span data-stu-id="c9715-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="c9715-432">La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut enchaîner les méthodes, comme le montre le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c9715-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="c9715-433">Remarque : L’URL **ne** doit`/`pas contenir de barre oblique de fuite ().</span><span class="sxs-lookup"><span data-stu-id="c9715-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c9715-434">Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="c9715-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="c9715-435">Le code suivant applique les stratégies CORS à tous les points de terminaison des applications via CORS Middleware :</span><span class="sxs-lookup"><span data-stu-id="c9715-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="c9715-436">Remarque `UseCors` : doit `UseMvc`être appelée avant .</span><span class="sxs-lookup"><span data-stu-id="c9715-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="c9715-437">Voir [Activer CORS dans Razor Pages, contrôleurs et méthodes d’action](#ecors) pour appliquer la stratégie CORS au niveau de la page/contrôleur/action.</span><span class="sxs-lookup"><span data-stu-id="c9715-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="c9715-438">Consultez [Test CORS](#test) pour les instructions sur le code de test similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="c9715-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c9715-439">Activez CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="c9715-439">Enable CORS with attributes</span></span>

<span data-ttu-id="c9715-440">L’attribut [ &lbrack;&rbrack; EnableCors](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale.</span><span class="sxs-lookup"><span data-stu-id="c9715-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c9715-441">L’attribut `[EnableCors]` permet CORS pour certains points de fin, plutôt que tous les points finaux.</span><span class="sxs-lookup"><span data-stu-id="c9715-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="c9715-442">Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` pour spécifier une stratégie.</span><span class="sxs-lookup"><span data-stu-id="c9715-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="c9715-443">L’attribut `[EnableCors]` peut être appliqué à :</span><span class="sxs-lookup"><span data-stu-id="c9715-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="c9715-444">Razor Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c9715-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="c9715-445">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="c9715-445">Controller</span></span>
* <span data-ttu-id="c9715-446">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="c9715-446">Controller action method</span></span>

<span data-ttu-id="c9715-447">Vous pouvez appliquer différentes stratégies au contrôleur/page-modèle/action avec l’attribut. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="c9715-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="c9715-448">Lorsque `[EnableCors]` l’attribut est appliqué à un modèle/méthode d’action de contrôleurs/pages, et que le CORS est activé dans middleware, ***les deux*** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="c9715-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c9715-449">Nous recommandons ***de ne pas*** combiner les politiques.</span><span class="sxs-lookup"><span data-stu-id="c9715-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="c9715-450">Utilisez `[EnableCors]` l’attribut ou middleware,**pas les deux**.</span><span class="sxs-lookup"><span data-stu-id="c9715-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="c9715-451">Lors `[EnableCors]`de l’utilisation, ne définissez **pas** une stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="c9715-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="c9715-452">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="c9715-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c9715-453">Le code suivant crée une politique de `"AnotherPolicy"`défaut corS et une stratégie nommée :</span><span class="sxs-lookup"><span data-stu-id="c9715-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="c9715-454">Désactiver le CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-454">Disable CORS</span></span>

<span data-ttu-id="c9715-455">[ &lbrack;L’attribut&rbrack; DisableCors](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive CORS pour le contrôleur/page-modèle/action.</span><span class="sxs-lookup"><span data-stu-id="c9715-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c9715-456">Options de politique CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-456">CORS policy options</span></span>

<span data-ttu-id="c9715-457">Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :</span><span class="sxs-lookup"><span data-stu-id="c9715-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c9715-458">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c9715-459">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c9715-460">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="c9715-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c9715-461">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="c9715-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c9715-462">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="c9715-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c9715-463">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="c9715-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c9715-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans .</span><span class="sxs-lookup"><span data-stu-id="c9715-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c9715-465">Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.</span><span class="sxs-lookup"><span data-stu-id="c9715-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c9715-466">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-466">Set the allowed origins</span></span>

<span data-ttu-id="c9715-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ).</span><span class="sxs-lookup"><span data-stu-id="c9715-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c9715-468">`AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c9715-469">Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal.</span><span class="sxs-lookup"><span data-stu-id="c9715-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c9715-470">Pour une application sécurisée, spécifiez une liste exacte des origines si le client doit s’autoriser à accéder aux ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="c9715-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="c9715-471">`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c9715-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c9715-472">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9715-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c9715-474">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="c9715-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c9715-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c9715-476">Permet n’importe quelle méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="c9715-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="c9715-477">Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c9715-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c9715-478">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c9715-479">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="c9715-479">Set the allowed request headers</span></span>

<span data-ttu-id="c9715-480">Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande *en-têtes*, appelez et spécifier les en-têtes autorisés:</span><span class="sxs-lookup"><span data-stu-id="c9715-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c9715-481">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c9715-482">Ce paramètre affecte les `Access-Control-Request-Headers` demandes de prévol et l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c9715-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="c9715-483">Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="c9715-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9715-484">CORS Middleware permet toujours d’envoyer quatre en-têtes dans le `Access-Control-Request-Headers` code indépendamment des valeurs configurées dans CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="c9715-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="c9715-485">Cette liste d’en-têtes comprend :</span><span class="sxs-lookup"><span data-stu-id="c9715-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="c9715-486">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="c9715-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="c9715-487">CORS Middleware répond avec succès à une demande de `Content-Language` prévol avec l’en-tête de demande suivant parce qu’il est toujours sur la liste blanche :</span><span class="sxs-lookup"><span data-stu-id="c9715-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c9715-488">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="c9715-488">Set the exposed response headers</span></span>

<span data-ttu-id="c9715-489">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c9715-490">Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="c9715-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c9715-491">Les en-têtes de réponse qui sont disponibles par défaut sont les :</span><span class="sxs-lookup"><span data-stu-id="c9715-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c9715-492">La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse .</span><span class="sxs-lookup"><span data-stu-id="c9715-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c9715-493">Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c9715-494">Informations d’identification dans les demandes d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="c9715-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c9715-495">Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR.</span><span class="sxs-lookup"><span data-stu-id="c9715-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c9715-496">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c9715-497">Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9715-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c9715-498">Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.</span><span class="sxs-lookup"><span data-stu-id="c9715-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c9715-499">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="c9715-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c9715-500">Utilisation de jQuery:</span><span class="sxs-lookup"><span data-stu-id="c9715-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c9715-501">Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="c9715-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c9715-502">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="c9715-502">The server must allow the credentials.</span></span> <span data-ttu-id="c9715-503">Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="c9715-504">La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c9715-505">Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="c9715-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c9715-506">Autoriser les informations d’identification inter-origine est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c9715-507">Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c9715-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c9715-508">La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="c9715-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="c9715-509">Demandes de prévol</span><span class="sxs-lookup"><span data-stu-id="c9715-509">Preflight requests</span></span>

<span data-ttu-id="c9715-510">Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant de faire la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="c9715-511">Cette demande est appelée une *demande de prévol*.</span><span class="sxs-lookup"><span data-stu-id="c9715-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="c9715-512">Le navigateur peut sauter la demande de prévol si les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="c9715-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="c9715-513">La méthode de demande est GET, HEAD ou POST.</span><span class="sxs-lookup"><span data-stu-id="c9715-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c9715-514">L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="c9715-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c9715-515">L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="c9715-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c9715-516">La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet.</span><span class="sxs-lookup"><span data-stu-id="c9715-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c9715-517">La spécification CORS appelle ces en-têtes *auteur demande en-têtes*.</span><span class="sxs-lookup"><span data-stu-id="c9715-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="c9715-518">La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .</span><span class="sxs-lookup"><span data-stu-id="c9715-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c9715-519">Voici un exemple de demande de prévol :</span><span class="sxs-lookup"><span data-stu-id="c9715-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="c9715-520">La demande avant vol utilise la méthode HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="c9715-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="c9715-521">Il comprend deux en-têtes spéciaux :</span><span class="sxs-lookup"><span data-stu-id="c9715-521">It includes two special headers:</span></span>

* <span data-ttu-id="c9715-522">`Access-Control-Request-Method`: La méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c9715-523">`Access-Control-Request-Headers`: Une liste d’en-têtes de demande que l’application fixe sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c9715-524">Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="c9715-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="c9715-525">Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="c9715-526">Voir [[HttpOptions] attribut pour les demandes de prévol](#pro).</span><span class="sxs-lookup"><span data-stu-id="c9715-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="c9715-527">Une demande de prévol corS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes qui sont envoyés avec la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="c9715-528">Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="c9715-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c9715-529">Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c9715-530">Les navigateurs ne sont pas `Access-Control-Request-Headers`tout à fait cohérents dans la façon dont ils se définissent .</span><span class="sxs-lookup"><span data-stu-id="c9715-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c9715-531">Si vous définissez des `"*"` en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>à autre chose `Accept` `Content-Type`que `Origin`(ou utiliser ), vous devriez inclure au moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="c9715-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="c9715-532">Ce qui suit est une réponse exemple à la demande de prévol (en supposant que le serveur permet la demande):</span><span class="sxs-lookup"><span data-stu-id="c9715-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="c9715-533">La réponse `Access-Control-Allow-Methods` comprend un en-tête qui `Access-Control-Allow-Headers` répertorie les méthodes autorisées et optionnellement un en-tête, qui répertorie les en-têtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="c9715-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="c9715-534">Si la demande de prévol réussit, le navigateur envoie la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="c9715-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="c9715-535">Si la demande de prévol est refusée, l’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c9715-536">Par conséquent, le navigateur ne tente pas la demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c9715-537">Définissez l’heure d’expiration avant le vol</span><span class="sxs-lookup"><span data-stu-id="c9715-537">Set the preflight expiration time</span></span>

<span data-ttu-id="c9715-538">L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="c9715-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c9715-539">Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :</span><span class="sxs-lookup"><span data-stu-id="c9715-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c9715-540">Fonctionnement du CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-540">How CORS works</span></span>

<span data-ttu-id="c9715-541">Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9715-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c9715-542">CORS **n’est pas** une fonction de sécurité.</span><span class="sxs-lookup"><span data-stu-id="c9715-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="c9715-543">CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="c9715-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c9715-544">Par exemple, un acteur malveillant peut utiliser [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="c9715-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c9715-545">Votre API n’est pas plus sûr en permettant CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="c9715-546">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c9715-547">Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c9715-548">Par exemple, l’un des outils suivants affichera la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="c9715-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c9715-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c9715-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c9715-550">Postman</span><span class="sxs-lookup"><span data-stu-id="c9715-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c9715-551">.NET HttpClient (en)</span><span class="sxs-lookup"><span data-stu-id="c9715-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c9715-552">Un navigateur Web en entrant l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="c9715-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c9715-553">C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.</span><span class="sxs-lookup"><span data-stu-id="c9715-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c9715-554">Les navigateurs (sans CORS) ne peuvent pas faire de demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="c9715-555">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="c9715-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c9715-556">JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c9715-557">Les scripts sont autorisés à être chargés à l’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c9715-558">Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c9715-559">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c9715-560">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="c9715-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c9715-561">Ce qui suit est un exemple d’une demande d’origine croisée.</span><span class="sxs-lookup"><span data-stu-id="c9715-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="c9715-562">L’en-tête `Origin` fournit le domaine du site qui fait la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="c9715-563">L’en-tête `Origin` est nécessaire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="c9715-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="c9715-564">Si le serveur autorise la `Access-Control-Allow-Origin` demande, il définit l’en-tête dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="c9715-565">La valeur de cet `Origin` en-tête correspond soit à `"*"`l’en-tête de la demande ou est la valeur wildcard , ce qui signifie que toute origine est autorisée:</span><span class="sxs-lookup"><span data-stu-id="c9715-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="c9715-566">Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue.</span><span class="sxs-lookup"><span data-stu-id="c9715-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c9715-567">Plus précisément, le navigateur interdit la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c9715-568">Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.</span><span class="sxs-lookup"><span data-stu-id="c9715-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="c9715-569">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="c9715-569">Test CORS</span></span>

<span data-ttu-id="c9715-570">Pour tester CORS :</span><span class="sxs-lookup"><span data-stu-id="c9715-570">To test CORS:</span></span>

1. <span data-ttu-id="c9715-571">[Créer un projet API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="c9715-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="c9715-572">Alternativement, vous pouvez [télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="c9715-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="c9715-573">Activez CORS à l’aide d’une des approches de ce document.</span><span class="sxs-lookup"><span data-stu-id="c9715-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="c9715-574">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c9715-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="c9715-575">`WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="c9715-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="c9715-576">Créez un projet d’application web (Razor Pages ou MVC).</span><span class="sxs-lookup"><span data-stu-id="c9715-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="c9715-577">L’échantillon utilise Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c9715-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="c9715-578">Vous pouvez créer l’application web dans la même solution que le projet API.</span><span class="sxs-lookup"><span data-stu-id="c9715-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="c9715-579">Ajoutez le code mis en évidence suivant au fichier *Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c9715-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="c9715-580">Dans le code `url: 'https://<web app>.azurewebsites.net/api/values/1',` précédent, remplacez par l’URL de l’application déployée.</span><span class="sxs-lookup"><span data-stu-id="c9715-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="c9715-581">Déployer le projet API.</span><span class="sxs-lookup"><span data-stu-id="c9715-581">Deploy the API project.</span></span> <span data-ttu-id="c9715-582">Par exemple, [déployez-vous à Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="c9715-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="c9715-583">Exécutez les pages Razor ou l’application MVC depuis le bureau et cliquez sur le bouton **Test.**</span><span class="sxs-lookup"><span data-stu-id="c9715-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="c9715-584">Utilisez les outils F12 pour passer en revue les messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="c9715-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="c9715-585">Retirez l’origine `WithOrigins` locale et déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="c9715-586">Alternativement, exécutez l’application client avec un port différent.</span><span class="sxs-lookup"><span data-stu-id="c9715-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="c9715-587">Par exemple, dirigez-vous à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c9715-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="c9715-588">Test avec l’application client.</span><span class="sxs-lookup"><span data-stu-id="c9715-588">Test with the client app.</span></span> <span data-ttu-id="c9715-589">Les échecs de CORS renvoient une erreur, mais le message d’erreur n’est pas disponible pour JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c9715-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="c9715-590">Utilisez l’onglet console dans les outils F12 pour voir l’erreur.</span><span class="sxs-lookup"><span data-stu-id="c9715-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="c9715-591">Selon le navigateur, vous obtenez une erreur (dans la console d’outils F12) similaire à ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="c9715-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="c9715-592">Utilisation de Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="c9715-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="c9715-593">**SEC7120: [CORS] `https://localhost:44375` L’origine `https://localhost:44375` n’a pas trouvé dans l’en-tête de réponse Access-Control-Allow-Origin pour la ressource d’origine croisée à`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="c9715-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="c9715-594">Utilisation de Chrome:</span><span class="sxs-lookup"><span data-stu-id="c9715-594">Using Chrome:</span></span>

     <span data-ttu-id="c9715-595">**L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la politique corS: Aucun «Accès-Contrôle-Autoriser-Origin» en-tête n’est présent sur la ressource demandée.**</span><span class="sxs-lookup"><span data-stu-id="c9715-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="c9715-596">Les paramètres de terminaison compatibles CORS peuvent être testés avec un outil, comme [Fiddler](https://www.telerik.com/fiddler) ou [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="c9715-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c9715-597">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c9715-598">Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="c9715-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c9715-599">Il n’est pas nécessaire pour CORS Middleware de traiter la demande.</span><span class="sxs-lookup"><span data-stu-id="c9715-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c9715-600">Les en-têtes corS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="c9715-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c9715-601">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="c9715-601">CORS in IIS</span></span>

<span data-ttu-id="c9715-602">Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="c9715-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c9715-603">Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="c9715-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9715-604">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c9715-604">Additional resources</span></span>

* [<span data-ttu-id="c9715-605">Partage des ressources inter-origine (CORS)</span><span class="sxs-lookup"><span data-stu-id="c9715-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c9715-606">Commencer avec le module CORS DE l’IIS</span><span class="sxs-lookup"><span data-stu-id="c9715-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
