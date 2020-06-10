---
title: Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS comme norme pour autoriser ou rejeter des demandes Cross-Origin dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: a78aff2d2e16f36ed034e6af110d7ed763271583
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105751"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="cf45b-103">Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf45b-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf45b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="cf45b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="cf45b-105">Cet article explique comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf45b-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="cf45b-106">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="cf45b-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="cf45b-107">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="cf45b-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="cf45b-108">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="cf45b-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="cf45b-109">Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="cf45b-110">Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="cf45b-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="cf45b-111">[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :</span><span class="sxs-lookup"><span data-stu-id="cf45b-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="cf45b-112">Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="cf45b-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="cf45b-113">N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="cf45b-114">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="cf45b-115">Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="cf45b-116">Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="cf45b-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="cf45b-117">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="cf45b-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="cf45b-118">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf45b-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="cf45b-119">Même origine</span><span class="sxs-lookup"><span data-stu-id="cf45b-119">Same origin</span></span>

<span data-ttu-id="cf45b-120">Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="cf45b-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="cf45b-121">Ces deux URL ont le même origine :</span><span class="sxs-lookup"><span data-stu-id="cf45b-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="cf45b-122">Ces URL ont des origines différentes des deux précédentes URL :</span><span class="sxs-lookup"><span data-stu-id="cf45b-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="cf45b-123">`https://example.net`: Domaine différent</span><span class="sxs-lookup"><span data-stu-id="cf45b-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="cf45b-124">`https://www.example.com/foo.html`: Autre sous-domaine</span><span class="sxs-lookup"><span data-stu-id="cf45b-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="cf45b-125">`http://example.com/foo.html`: Schéma différent</span><span class="sxs-lookup"><span data-stu-id="cf45b-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="cf45b-126">`https://example.com:9000/foo.html`: Autre port</span><span class="sxs-lookup"><span data-stu-id="cf45b-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="cf45b-127">Activez CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-127">Enable CORS</span></span>

<span data-ttu-id="cf45b-128">Il existe trois façons d’activer CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="cf45b-129">Dans l’intergiciel (middleware) à l’aide d’une [stratégie nommée](#np) ou d’une [stratégie par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="cf45b-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="cf45b-130">Utilisation du [routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="cf45b-131">Avec l’attribut [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="cf45b-132">L’utilisation de l’attribut [[EnableCors]](#attr) avec une stratégie nommée offre un contrôle plus fin pour limiter les points de terminaison qui prennent en charge cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="cf45b-133">Chaque approche est décrite en détail dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="cf45b-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="cf45b-134">CORS avec la stratégie et l’intergiciel (middleware) nommés</span><span class="sxs-lookup"><span data-stu-id="cf45b-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="cf45b-135">L’intergiciel (middleware) CORS gère les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="cf45b-136">Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :</span><span class="sxs-lookup"><span data-stu-id="cf45b-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="cf45b-137">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cf45b-137">The preceding code:</span></span>

* <span data-ttu-id="cf45b-138">Définit le nom de la stratégie sur `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="cf45b-139">Le nom de la stratégie est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="cf45b-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="cf45b-140">Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension et spécifie la `_myAllowSpecificOrigins` stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="cf45b-141">`UseCors`Ajoute l’intergiciel (middleware) CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="cf45b-142">L’appel à `UseCors` doit être placé après `UseRouting` , mais avant `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="cf45b-143">Pour plus d’informations, consultez [ordre des intergiciels (middleware](xref:fundamentals/middleware/index#middleware-order)).</span><span class="sxs-lookup"><span data-stu-id="cf45b-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="cf45b-144">Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cf45b-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cf45b-145">L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet.</span><span class="sxs-lookup"><span data-stu-id="cf45b-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="cf45b-146">Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="cf45b-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="cf45b-147">Active la `_myAllowSpecificOrigins` stratégie cors pour tous les points de terminaison de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="cf45b-148">Consultez [routage des points de terminaison](#ecors) pour appliquer une stratégie cors à des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="cf45b-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="cf45b-149">Avec le routage de point de terminaison, l’intergiciel (middleware) CORS **doit** être configuré pour s’exécuter entre les appels à `UseRouting` et `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-149">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="cf45b-150">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="cf45b-151">L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :</span><span class="sxs-lookup"><span data-stu-id="cf45b-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="cf45b-152">Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="cf45b-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="cf45b-153">Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être chaînées, comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cf45b-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="cf45b-154">Remarque : l’URL spécifiée **ne doit pas** contenir de barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="cf45b-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="cf45b-155">Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="cf45b-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="cf45b-156">CORS avec la stratégie et l’intergiciel (middleware) par défaut</span><span class="sxs-lookup"><span data-stu-id="cf45b-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="cf45b-157">Le code en surbrillance suivant active la stratégie CORS par défaut :</span><span class="sxs-lookup"><span data-stu-id="cf45b-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="cf45b-158">Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="cf45b-159">Activer cors avec routage du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="cf45b-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="cf45b-160">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf).</span><span class="sxs-lookup"><span data-stu-id="cf45b-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="cf45b-161">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [test cors avec routage des points de terminaison et [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="cf45b-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="cf45b-162">Avec le routage de point de terminaison, CORS peut être activé sur une base par point de terminaison à l’aide de l' <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ensemble de méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="cf45b-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="cf45b-163">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cf45b-163">In the preceding code:</span></span>

* <span data-ttu-id="cf45b-164">`app.UseCors`active l’intergiciel (middleware) CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="cf45b-165">Étant donné qu’une stratégie par défaut n’a pas été configurée, `app.UseCors()` seuls n’activent cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="cf45b-166">Les `/echo` points de terminaison et des contrôleurs autorisent les demandes Cross-Origin à l’aide de la stratégie spécifiée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="cf45b-167">Les `/echo2` Razor points de terminaison et pages n’autorisent **pas** les demandes Cross-Origin, car aucune stratégie par défaut n’a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-167">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="cf45b-168">L’attribut [[DisableCors]](#dc) ne désactive **pas** cors qui a été activé par le routage de point de terminaison avec `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-168">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="cf45b-169">Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test de code similaire au précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="cf45b-170">Activer CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="cf45b-170">Enable CORS with attributes</span></span>

<span data-ttu-id="cf45b-171">L’activation de CORS avec l’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et l’application d’une stratégie nommée uniquement aux points de terminaison qui requièrent cors fournissent le contrôle le plus fin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="cf45b-172">L’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de cors globalement.</span><span class="sxs-lookup"><span data-stu-id="cf45b-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="cf45b-173">L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="cf45b-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="cf45b-174">`[EnableCors]`spécifie la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="cf45b-175">`[EnableCors("{Policy String}")]`spécifie une stratégie nommée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="cf45b-176">L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cf45b-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="cf45b-177">Pagination`PageModel`</span><span class="sxs-lookup"><span data-stu-id="cf45b-177"> Page `PageModel`</span></span>
* <span data-ttu-id="cf45b-178">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="cf45b-178">Controller</span></span>
* <span data-ttu-id="cf45b-179">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="cf45b-179">Controller action method</span></span>

<span data-ttu-id="cf45b-180">Différentes stratégies peuvent être appliquées à des contrôleurs, des modèles de page ou des méthodes d’action avec l' `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="cf45b-181">Lorsque l' `[EnableCors]` attribut est appliqué à un contrôleur, à un modèle de page ou à une méthode d’action, et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="cf45b-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="cf45b-182">**Nous vous recommandons de combiner les stratégies. Utilisez l'** `[EnableCors]` **attribut ou l’intergiciel (middleware), pas les deux dans la même application.**</span><span class="sxs-lookup"><span data-stu-id="cf45b-182">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="cf45b-183">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="cf45b-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="cf45b-184">Le code suivant crée deux stratégies CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="cf45b-185">Pour un meilleur contrôle de la limitation des demandes CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="cf45b-186">Utilisez `[EnableCors("MyPolicy")]` avec une stratégie nommée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="cf45b-187">Ne définissez pas de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-187">Don't define a default policy.</span></span>
* <span data-ttu-id="cf45b-188">N’utilisez pas le [routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="cf45b-189">Le code de la section suivante répond à la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="cf45b-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="cf45b-190">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="cf45b-191">Désactiver CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-191">Disable CORS</span></span>

<span data-ttu-id="cf45b-192">L’attribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive **pas** cors qui a été activé par le routage des [points de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="cf45b-193">Le code suivant définit la stratégie CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="cf45b-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="cf45b-194">Le code suivant désactive CORS pour l' `GetValues2` action :</span><span class="sxs-lookup"><span data-stu-id="cf45b-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="cf45b-195">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cf45b-195">The preceding code:</span></span>

* <span data-ttu-id="cf45b-196">N’active pas CORS avec [routage du point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="cf45b-197">Ne définit pas une [stratégie cors par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="cf45b-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="cf45b-198">Utilise [[EnableCors ("MyPolicy")]](#attr) pour activer la `"MyPolicy"` stratégie cors pour le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="cf45b-199">Désactive CORS pour la `GetValues2` méthode.</span><span class="sxs-lookup"><span data-stu-id="cf45b-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="cf45b-200">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="cf45b-201">Options de stratégie CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-201">CORS policy options</span></span>

<span data-ttu-id="cf45b-202">Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="cf45b-203">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="cf45b-204">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="cf45b-205">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="cf45b-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="cf45b-206">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="cf45b-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="cf45b-207">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="cf45b-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="cf45b-208">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="cf45b-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="cf45b-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cf45b-210">Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="cf45b-211">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-211">Set the allowed origins</span></span>

<span data-ttu-id="cf45b-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="cf45b-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="cf45b-213">`AllowAnyOrigin`n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="cf45b-214">La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites.</span><span class="sxs-lookup"><span data-stu-id="cf45b-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="cf45b-215">Le service CORS retourne une réponse CORS non valide lorsqu’une application est configurée avec les deux méthodes.</span><span class="sxs-lookup"><span data-stu-id="cf45b-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="cf45b-216">`AllowAnyOrigin`affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="cf45b-217">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cf45b-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="cf45b-219">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="cf45b-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="cf45b-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="cf45b-221">Autorise toute méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="cf45b-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="cf45b-222">Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="cf45b-223">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="cf45b-224">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="cf45b-224">Set the allowed request headers</span></span>

<span data-ttu-id="cf45b-225">Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée [créer des en-têtes de demande](https://xhr.spec.whatwg.org/#request), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :</span><span class="sxs-lookup"><span data-stu-id="cf45b-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="cf45b-226">Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="cf45b-227">`AllowAnyHeader`affecte les demandes préliminaires et l’en-tête [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="cf45b-228">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cf45b-229">Une stratégie d’intergiciel (middleware) CORS correspondant à des en-têtes spécifiques spécifiés par `WithHeaders` n’est possible que lorsque les en-têtes envoyés dans `Access-Control-Request-Headers` correspondent exactement aux en-têtes indiqués dans `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="cf45b-230">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="cf45b-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="cf45b-231">L’intergiciel (middleware) CORS refuse une demande préliminaire avec l’en-tête de demande suivant, car `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas listé dans `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="cf45b-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="cf45b-232">L’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="cf45b-233">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="cf45b-234">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="cf45b-234">Set the exposed response headers</span></span>

<span data-ttu-id="cf45b-235">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="cf45b-236">Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="cf45b-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="cf45b-237">Les en-têtes de réponse qui sont disponibles par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="cf45b-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="cf45b-238">La spécification CORS appelle ces en *-têtes de réponse simples*en-têtes.</span><span class="sxs-lookup"><span data-stu-id="cf45b-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="cf45b-239">Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="cf45b-240">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="cf45b-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="cf45b-241">Les informations d’identification nécessitent un traitement spécial dans une demande CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="cf45b-242">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="cf45b-243">Les informations d’identification incluent les cookies et les schémas d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="cf45b-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="cf45b-244">Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="cf45b-245">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="cf45b-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="cf45b-246">Utilisation de jQuery :</span><span class="sxs-lookup"><span data-stu-id="cf45b-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="cf45b-247">Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="cf45b-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="cf45b-248">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="cf45b-248">The server must allow the credentials.</span></span> <span data-ttu-id="cf45b-249">Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="cf45b-250">La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="cf45b-251">Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="cf45b-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="cf45b-252">L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="cf45b-253">Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="cf45b-254">La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="cf45b-255">Demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="cf45b-255">Preflight requests</span></span>

<span data-ttu-id="cf45b-256">Pour certaines demandes CORS, le navigateur envoie une demande d' [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant d’effectuer la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="cf45b-257">Cette demande porte le nom de [demande préliminaire](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="cf45b-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="cf45b-258">Le navigateur peut ignorer la demande préliminaire si **toutes** les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="cf45b-258">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="cf45b-259">La méthode de demande est : obtenir, début ou publication.</span><span class="sxs-lookup"><span data-stu-id="cf45b-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="cf45b-260">L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="cf45b-261">L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="cf45b-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="cf45b-262">La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet.</span><span class="sxs-lookup"><span data-stu-id="cf45b-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="cf45b-263">La spécification CORS appelle ces en-têtes [créer des en-](https://www.w3.org/TR/cors/#author-request-headers)têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="cf45b-264">La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="cf45b-265">Voici un exemple de réponse similaire à la demande préliminaire effectuée à partir du bouton **[placer un test]** dans la section [cors de test](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="cf45b-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="cf45b-266">La demande préliminaire utilise la méthode [http options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="cf45b-267">Il peut inclure les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="cf45b-267">It may include the following headers:</span></span>

* <span data-ttu-id="cf45b-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): méthode http qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="cf45b-269">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): liste des en-têtes de requête que l’application définit sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="cf45b-270">Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="cf45b-271">Access-Control-allow-Methods</span><span class="sxs-lookup"><span data-stu-id="cf45b-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="cf45b-272">Si la demande préliminaire est refusée, l’application retourne une `200 OK` réponse, mais ne définit pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="cf45b-273">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="cf45b-274">Pour obtenir un exemple de demande préliminaire refusée, consultez la section « [test cors](#testc) » de ce document.</span><span class="sxs-lookup"><span data-stu-id="cf45b-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="cf45b-275">À l’aide des outils F12, l’application console affiche une erreur semblable à l’une des options suivantes, selon le navigateur :</span><span class="sxs-lookup"><span data-stu-id="cf45b-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="cf45b-276">Firefox : requête Cross-Origin bloquée : la même stratégie d’origine interdit la lecture de la ressource distante à l’adresse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="cf45b-277">(Raison : la demande CORS n’a pas pu être effectuée).</span><span class="sxs-lookup"><span data-stu-id="cf45b-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="cf45b-278">En savoir plus</span><span class="sxs-lookup"><span data-stu-id="cf45b-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="cf45b-279">Basé sur le chrome : l’accès à la récupération sur « https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 » à partir de l’origine « https://cors3.azurewebsites.net » a été bloqué par la stratégie cors : la réponse à la demande préliminaire ne réussit pas la vérification du contrôle d’accès : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="cf45b-280">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="cf45b-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="cf45b-281">Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="cf45b-282">Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="cf45b-283">Les navigateurs ne sont pas cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="cf45b-284">Si l’une des deux :</span><span class="sxs-lookup"><span data-stu-id="cf45b-284">If either:</span></span>

* <span data-ttu-id="cf45b-285">Les en-têtes sont définis sur une valeur autre que`"*"`</span><span class="sxs-lookup"><span data-stu-id="cf45b-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="cf45b-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>est appelé : inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="cf45b-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="cf45b-287">Code de demande préliminaire automatique</span><span class="sxs-lookup"><span data-stu-id="cf45b-287">Automatic preflight request code</span></span>

<span data-ttu-id="cf45b-288">Lorsque la stratégie CORS est appliquée :</span><span class="sxs-lookup"><span data-stu-id="cf45b-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="cf45b-289">Globalement en appelant `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="cf45b-290">À l’aide de l' `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="cf45b-291">ASP.NET Core répond à la demande d’OPTIONS préliminaires.</span><span class="sxs-lookup"><span data-stu-id="cf45b-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="cf45b-292">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les demandes de prévols automatiques.</span><span class="sxs-lookup"><span data-stu-id="cf45b-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="cf45b-293">La section « [cors de test](#testc) » de ce document illustre ce comportement.</span><span class="sxs-lookup"><span data-stu-id="cf45b-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="cf45b-294">Attribut [HttpOptions] pour les demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="cf45b-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="cf45b-295">Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement aux demandes préliminaires CORS automatiquement.</span><span class="sxs-lookup"><span data-stu-id="cf45b-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="cf45b-296">Dans certains scénarios, cela peut ne pas être le cas.</span><span class="sxs-lookup"><span data-stu-id="cf45b-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="cf45b-297">Par exemple, à l’aide de [cors avec routage du point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="cf45b-298">Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des points de terminaison pour les demandes d’options :</span><span class="sxs-lookup"><span data-stu-id="cf45b-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="cf45b-299">Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="cf45b-300">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="cf45b-300">Set the preflight expiration time</span></span>

<span data-ttu-id="cf45b-301">L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="cf45b-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="cf45b-302">Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="cf45b-303">Fonctionnement de CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-303">How CORS works</span></span>

<span data-ttu-id="cf45b-304">Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.</span><span class="sxs-lookup"><span data-stu-id="cf45b-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="cf45b-305">CORS n’est **pas** une fonctionnalité de sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="cf45b-306">CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="cf45b-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="cf45b-307">Par exemple, un acteur malveillant peut utiliser des [scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="cf45b-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="cf45b-308">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="cf45b-309">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="cf45b-310">Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="cf45b-311">Par exemple, l’un des outils suivants affiche la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="cf45b-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="cf45b-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="cf45b-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="cf45b-313">Postman</span><span class="sxs-lookup"><span data-stu-id="cf45b-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="cf45b-314">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="cf45b-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="cf45b-315">Un navigateur Web en entrant l’URL dans la barre d’adresses.</span><span class="sxs-lookup"><span data-stu-id="cf45b-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="cf45b-316">C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.</span><span class="sxs-lookup"><span data-stu-id="cf45b-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="cf45b-317">Les navigateurs sans CORS ne peuvent pas effectuer de requêtes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="cf45b-318">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="cf45b-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="cf45b-319">JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="cf45b-320">Les scripts sont autorisés à être chargés sur plusieurs origines.</span><span class="sxs-lookup"><span data-stu-id="cf45b-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="cf45b-321">La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="cf45b-322">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="cf45b-323">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="cf45b-324">[Bouton put test](https://cors3.azurewebsites.net/test) sur l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) déployé</span><span class="sxs-lookup"><span data-stu-id="cf45b-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="cf45b-325">Voici un exemple de demande Cross-Origin à partir du bouton de test des [valeurs](https://cors3.azurewebsites.net/) vers `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="cf45b-326">L' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="cf45b-326">The `Origin` header:</span></span>

* <span data-ttu-id="cf45b-327">Fournit le domaine du site qui effectue la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="cf45b-328">Est obligatoire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cf45b-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="cf45b-329">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="cf45b-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="cf45b-330">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="cf45b-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="cf45b-331">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="cf45b-331">**Request headers**</span></span>

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

<span data-ttu-id="cf45b-332">Dans `OPTIONS` les demandes, le serveur définit l’en-tête des **en-têtes de réponse** `Access-Control-Allow-Origin: {allowed origin}` dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="cf45b-333">Par exemple, l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)de requête de bouton [delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) déployé `OPTIONS` contient les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="cf45b-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="cf45b-334">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="cf45b-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="cf45b-335">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="cf45b-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="cf45b-336">**En-têtes de requête**</span><span class="sxs-lookup"><span data-stu-id="cf45b-336">**Request headers**</span></span>

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

<span data-ttu-id="cf45b-337">Dans les **en-têtes de réponse**précédents, le serveur définit l’en-tête [Access-Control-allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="cf45b-338">La `https://cors1.azurewebsites.net` valeur de cet en-tête correspond à l' `Origin` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="cf45b-339">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> est appelé, la `Access-Control-Allow-Origin: *` valeur de caractère générique est retournée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="cf45b-340">`AllowAnyOrigin`autorise toute origine.</span><span class="sxs-lookup"><span data-stu-id="cf45b-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="cf45b-341">Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="cf45b-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="cf45b-342">Plus précisément, le navigateur n’autorise pas la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="cf45b-343">Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="cf45b-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="cf45b-344">Afficher les demandes OPTIONS</span><span class="sxs-lookup"><span data-stu-id="cf45b-344">Display OPTIONS requests</span></span>

<span data-ttu-id="cf45b-345">Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sous l’onglet Réseau des outils F12.</span><span class="sxs-lookup"><span data-stu-id="cf45b-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="cf45b-346">Pour afficher les demandes OPTIONS dans ces navigateurs :</span><span class="sxs-lookup"><span data-stu-id="cf45b-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="cf45b-347">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="cf45b-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="cf45b-348">Désactivez l’indicateur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-348">disable the flag.</span></span>
* <span data-ttu-id="cf45b-349">redémarrer.</span><span class="sxs-lookup"><span data-stu-id="cf45b-349">restart.</span></span>

<span data-ttu-id="cf45b-350">Firefox affiche les demandes d’OPTIONS par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="cf45b-351">CORS dans IIS</span><span class="sxs-lookup"><span data-stu-id="cf45b-351">CORS in IIS</span></span>

<span data-ttu-id="cf45b-352">Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="cf45b-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="cf45b-353">Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="cf45b-354">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-354">Test CORS</span></span>

<span data-ttu-id="cf45b-355">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contient du code pour tester cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="cf45b-356">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="cf45b-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="cf45b-357">L’exemple est un projet d’API avec des Razor pages ajoutées :</span><span class="sxs-lookup"><span data-stu-id="cf45b-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="cf45b-358">`WithOrigins("https://localhost:<port>");`doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="cf45b-359">Les éléments suivants `ValuesController` fournissent les points de terminaison pour le test :</span><span class="sxs-lookup"><span data-stu-id="cf45b-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="cf45b-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le package NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) et affiche des informations sur l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="cf45b-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="cf45b-361">Testez l’exemple de code précédent à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="cf45b-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="cf45b-362">Utilisez l’exemple d’application déployée à l’adresse [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="cf45b-363">Il n’est pas nécessaire de télécharger l’exemple.</span><span class="sxs-lookup"><span data-stu-id="cf45b-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="cf45b-364">Exécutez l’exemple avec l' `dotnet run` URL par défaut de `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="cf45b-365">Exécutez l’exemple à partir de Visual Studio avec le port défini sur 44398 pour une URL de `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="cf45b-366">À l’aide d’un navigateur avec les outils F12 :</span><span class="sxs-lookup"><span data-stu-id="cf45b-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="cf45b-367">Sélectionnez le bouton **valeurs** et passez en revue les en-têtes sous l’onglet **réseau** .</span><span class="sxs-lookup"><span data-stu-id="cf45b-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="cf45b-368">Sélectionnez le bouton **put test** .</span><span class="sxs-lookup"><span data-stu-id="cf45b-368">Select the **PUT test** button.</span></span> <span data-ttu-id="cf45b-369">Consultez [afficher les demandes d’options](#options) pour obtenir des instructions sur l’affichage de la demande d’options.</span><span class="sxs-lookup"><span data-stu-id="cf45b-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="cf45b-370">Le **test put** crée deux demandes, une demande préliminaire options et la demande put.</span><span class="sxs-lookup"><span data-stu-id="cf45b-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="cf45b-371">Sélectionnez le **`GetValues2 [DisableCors]`** bouton pour déclencher une demande cors ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="cf45b-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="cf45b-372">Comme mentionné dans le document, la réponse retourne 200 réussite, mais la demande CORS n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="cf45b-373">Sélectionnez l’onglet **console** pour afficher l’erreur cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="cf45b-374">En fonction du navigateur, une erreur semblable à la suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="cf45b-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="cf45b-375">L’accès à l’extraction à `'https://cors1.azurewebsites.net/api/values/GetValues2'` partir de l’origine `'https://cors3.azurewebsites.net'` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="cf45b-376">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="cf45b-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="cf45b-377">Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil, tel que le terme « [bouclé](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)» ou « [postal](https://www.getpostman.com/)».</span><span class="sxs-lookup"><span data-stu-id="cf45b-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="cf45b-378">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="cf45b-379">Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="cf45b-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="cf45b-380">Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="cf45b-381">Les en-têtes CORS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="cf45b-382">La commande suivante utilise `curl` pour émettre une demande d’options avec des informations :</span><span class="sxs-lookup"><span data-stu-id="cf45b-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="cf45b-383">De test CORS avec routage du point de terminaison et [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="cf45b-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="cf45b-384">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf).</span><span class="sxs-lookup"><span data-stu-id="cf45b-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="cf45b-385">Prenons le code suivant qui utilise le [routage de point de terminaison pour activer cors](#ecors):</span><span class="sxs-lookup"><span data-stu-id="cf45b-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="cf45b-386">Les éléments suivants `TodoItems1Controller` fournissent des points de terminaison pour le test :</span><span class="sxs-lookup"><span data-stu-id="cf45b-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="cf45b-387">Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=1) de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé.</span><span class="sxs-lookup"><span data-stu-id="cf45b-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="cf45b-388">Les boutons **supprimer [EnableCors]** et **obtenir [EnableCors]** fonctionnent correctement, car les points de terminaison ont `[EnableCors]` et répondent aux demandes préliminaires.</span><span class="sxs-lookup"><span data-stu-id="cf45b-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="cf45b-389">Les autres points de terminaison échouent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-389">The other endpoints fails.</span></span> <span data-ttu-id="cf45b-390">Le bouton d' **extraction** échoue, car le [code JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :</span><span class="sxs-lookup"><span data-stu-id="cf45b-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="cf45b-391">Les éléments suivants `TodoItems2Controller` fournissent des points de terminaison similaires, mais incluent du code explicite pour répondre aux demandes d’options :</span><span class="sxs-lookup"><span data-stu-id="cf45b-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="cf45b-392">Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=2) de l’exemple déployé.</span><span class="sxs-lookup"><span data-stu-id="cf45b-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="cf45b-393">Dans la liste déroulante **contrôleur** , sélectionnez **prévols** , puis **définir le contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="cf45b-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="cf45b-394">Tous les appels CORS aux points de terminaison ont été `TodoItems2Controller` correctement effectués.</span><span class="sxs-lookup"><span data-stu-id="cf45b-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf45b-395">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cf45b-395">Additional resources</span></span>

* [<span data-ttu-id="cf45b-396">Partage des ressources Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="cf45b-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="cf45b-397">Prise en main du module IIS CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf45b-398">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf45b-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cf45b-399">Cet article explique comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf45b-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="cf45b-400">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="cf45b-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="cf45b-401">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="cf45b-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="cf45b-402">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="cf45b-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="cf45b-403">Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="cf45b-404">Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="cf45b-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="cf45b-405">[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :</span><span class="sxs-lookup"><span data-stu-id="cf45b-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="cf45b-406">Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="cf45b-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="cf45b-407">N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="cf45b-408">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="cf45b-409">Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="cf45b-410">Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="cf45b-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="cf45b-411">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="cf45b-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="cf45b-412">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf45b-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="cf45b-413">Même origine</span><span class="sxs-lookup"><span data-stu-id="cf45b-413">Same origin</span></span>

<span data-ttu-id="cf45b-414">Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="cf45b-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="cf45b-415">Ces deux URL ont le même origine :</span><span class="sxs-lookup"><span data-stu-id="cf45b-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="cf45b-416">Ces URL ont des origines différentes des deux précédentes URL :</span><span class="sxs-lookup"><span data-stu-id="cf45b-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="cf45b-417">`https://example.net`: Domaine différent</span><span class="sxs-lookup"><span data-stu-id="cf45b-417">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="cf45b-418">`https://www.example.com/foo.html`: Autre sous-domaine</span><span class="sxs-lookup"><span data-stu-id="cf45b-418">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="cf45b-419">`http://example.com/foo.html`: Schéma différent</span><span class="sxs-lookup"><span data-stu-id="cf45b-419">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="cf45b-420">`https://example.com:9000/foo.html`: Autre port</span><span class="sxs-lookup"><span data-stu-id="cf45b-420">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="cf45b-421">Internet Explorer ne prend pas en compte le port lors de la comparaison des origines.</span><span class="sxs-lookup"><span data-stu-id="cf45b-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="cf45b-422">CORS avec la stratégie et l’intergiciel (middleware) nommés</span><span class="sxs-lookup"><span data-stu-id="cf45b-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="cf45b-423">L’intergiciel (middleware) CORS gère les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="cf45b-424">Le code suivant active CORS pour l’application entière avec l’origine spécifiée :</span><span class="sxs-lookup"><span data-stu-id="cf45b-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="cf45b-425">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cf45b-425">The preceding code:</span></span>

* <span data-ttu-id="cf45b-426">Définit le nom de la stratégie sur « \_ myAllowSpecificOrigins ».</span><span class="sxs-lookup"><span data-stu-id="cf45b-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="cf45b-427">Le nom de la stratégie est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="cf45b-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="cf45b-428">Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension, qui active cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="cf45b-429">Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cf45b-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cf45b-430">L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet.</span><span class="sxs-lookup"><span data-stu-id="cf45b-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="cf45b-431">Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="cf45b-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="cf45b-432">L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :</span><span class="sxs-lookup"><span data-stu-id="cf45b-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="cf45b-433">Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="cf45b-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="cf45b-434">La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut chaîner des méthodes, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cf45b-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="cf45b-435">Remarque : l’URL **ne doit pas** contenir de barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="cf45b-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="cf45b-436">Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="cf45b-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="cf45b-437">Le code suivant applique des stratégies CORS à tous les points de terminaison des applications par le biais de l’intergiciel (middleware) CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="cf45b-438">Remarque : `UseCors` doit être appelé avant `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="cf45b-439">Consultez [activer cors dans les Razor pages, les contrôleurs et les méthodes d’action](#ecors) pour appliquer la stratégie cors au niveau de la page/du contrôleur/action.</span><span class="sxs-lookup"><span data-stu-id="cf45b-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="cf45b-440">Consultez la page [test cors](#test) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="cf45b-441">Activer CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="cf45b-441">Enable CORS with attributes</span></span>

<span data-ttu-id="cf45b-442">L’attribut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fournit une alternative à l’application de cors globalement.</span><span class="sxs-lookup"><span data-stu-id="cf45b-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="cf45b-443">L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="cf45b-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="cf45b-444">Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` spécifier une stratégie.</span><span class="sxs-lookup"><span data-stu-id="cf45b-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="cf45b-445">L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cf45b-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="cf45b-446">Pagination`PageModel`</span><span class="sxs-lookup"><span data-stu-id="cf45b-446"> Page `PageModel`</span></span>
* <span data-ttu-id="cf45b-447">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="cf45b-447">Controller</span></span>
* <span data-ttu-id="cf45b-448">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="cf45b-448">Controller action method</span></span>

<span data-ttu-id="cf45b-449">Vous pouvez appliquer différentes stratégies à Controller/page-Model/action avec l' `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="cf45b-450">Lorsque l' `[EnableCors]` attribut est appliqué à un modèle de contrôleurs/page/méthode d’action et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="cf45b-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="cf45b-451">Nous vous recommandons de **ne pas** combiner les stratégies.</span><span class="sxs-lookup"><span data-stu-id="cf45b-451">We recommend **not** combining policies.</span></span> <span data-ttu-id="cf45b-452">Utilisez l' `[EnableCors]` attribut ou l’intergiciel (middleware), **pas les deux**.</span><span class="sxs-lookup"><span data-stu-id="cf45b-452">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="cf45b-453">Lorsque vous utilisez `[EnableCors]` , ne définissez **pas** de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf45b-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="cf45b-454">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="cf45b-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="cf45b-455">Le code suivant crée une stratégie CORS par défaut et une stratégie nommée `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="cf45b-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="cf45b-456">Désactiver CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-456">Disable CORS</span></span>

<span data-ttu-id="cf45b-457">L’attribut [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive cors pour le contrôleur/page-Model/action.</span><span class="sxs-lookup"><span data-stu-id="cf45b-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="cf45b-458">Options de stratégie CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-458">CORS policy options</span></span>

<span data-ttu-id="cf45b-459">Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="cf45b-460">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="cf45b-461">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="cf45b-462">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="cf45b-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="cf45b-463">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="cf45b-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="cf45b-464">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="cf45b-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="cf45b-465">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="cf45b-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="cf45b-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cf45b-467">Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="cf45b-468">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-468">Set the allowed origins</span></span>

<span data-ttu-id="cf45b-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="cf45b-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="cf45b-470">`AllowAnyOrigin`n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="cf45b-471">La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites.</span><span class="sxs-lookup"><span data-stu-id="cf45b-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="cf45b-472">Pour une application sécurisée, spécifiez une liste exacte d’origines si le client doit s’autoriser à accéder aux ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="cf45b-473">`AllowAnyOrigin`affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="cf45b-474">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cf45b-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="cf45b-476">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="cf45b-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="cf45b-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="cf45b-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="cf45b-478">Autorise toute méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="cf45b-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="cf45b-479">Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="cf45b-480">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="cf45b-481">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="cf45b-481">Set the allowed request headers</span></span>

<span data-ttu-id="cf45b-482">Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée *créer des en-têtes de demande*, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :</span><span class="sxs-lookup"><span data-stu-id="cf45b-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="cf45b-483">Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="cf45b-484">Ce paramètre affecte les demandes préliminaires et l' `Access-Control-Request-Headers` en-tête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="cf45b-485">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="cf45b-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="cf45b-486">L’intergiciel (middleware) CORS autorise toujours l’envoi de quatre en-têtes dans le `Access-Control-Request-Headers` , quelles que soient les valeurs configurées dans les en-têtes CorsPolicy.</span><span class="sxs-lookup"><span data-stu-id="cf45b-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="cf45b-487">Cette liste d’en-têtes comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cf45b-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="cf45b-488">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="cf45b-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="cf45b-489">L’intergiciel (middleware) CORS répond correctement à une demande préliminaire avec l’en-tête de demande suivant, car est toujours ajouté à la liste `Content-Language` blanche :</span><span class="sxs-lookup"><span data-stu-id="cf45b-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="cf45b-490">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="cf45b-490">Set the exposed response headers</span></span>

<span data-ttu-id="cf45b-491">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="cf45b-492">Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="cf45b-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="cf45b-493">Les en-têtes de réponse qui sont disponibles par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="cf45b-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="cf45b-494">La spécification CORS appelle ces en *-têtes de réponse simples*en-têtes.</span><span class="sxs-lookup"><span data-stu-id="cf45b-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="cf45b-495">Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="cf45b-496">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="cf45b-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="cf45b-497">Les informations d’identification nécessitent un traitement spécial dans une demande CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="cf45b-498">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="cf45b-499">Les informations d’identification incluent les cookies et les schémas d’authentification HTTP.</span><span class="sxs-lookup"><span data-stu-id="cf45b-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="cf45b-500">Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="cf45b-501">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="cf45b-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="cf45b-502">Utilisation de jQuery :</span><span class="sxs-lookup"><span data-stu-id="cf45b-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="cf45b-503">Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="cf45b-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="cf45b-504">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="cf45b-504">The server must allow the credentials.</span></span> <span data-ttu-id="cf45b-505">Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="cf45b-506">La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="cf45b-507">Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="cf45b-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="cf45b-508">L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="cf45b-509">Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="cf45b-510">La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="cf45b-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="cf45b-511">Demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="cf45b-511">Preflight requests</span></span>

<span data-ttu-id="cf45b-512">Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant d’effectuer la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="cf45b-513">Cette demande porte le nom de *demande préliminaire*.</span><span class="sxs-lookup"><span data-stu-id="cf45b-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="cf45b-514">Le navigateur peut ignorer la demande préliminaire si les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="cf45b-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="cf45b-515">La méthode de demande est : obtenir, début ou publication.</span><span class="sxs-lookup"><span data-stu-id="cf45b-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="cf45b-516">L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="cf45b-517">L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="cf45b-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="cf45b-518">La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet.</span><span class="sxs-lookup"><span data-stu-id="cf45b-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="cf45b-519">La spécification CORS appelle ces en-têtes *créer des en-* têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="cf45b-520">La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="cf45b-521">Voici un exemple de demande préliminaire :</span><span class="sxs-lookup"><span data-stu-id="cf45b-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="cf45b-522">La requête de pré-vol utilise la méthode HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="cf45b-523">Il comprend deux en-têtes spéciaux :</span><span class="sxs-lookup"><span data-stu-id="cf45b-523">It includes two special headers:</span></span>

* <span data-ttu-id="cf45b-524">`Access-Control-Request-Method`: Méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="cf45b-525">`Access-Control-Request-Headers`: Liste des en-têtes de requête que l’application définit sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="cf45b-526">Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="cf45b-527">Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes préliminaires CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="cf45b-528">Consultez l' [attribut [HttpOptions] pour les demandes préliminaires](#pro).</span><span class="sxs-lookup"><span data-stu-id="cf45b-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="cf45b-529">Une demande préliminaire CORS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes envoyés avec la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="cf45b-530">Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="cf45b-531">Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="cf45b-532">Les navigateurs ne sont pas entièrement cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="cf45b-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="cf45b-533">Si vous définissez des en-têtes autres que `"*"` (ou utilisez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), vous devez inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="cf45b-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="cf45b-534">Voici un exemple de réponse à la demande préliminaire (en supposant que le serveur autorise la demande) :</span><span class="sxs-lookup"><span data-stu-id="cf45b-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="cf45b-535">La réponse comprend un `Access-Control-Allow-Methods` en-tête qui répertorie les méthodes autorisées et éventuellement un `Access-Control-Allow-Headers` en-tête, qui répertorie les en-têtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="cf45b-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="cf45b-536">Si la demande préliminaire est réussie, le navigateur envoie la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="cf45b-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="cf45b-537">Si la demande préliminaire est refusée, l’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="cf45b-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="cf45b-538">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="cf45b-539">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="cf45b-539">Set the preflight expiration time</span></span>

<span data-ttu-id="cf45b-540">L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="cf45b-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="cf45b-541">Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="cf45b-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="cf45b-542">Fonctionnement de CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-542">How CORS works</span></span>

<span data-ttu-id="cf45b-543">Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.</span><span class="sxs-lookup"><span data-stu-id="cf45b-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="cf45b-544">CORS n’est **pas** une fonctionnalité de sécurité.</span><span class="sxs-lookup"><span data-stu-id="cf45b-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="cf45b-545">CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="cf45b-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="cf45b-546">Par exemple, un acteur malveillant peut utiliser l’option [empêcher les scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="cf45b-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="cf45b-547">Votre API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="cf45b-548">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="cf45b-549">Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="cf45b-550">Par exemple, l’un des outils suivants affiche la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="cf45b-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="cf45b-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="cf45b-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="cf45b-552">Postman</span><span class="sxs-lookup"><span data-stu-id="cf45b-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="cf45b-553">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="cf45b-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="cf45b-554">Un navigateur Web en entrant l’URL dans la barre d’adresses.</span><span class="sxs-lookup"><span data-stu-id="cf45b-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="cf45b-555">C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.</span><span class="sxs-lookup"><span data-stu-id="cf45b-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="cf45b-556">Les navigateurs (sans CORS) ne peuvent pas effectuer de demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="cf45b-557">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="cf45b-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="cf45b-558">JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="cf45b-559">Les scripts sont autorisés à être chargés sur plusieurs origines.</span><span class="sxs-lookup"><span data-stu-id="cf45b-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="cf45b-560">La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="cf45b-561">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="cf45b-562">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="cf45b-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="cf45b-563">Voici un exemple de demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cf45b-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="cf45b-564">L' `Origin` en-tête fournit le domaine du site qui effectue la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="cf45b-565">L' `Origin` en-tête est obligatoire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cf45b-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="cf45b-566">Si le serveur autorise la demande, il définit l' `Access-Control-Allow-Origin` en-tête dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="cf45b-567">La valeur de cet en-tête correspond à l' `Origin` en-tête de la demande ou à la valeur du caractère générique `"*"` , ce qui signifie que toute origine est autorisée :</span><span class="sxs-lookup"><span data-stu-id="cf45b-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="cf45b-568">Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="cf45b-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="cf45b-569">Plus précisément, le navigateur n’autorise pas la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="cf45b-570">Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="cf45b-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="cf45b-571">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-571">Test CORS</span></span>

<span data-ttu-id="cf45b-572">Pour tester CORS :</span><span class="sxs-lookup"><span data-stu-id="cf45b-572">To test CORS:</span></span>

1. <span data-ttu-id="cf45b-573">[Créez un projet d’API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="cf45b-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="cf45b-574">Vous pouvez également [Télécharger l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="cf45b-575">Activez CORS à l’aide de l’une des approches décrites dans ce document.</span><span class="sxs-lookup"><span data-stu-id="cf45b-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="cf45b-576">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cf45b-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="cf45b-577">`WithOrigins("https://localhost:<port>");`doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="cf45b-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="cf45b-578">Créez un projet d’application Web ( Razor pages ou Mvc).</span><span class="sxs-lookup"><span data-stu-id="cf45b-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="cf45b-579">L’exemple utilise des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="cf45b-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="cf45b-580">Vous pouvez créer l’application Web dans la même solution que le projet d’API.</span><span class="sxs-lookup"><span data-stu-id="cf45b-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="cf45b-581">Ajoutez le code en surbrillance suivant au fichier *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cf45b-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="cf45b-582">Dans le code précédent, remplacez `url: 'https://<web app>.azurewebsites.net/api/values/1',` par l’URL de l’application déployée.</span><span class="sxs-lookup"><span data-stu-id="cf45b-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="cf45b-583">Déployez le projet API.</span><span class="sxs-lookup"><span data-stu-id="cf45b-583">Deploy the API project.</span></span> <span data-ttu-id="cf45b-584">Par exemple, [déployez sur Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="cf45b-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="cf45b-585">Exécutez les Razor pages ou l’application MVC à partir du bureau, puis cliquez sur le bouton **test** .</span><span class="sxs-lookup"><span data-stu-id="cf45b-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="cf45b-586">Utilisez les outils F12 pour passer en revue les messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="cf45b-587">Supprimez l’origine localhost de `WithOrigins` et déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="cf45b-588">Vous pouvez également exécuter l’application cliente avec un autre port.</span><span class="sxs-lookup"><span data-stu-id="cf45b-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="cf45b-589">Par exemple, exécutez à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf45b-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="cf45b-590">Testez avec l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="cf45b-590">Test with the client app.</span></span> <span data-ttu-id="cf45b-591">Les échecs CORS retournent une erreur, mais le message d’erreur n’est pas disponible pour JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cf45b-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="cf45b-592">Utilisez l’onglet Console des outils F12 pour afficher l’erreur.</span><span class="sxs-lookup"><span data-stu-id="cf45b-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="cf45b-593">En fonction du navigateur, vous recevez une erreur (dans la console outils F12) semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cf45b-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="cf45b-594">Utilisation de Microsoft Edge :</span><span class="sxs-lookup"><span data-stu-id="cf45b-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="cf45b-595">**SEC7120 : [CORS] l’origine `https://localhost:44375` n’a pas été trouvée `https://localhost:44375` dans l’en-tête de réponse Access-Control-allow-Origin pour la ressource Cross-Origin à l’adresse`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="cf45b-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="cf45b-596">Utilisation de chrome :</span><span class="sxs-lookup"><span data-stu-id="cf45b-596">Using Chrome:</span></span>

     <span data-ttu-id="cf45b-597">**L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.**</span><span class="sxs-lookup"><span data-stu-id="cf45b-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="cf45b-598">Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil tel que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="cf45b-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="cf45b-599">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="cf45b-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="cf45b-600">Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="cf45b-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="cf45b-601">Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.</span><span class="sxs-lookup"><span data-stu-id="cf45b-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="cf45b-602">Les en-têtes CORS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cf45b-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="cf45b-603">CORS dans IIS</span><span class="sxs-lookup"><span data-stu-id="cf45b-603">CORS in IIS</span></span>

<span data-ttu-id="cf45b-604">Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="cf45b-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="cf45b-605">Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cf45b-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf45b-606">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cf45b-606">Additional resources</span></span>

* [<span data-ttu-id="cf45b-607">Partage des ressources Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="cf45b-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="cf45b-608">Prise en main du module IIS CORS</span><span class="sxs-lookup"><span data-stu-id="cf45b-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
