---
title: Mettre en forme les données des réponses dans l’API web ASP.NET Core
author: ardalis
description: Découvrez comment mettre en forme les données des réponses dans l’API web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 22787b20879c3739ee8a8d74c7a39e7cf8f4d5b0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774234"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="f77f6-103">Mettre en forme les données des réponses dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77f6-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="f77f6-104">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f77f6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f77f6-105">ASP.NET Core MVC prend en charge la mise en forme des données de réponse.</span><span class="sxs-lookup"><span data-stu-id="f77f6-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="f77f6-106">Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.</span><span class="sxs-lookup"><span data-stu-id="f77f6-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="f77f6-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f77f6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="f77f6-108">Résultats des actions spécifiques au format</span><span class="sxs-lookup"><span data-stu-id="f77f6-108">Format-specific Action Results</span></span>

<span data-ttu-id="f77f6-109">Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="f77f6-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="f77f6-110">Les actions peuvent retourner des résultats mis en forme dans un format particulier, indépendamment des préférences du client.</span><span class="sxs-lookup"><span data-stu-id="f77f6-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="f77f6-111">Par exemple, le `JsonResult` retour de retourne des données au format JSON.</span><span class="sxs-lookup"><span data-stu-id="f77f6-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="f77f6-112">Le `ContentResult` retour de ou une chaîne retourne des données de chaîne au format texte brut.</span><span class="sxs-lookup"><span data-stu-id="f77f6-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="f77f6-113">Une action n’est pas requise pour retourner un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="f77f6-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="f77f6-114">ASP.NET Core prend en charge toute valeur de retour d’objet.</span><span class="sxs-lookup"><span data-stu-id="f77f6-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="f77f6-115">Les résultats des actions qui retournent des <xref:Microsoft.AspNetCore.Mvc.IActionResult> objets qui ne sont pas des types <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> sont sérialisés à l’aide de l’implémentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="f77f6-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="f77f6-116">Pour plus d’informations, consultez <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="f77f6-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="f77f6-117">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> d’assistance intégrée retourne des données au format JSON :[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="f77f6-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="f77f6-118">L’exemple de téléchargement retourne la liste des auteurs.</span><span class="sxs-lookup"><span data-stu-id="f77f6-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="f77f6-119">À l’aide des outils de développement du navigateur F12 ou du [poster](https://www.getpostman.com/tools) avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="f77f6-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="f77f6-120">L’en-tête de réponse contenant **Content-type :** `application/json; charset=utf-8` est affiché.</span><span class="sxs-lookup"><span data-stu-id="f77f6-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="f77f6-121">Les en-têtes de demande s’affichent.</span><span class="sxs-lookup"><span data-stu-id="f77f6-121">The request headers are displayed.</span></span> <span data-ttu-id="f77f6-122">Par exemple, l' `Accept` en-tête.</span><span class="sxs-lookup"><span data-stu-id="f77f6-122">For example, the `Accept` header.</span></span> <span data-ttu-id="f77f6-123">L' `Accept` en-tête est ignoré par le code précédent.</span><span class="sxs-lookup"><span data-stu-id="f77f6-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="f77f6-124">Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> et le helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> :</span><span class="sxs-lookup"><span data-stu-id="f77f6-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="f77f6-125">Dans le code précédent, le `Content-Type` retourné est `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="f77f6-126">Retour `Content-Type` d' `text/plain`une chaîne :</span><span class="sxs-lookup"><span data-stu-id="f77f6-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="f77f6-127">Pour les actions avec plusieurs types de retour `IActionResult`, retournez.</span><span class="sxs-lookup"><span data-stu-id="f77f6-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="f77f6-128">Par exemple, le retour de codes d’état HTTP différents en fonction du résultat des opérations effectuées.</span><span class="sxs-lookup"><span data-stu-id="f77f6-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="f77f6-129">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="f77f6-129">Content negotiation</span></span>

<span data-ttu-id="f77f6-130">La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="f77f6-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="f77f6-131">Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="f77f6-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="f77f6-132">La négociation de contenu est :</span><span class="sxs-lookup"><span data-stu-id="f77f6-132">Content negotiation is:</span></span>

* <span data-ttu-id="f77f6-133">Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="f77f6-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="f77f6-134">Intégré aux résultats d’action spécifiques au code d’État retournés par les méthodes d’assistance.</span><span class="sxs-lookup"><span data-stu-id="f77f6-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="f77f6-135">Les méthodes d’assistance des résultats d’action sont `ObjectResult`basées sur.</span><span class="sxs-lookup"><span data-stu-id="f77f6-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="f77f6-136">Quand un type de modèle est retourné, le type de `ObjectResult`retour est.</span><span class="sxs-lookup"><span data-stu-id="f77f6-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="f77f6-137">La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="f77f6-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="f77f6-138">Par défaut, ASP.net Core prend `application/json`en `text/json`charge les `text/plain` types de média, et.</span><span class="sxs-lookup"><span data-stu-id="f77f6-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="f77f6-139">Les outils tels que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/tools) peuvent définir l' `Accept` en-tête de demande pour spécifier le format de retour.</span><span class="sxs-lookup"><span data-stu-id="f77f6-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="f77f6-140">Lorsque l' `Accept` en-tête contient un type pris en charge par le serveur, ce type est retourné.</span><span class="sxs-lookup"><span data-stu-id="f77f6-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="f77f6-141">La section suivante montre comment ajouter des formateurs supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="f77f6-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="f77f6-142">Les actions de contrôleur peuvent retourner des POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="f77f6-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="f77f6-143">Lorsqu’un POCO est retourné, le runtime crée automatiquement un `ObjectResult` objet qui encapsule l’objet.</span><span class="sxs-lookup"><span data-stu-id="f77f6-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="f77f6-144">Le client obtient l’objet sérialisé mis en forme.</span><span class="sxs-lookup"><span data-stu-id="f77f6-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="f77f6-145">Si l’objet retourné est `null`, une `204 No Content` réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="f77f6-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="f77f6-146">Retour d’un type d’objet :</span><span class="sxs-lookup"><span data-stu-id="f77f6-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="f77f6-147">Dans le code précédent, une demande d’alias d’auteur valide retourne une `200 OK` réponse avec les données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="f77f6-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="f77f6-148">Une demande d’alias non valide retourne une `204 No Content` réponse.</span><span class="sxs-lookup"><span data-stu-id="f77f6-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="f77f6-149">En-tête Accept</span><span class="sxs-lookup"><span data-stu-id="f77f6-149">The Accept header</span></span>

<span data-ttu-id="f77f6-150">La *négociation* de contenu a lieu `Accept` lorsqu’un en-tête apparaît dans la demande.</span><span class="sxs-lookup"><span data-stu-id="f77f6-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="f77f6-151">Quand une demande contient un en-tête Accept, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f77f6-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="f77f6-152">Énumère les types de médias dans l’en-tête Accept dans l’ordre de préférence.</span><span class="sxs-lookup"><span data-stu-id="f77f6-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="f77f6-153">Tente de trouver un formateur capable de produire une réponse dans l’un des formats spécifiés.</span><span class="sxs-lookup"><span data-stu-id="f77f6-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="f77f6-154">Si aucun formateur trouvé pouvant satisfaire la demande du client, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f77f6-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="f77f6-155">Retourne `406 Not Acceptable` si <xref:Microsoft.AspNetCore.Mvc.MvcOptions> a été défini, ou-</span><span class="sxs-lookup"><span data-stu-id="f77f6-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="f77f6-156">Tente de trouver le premier formateur qui peut produire une réponse.</span><span class="sxs-lookup"><span data-stu-id="f77f6-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="f77f6-157">Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut mettre en forme l’objet est utilisé.</span><span class="sxs-lookup"><span data-stu-id="f77f6-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="f77f6-158">Si aucun `Accept` en-tête n’apparaît dans la requête :</span><span class="sxs-lookup"><span data-stu-id="f77f6-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="f77f6-159">Le premier formateur qui peut gérer l’objet est utilisé pour sérialiser la réponse.</span><span class="sxs-lookup"><span data-stu-id="f77f6-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="f77f6-160">Aucune négociation n’a lieu.</span><span class="sxs-lookup"><span data-stu-id="f77f6-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="f77f6-161">Le serveur détermine le format à retourner.</span><span class="sxs-lookup"><span data-stu-id="f77f6-161">The server is determining what format to return.</span></span>

<span data-ttu-id="f77f6-162">Si l’en-tête `*/*`Accept contient, l’en- `RespectBrowserAcceptHeader` tête est ignoré, sauf <xref:Microsoft.AspNetCore.Mvc.MvcOptions>si a la valeur true on.</span><span class="sxs-lookup"><span data-stu-id="f77f6-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="f77f6-163">Navigateurs et négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="f77f6-163">Browsers and content negotiation</span></span>

<span data-ttu-id="f77f6-164">Contrairement aux clients d’API typiques, les navigateurs Web fournissent `Accept` des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="f77f6-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="f77f6-165">Le navigateur Web spécifie de nombreux formats, y compris des caractères génériques.</span><span class="sxs-lookup"><span data-stu-id="f77f6-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="f77f6-166">Par défaut, lorsque le Framework détecte que la demande provient d’un navigateur :</span><span class="sxs-lookup"><span data-stu-id="f77f6-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="f77f6-167">L' `Accept` en-tête est ignoré.</span><span class="sxs-lookup"><span data-stu-id="f77f6-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="f77f6-168">Le contenu est renvoyé au format JSON, sauf s’il a été configuré autrement.</span><span class="sxs-lookup"><span data-stu-id="f77f6-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="f77f6-169">Cela offre une expérience plus cohérente entre les navigateurs lors de l’utilisation des API.</span><span class="sxs-lookup"><span data-stu-id="f77f6-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="f77f6-170">Pour configurer une application pour honorer les en-têtes Accept <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> du `true`navigateur, définissez sur :</span><span class="sxs-lookup"><span data-stu-id="f77f6-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="f77f6-171">Configurer les formateurs</span><span class="sxs-lookup"><span data-stu-id="f77f6-171">Configure formatters</span></span>

<span data-ttu-id="f77f6-172">Les applications qui doivent prendre en charge des formats supplémentaires peuvent ajouter les packages NuGet appropriés et configurer la prise en charge.</span><span class="sxs-lookup"><span data-stu-id="f77f6-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="f77f6-173">Il existe des formateurs distincts pour les entrées et pour les sorties.</span><span class="sxs-lookup"><span data-stu-id="f77f6-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="f77f6-174">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="f77f6-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="f77f6-175">Les formateurs de sortie sont utilisés pour mettre en forme les réponses.</span><span class="sxs-lookup"><span data-stu-id="f77f6-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="f77f6-176">Pour plus d’informations sur la création d’un formateur personnalisé, consultez [formateurs personnalisés](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="f77f6-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="f77f6-177">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="f77f6-177">Add XML format support</span></span>

<span data-ttu-id="f77f6-178">Les formateurs XML implémentés à l' <xref:System.Xml.Serialization.XmlSerializer> aide <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>de sont configurés en appelant :</span><span class="sxs-lookup"><span data-stu-id="f77f6-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="f77f6-179">Le code précédent sérialise les résultats à `XmlSerializer`l’aide de.</span><span class="sxs-lookup"><span data-stu-id="f77f6-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="f77f6-180">Lorsque vous utilisez le code précédent, les méthodes de contrôleur retournent le format approprié `Accept` en fonction de l’en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="f77f6-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="f77f6-181">Configurer des formateurs basés sur System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="f77f6-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="f77f6-182">Les fonctionnalités pour les formateurs basés sur `System.Text.Json` peuvent être configurées avec `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="f77f6-183">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide `JsonResult`de.</span><span class="sxs-lookup"><span data-stu-id="f77f6-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="f77f6-184">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f77f6-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="f77f6-185">Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="f77f6-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="f77f6-186">Avant ASP.NET Core 3,0, les formateurs JSON utilisés par défaut ont été implémentés à l’aide du `Newtonsoft.Json` package.</span><span class="sxs-lookup"><span data-stu-id="f77f6-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="f77f6-187">Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="f77f6-188">La prise `Newtonsoft.Json` en charge des formateurs et des fonctionnalités de base est disponible en installant le package NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) et en le configurant dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="f77f6-189">Certaines fonctionnalités peuvent ne pas fonctionner correctement `System.Text.Json`avec les `Newtonsoft.Json`formateurs basés sur et nécessitent une référence aux formateurs basés sur.</span><span class="sxs-lookup"><span data-stu-id="f77f6-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="f77f6-190">Continuer à utiliser `Newtonsoft.Json`les formateurs basés sur, si l’application :</span><span class="sxs-lookup"><span data-stu-id="f77f6-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="f77f6-191">Utilise `Newtonsoft.Json` des attributs.</span><span class="sxs-lookup"><span data-stu-id="f77f6-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="f77f6-192">Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="f77f6-193">Personnalise les paramètres de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="f77f6-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="f77f6-194">S’appuie sur les fonctionnalités `Newtonsoft.Json` fournies par.</span><span class="sxs-lookup"><span data-stu-id="f77f6-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="f77f6-195">Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="f77f6-196">Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="f77f6-197">Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="f77f6-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="f77f6-198">Les `Newtonsoft.Json`fonctionnalités des formateurs basés sur peuvent être configurées `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="f77f6-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="f77f6-199">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide `JsonResult`de.</span><span class="sxs-lookup"><span data-stu-id="f77f6-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="f77f6-200">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f77f6-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="f77f6-201">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="f77f6-201">Add XML format support</span></span>

<span data-ttu-id="f77f6-202">La mise en forme XML requiert le package NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="f77f6-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="f77f6-203">Les formateurs XML implémentés à l' <xref:System.Xml.Serialization.XmlSerializer> aide <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>de sont configurés en appelant :</span><span class="sxs-lookup"><span data-stu-id="f77f6-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="f77f6-204">Le code précédent sérialise les résultats à `XmlSerializer`l’aide de.</span><span class="sxs-lookup"><span data-stu-id="f77f6-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="f77f6-205">Lorsque vous utilisez le code précédent, les méthodes de contrôleur doivent retourner le format approprié en fonction `Accept` de l’en-tête de la requête.</span><span class="sxs-lookup"><span data-stu-id="f77f6-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="f77f6-206">Spécifier un format</span><span class="sxs-lookup"><span data-stu-id="f77f6-206">Specify a format</span></span>

<span data-ttu-id="f77f6-207">Pour restreindre les formats de réponse, appliquez [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) le filtre.</span><span class="sxs-lookup"><span data-stu-id="f77f6-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="f77f6-208">Comme la [Filters](xref:mvc/controllers/filters)plupart des `[Produces]` filtres, peut être appliqué au niveau de l’action, du contrôleur ou de l’étendue globale :</span><span class="sxs-lookup"><span data-stu-id="f77f6-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="f77f6-209">Le filtre [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) précédent :</span><span class="sxs-lookup"><span data-stu-id="f77f6-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="f77f6-210">Force toutes les actions du contrôleur à retourner des réponses au format JSON.</span><span class="sxs-lookup"><span data-stu-id="f77f6-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="f77f6-211">Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.</span><span class="sxs-lookup"><span data-stu-id="f77f6-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="f77f6-212">Pour plus d’informations, consultez [filtres](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="f77f6-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="f77f6-213">Formateurs de cas spéciaux</span><span class="sxs-lookup"><span data-stu-id="f77f6-213">Special case formatters</span></span>

<span data-ttu-id="f77f6-214">Certains cas spéciaux sont implémentés avec des formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="f77f6-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="f77f6-215">Par défaut, `string` les types de retour sont mis en forme en tant que *texte/plain* (*texte/html* si demandé via l' `Accept` en-tête).</span><span class="sxs-lookup"><span data-stu-id="f77f6-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="f77f6-216">Ce comportement peut être supprimé en supprimant <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>le.</span><span class="sxs-lookup"><span data-stu-id="f77f6-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="f77f6-217">Les formateurs sont supprimés de `ConfigureServices` la méthode.</span><span class="sxs-lookup"><span data-stu-id="f77f6-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="f77f6-218">Les actions qui ont un type de retour d' `204 No Content` objet de `null`modèle retournent lors du retour de.</span><span class="sxs-lookup"><span data-stu-id="f77f6-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="f77f6-219">Ce comportement peut être supprimé en supprimant <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>le.</span><span class="sxs-lookup"><span data-stu-id="f77f6-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="f77f6-220">Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="f77f6-221">Sans le `StringOutputFormatter`, le formateur JSON intégré met en forme `string` les types de retour.</span><span class="sxs-lookup"><span data-stu-id="f77f6-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="f77f6-222">Si le formateur JSON intégré est supprimé et qu’un formateur XML est disponible, le formateur XML met en `string` forme les types de retour.</span><span class="sxs-lookup"><span data-stu-id="f77f6-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="f77f6-223">Sinon, `string` les types de `406 Not Acceptable`retour retournent.</span><span class="sxs-lookup"><span data-stu-id="f77f6-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="f77f6-224">Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré.</span><span class="sxs-lookup"><span data-stu-id="f77f6-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="f77f6-225">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f77f6-225">For example:</span></span>

* <span data-ttu-id="f77f6-226">Le formateur JSON retourne une réponse avec un corps de `null`.</span><span class="sxs-lookup"><span data-stu-id="f77f6-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="f77f6-227">Le formateur XML retourne un élément XML vide avec l’ensemble `xsi:nil="true"` d’attributs.</span><span class="sxs-lookup"><span data-stu-id="f77f6-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="f77f6-228">Mappages d’URL de format de réponse</span><span class="sxs-lookup"><span data-stu-id="f77f6-228">Response format URL mappings</span></span>

<span data-ttu-id="f77f6-229">Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :</span><span class="sxs-lookup"><span data-stu-id="f77f6-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="f77f6-230">Dans la chaîne de requête ou dans une partie du chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="f77f6-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="f77f6-231">En utilisant une extension de fichier spécifique au format, par exemple. XML ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="f77f6-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="f77f6-232">Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API.</span><span class="sxs-lookup"><span data-stu-id="f77f6-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="f77f6-233">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f77f6-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="f77f6-234">L’itinéraire précédent permet de spécifier le format demandé en tant qu’extension de fichier facultative.</span><span class="sxs-lookup"><span data-stu-id="f77f6-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="f77f6-235">L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lorsque la réponse est créée.</span><span class="sxs-lookup"><span data-stu-id="f77f6-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="f77f6-236">Routage</span><span class="sxs-lookup"><span data-stu-id="f77f6-236">Route</span></span>        |             <span data-ttu-id="f77f6-237">Formateur</span><span class="sxs-lookup"><span data-stu-id="f77f6-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="f77f6-238">Le formateur de sortie par défaut</span><span class="sxs-lookup"><span data-stu-id="f77f6-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="f77f6-239">Le formateur JSON (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="f77f6-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="f77f6-240">Le formateur XML (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="f77f6-240">The XML formatter (if configured)</span></span>  |
