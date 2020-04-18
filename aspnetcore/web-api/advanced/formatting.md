---
title: Mettre en forme les données des réponses dans l’API web ASP.NET Core
author: ardalis
description: Découvrez comment mettre en forme les données des réponses dans l’API web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642710"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="e1807-103">Mettre en forme les données des réponses dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1807-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="e1807-104">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e1807-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e1807-105">ASP.NET Core MVC a la prise en charge pour le formatage des données de réponse.</span><span class="sxs-lookup"><span data-stu-id="e1807-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="e1807-106">Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.</span><span class="sxs-lookup"><span data-stu-id="e1807-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="e1807-107">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1807-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="e1807-108">Résultats d’action spécifiques au format</span><span class="sxs-lookup"><span data-stu-id="e1807-108">Format-specific Action Results</span></span>

<span data-ttu-id="e1807-109">Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="e1807-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="e1807-110">Les actions peuvent retourner les résultats qui sont formatés dans un format particulier, indépendamment des préférences des clients.</span><span class="sxs-lookup"><span data-stu-id="e1807-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="e1807-111">Par exemple, `JsonResult` le retour renvoie les données formatées par JSON.</span><span class="sxs-lookup"><span data-stu-id="e1807-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="e1807-112">Le `ContentResult` retour ou une chaîne renvoie des données de chaîne formatées de texte simple.</span><span class="sxs-lookup"><span data-stu-id="e1807-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="e1807-113">Une action n’est pas nécessaire pour retourner un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="e1807-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="e1807-114">ASP.NET Core prend en charge toute valeur de retour d’objet.</span><span class="sxs-lookup"><span data-stu-id="e1807-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="e1807-115">Les résultats d’actions qui <xref:Microsoft.AspNetCore.Mvc.IActionResult> renvoient des objets <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> qui ne sont pas des types sont sérialisés à l’aide de la mise en œuvre appropriée.</span><span class="sxs-lookup"><span data-stu-id="e1807-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="e1807-116">Pour plus d’informations, consultez <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="e1807-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="e1807-117">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> d’assistance intégrée renvoie les données formatées par JSON :[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="e1807-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="e1807-118">Le téléchargement de l’échantillon renvoie la liste des auteurs.</span><span class="sxs-lookup"><span data-stu-id="e1807-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="e1807-119">Utilisation des outils de développeur de navigateur F12 ou [Postman](https://www.getpostman.com/tools) avec le code précédent:</span><span class="sxs-lookup"><span data-stu-id="e1807-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="e1807-120">L’en-tête de réponse contenant **du type de contenu est** `application/json; charset=utf-8` affiché.</span><span class="sxs-lookup"><span data-stu-id="e1807-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="e1807-121">Les en-têtes de demande sont affichés.</span><span class="sxs-lookup"><span data-stu-id="e1807-121">The request headers are displayed.</span></span> <span data-ttu-id="e1807-122">Par exemple, `Accept` l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="e1807-122">For example, the `Accept` header.</span></span> <span data-ttu-id="e1807-123">L’en-tête `Accept` est ignoré par le code précédent.</span><span class="sxs-lookup"><span data-stu-id="e1807-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="e1807-124">Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> et le helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> :</span><span class="sxs-lookup"><span data-stu-id="e1807-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="e1807-125">Dans le code `Content-Type` précédent, `text/plain`le retour est .</span><span class="sxs-lookup"><span data-stu-id="e1807-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="e1807-126">Le retour `Content-Type` d’une chaîne délivre : `text/plain`</span><span class="sxs-lookup"><span data-stu-id="e1807-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="e1807-127">Pour les actions avec `IActionResult`plusieurs types de retour, retour .</span><span class="sxs-lookup"><span data-stu-id="e1807-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="e1807-128">Par exemple, le retour de différents codes d’état HTTP en fonction du résultat des opérations effectuées.</span><span class="sxs-lookup"><span data-stu-id="e1807-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="e1807-129">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="e1807-129">Content negotiation</span></span>

<span data-ttu-id="e1807-130">La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="e1807-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="e1807-131">Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="e1807-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="e1807-132">La négociation de contenu est la suivante :</span><span class="sxs-lookup"><span data-stu-id="e1807-132">Content negotiation is:</span></span>

* <span data-ttu-id="e1807-133">Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="e1807-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="e1807-134">Intégré dans les résultats d’action spécifiques au code d’état retournés des méthodes d’aide.</span><span class="sxs-lookup"><span data-stu-id="e1807-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="e1807-135">Les méthodes d’aide aux `ObjectResult`résultats d’action sont basées sur .</span><span class="sxs-lookup"><span data-stu-id="e1807-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="e1807-136">Quand un type de modèle est `ObjectResult`retourné, le type de retour est .</span><span class="sxs-lookup"><span data-stu-id="e1807-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="e1807-137">La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="e1807-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="e1807-138">Par défaut, ASP.NET Core `application/json`prend `text/json`en `text/plain` charge, et les types de médias.</span><span class="sxs-lookup"><span data-stu-id="e1807-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="e1807-139">Des outils tels que [Fiddler](https://www.telerik.com/fiddler) `Accept` ou [Postman](https://www.getpostman.com/tools) peuvent définir l’en-tête de la demande pour spécifier le format de retour.</span><span class="sxs-lookup"><span data-stu-id="e1807-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="e1807-140">Lorsque `Accept` l’en-tête contient un type que le serveur prend en charge, ce type est retourné.</span><span class="sxs-lookup"><span data-stu-id="e1807-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="e1807-141">La section suivante montre comment ajouter des formateurs supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e1807-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="e1807-142">Les actions de contrôleur peuvent retourner les POCO (objets CLR de plaine ancienne).</span><span class="sxs-lookup"><span data-stu-id="e1807-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="e1807-143">Lorsqu’un POCO est retourné, le `ObjectResult` temps d’exécution crée automatiquement un qui enveloppe l’objet.</span><span class="sxs-lookup"><span data-stu-id="e1807-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="e1807-144">Le client reçoit l’objet sérialisé formaté.</span><span class="sxs-lookup"><span data-stu-id="e1807-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="e1807-145">Si l’objet `null`retourné `204 No Content` est, une réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="e1807-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="e1807-146">Retour d’un type d’objet :</span><span class="sxs-lookup"><span data-stu-id="e1807-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="e1807-147">Dans le code précédent, une demande d’un pseudonyme d’auteur valide renvoie une `200 OK` réponse avec les données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="e1807-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="e1807-148">Une demande d’alias `204 No Content` invalide renvoie une réponse.</span><span class="sxs-lookup"><span data-stu-id="e1807-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="e1807-149">L’en-tête Accept</span><span class="sxs-lookup"><span data-stu-id="e1807-149">The Accept header</span></span>

<span data-ttu-id="e1807-150">La *négociation* de `Accept` contenu a lieu lorsqu’un en-tête apparaît dans la demande.</span><span class="sxs-lookup"><span data-stu-id="e1807-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="e1807-151">Lorsqu’une demande contient un en-tête d’acceptation, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="e1807-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="e1807-152">Énumère les types de médias dans l’en-tête d’acceptation dans l’ordre de préférence.</span><span class="sxs-lookup"><span data-stu-id="e1807-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="e1807-153">Essaye de trouver un formateur qui peut produire une réponse dans l’un des formats spécifiés.</span><span class="sxs-lookup"><span data-stu-id="e1807-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="e1807-154">Si aucun formateur n’est trouvé qui peut satisfaire à la demande du client, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="e1807-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="e1807-155">`406 Not Acceptable` Retours <xref:Microsoft.AspNetCore.Mvc.MvcOptions> si a été défini, ou -</span><span class="sxs-lookup"><span data-stu-id="e1807-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="e1807-156">Essaye de trouver le premier formateur qui peut produire une réponse.</span><span class="sxs-lookup"><span data-stu-id="e1807-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="e1807-157">Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut formater l’objet est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e1807-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="e1807-158">Si `Accept` aucun en-tête n’apparaît dans la demande :</span><span class="sxs-lookup"><span data-stu-id="e1807-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="e1807-159">Le premier formateur qui peut manipuler l’objet est utilisé pour sérialiser la réponse.</span><span class="sxs-lookup"><span data-stu-id="e1807-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="e1807-160">Il n’y a pas de négociation en cours.</span><span class="sxs-lookup"><span data-stu-id="e1807-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="e1807-161">Le serveur détermine le format à retourner.</span><span class="sxs-lookup"><span data-stu-id="e1807-161">The server is determining what format to return.</span></span>

<span data-ttu-id="e1807-162">Si l’en-tête Accept contient, `*/*`l’en-tête est ignoré à moins d’être `RespectBrowserAcceptHeader` concrétisée sur <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="e1807-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="e1807-163">Navigateurs et négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="e1807-163">Browsers and content negotiation</span></span>

<span data-ttu-id="e1807-164">Contrairement aux clients API typiques, les navigateurs Web fournissent des `Accept` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="e1807-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="e1807-165">Navigateur Web spécifient de nombreux formats, y compris wildcards.</span><span class="sxs-lookup"><span data-stu-id="e1807-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="e1807-166">Par défaut, lorsque le cadre détecte que la demande provient d’un navigateur :</span><span class="sxs-lookup"><span data-stu-id="e1807-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="e1807-167">L’en-tête `Accept` est ignoré.</span><span class="sxs-lookup"><span data-stu-id="e1807-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="e1807-168">Le contenu est retourné dans JSON, sauf configuré autrement.</span><span class="sxs-lookup"><span data-stu-id="e1807-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="e1807-169">Cela offre une expérience plus cohérente à travers les navigateurs lors de la consommation d’API.</span><span class="sxs-lookup"><span data-stu-id="e1807-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="e1807-170">Pour configurer une application pour honorer <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`les en-têtes du navigateur, définissez :</span><span class="sxs-lookup"><span data-stu-id="e1807-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="e1807-171">Configurer les formateurs</span><span class="sxs-lookup"><span data-stu-id="e1807-171">Configure formatters</span></span>

<span data-ttu-id="e1807-172">Les applications qui ont besoin de prendre en charge des formats supplémentaires peuvent ajouter les paquets NuGet appropriés et configurer le support.</span><span class="sxs-lookup"><span data-stu-id="e1807-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="e1807-173">Il existe des formateurs distincts pour les entrées et pour les sorties.</span><span class="sxs-lookup"><span data-stu-id="e1807-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="e1807-174">Les formateurs d’entrée sont utilisés par [Model Binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="e1807-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e1807-175">Les formateurs de sortie sont utilisés pour formater les réponses.</span><span class="sxs-lookup"><span data-stu-id="e1807-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="e1807-176">Pour plus d’informations sur la création d’un formateur personnalisé, voir [Custom Formatters](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="e1807-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="e1807-177">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="e1807-177">Add XML format support</span></span>

<span data-ttu-id="e1807-178">Les formateurs XML mis en œuvre à l’aide <xref:System.Xml.Serialization.XmlSerializer> sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="e1807-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="e1807-179">Le code précédent sérialis les résultats à l’aide `XmlSerializer`de .</span><span class="sxs-lookup"><span data-stu-id="e1807-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="e1807-180">Lors de l’utilisation du code précédent, les méthodes `Accept` de contrôleur renvoient le format approprié en fonction de l’en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="e1807-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="e1807-181">Configurer des formateurs basés sur System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="e1807-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="e1807-182">Les fonctionnalités pour les formateurs basés sur `System.Text.Json` peuvent être configurées avec `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="e1807-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="e1807-183">Les options de sérialisation des sorties, par `JsonResult`action, peuvent être configurées à l’aide de .</span><span class="sxs-lookup"><span data-stu-id="e1807-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="e1807-184">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e1807-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="e1807-185">Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="e1807-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="e1807-186">Avant ASP.NET Core 3.0, les formateurs JSON utilisés `Newtonsoft.Json` par défaut ont été mis en œuvre à l’aide du paquet.</span><span class="sxs-lookup"><span data-stu-id="e1807-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="e1807-187">Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="e1807-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="e1807-188">La `Newtonsoft.Json` prise en charge des formateurs et fonctionnalités basés est disponible en installant le paquet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet et en le configurant dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e1807-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="e1807-189">Certaines fonctionnalités peuvent `System.Text.Json`ne pas bien fonctionner avec `Newtonsoft.Json`les formateurs basés et nécessitent une référence aux formateurs basés.</span><span class="sxs-lookup"><span data-stu-id="e1807-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="e1807-190">Continuer à `Newtonsoft.Json`utiliser les formateurs basés si l’application :</span><span class="sxs-lookup"><span data-stu-id="e1807-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="e1807-191">Utilise `Newtonsoft.Json` des attributs.</span><span class="sxs-lookup"><span data-stu-id="e1807-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="e1807-192">Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="e1807-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="e1807-193">Personnalise les paramètres de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="e1807-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="e1807-194">S’appuie `Newtonsoft.Json` sur les fonctionnalités qui fournissent.</span><span class="sxs-lookup"><span data-stu-id="e1807-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="e1807-195">Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="e1807-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="e1807-196">Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="e1807-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="e1807-197">Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="e1807-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="e1807-198">Les fonctionnalités des formateurs basés peuvent être configurées à l’aide `Newtonsoft.Json`de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="e1807-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="e1807-199">Les options de sérialisation des sorties, par `JsonResult`action, peuvent être configurées à l’aide de .</span><span class="sxs-lookup"><span data-stu-id="e1807-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="e1807-200">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e1807-200">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="e1807-201">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="e1807-201">Add XML format support</span></span>

<span data-ttu-id="e1807-202">Le formatage XML nécessite le package [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e1807-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="e1807-203">Les formateurs XML mis en œuvre à l’aide <xref:System.Xml.Serialization.XmlSerializer> sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="e1807-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="e1807-204">Le code précédent sérialis les résultats à l’aide `XmlSerializer`de .</span><span class="sxs-lookup"><span data-stu-id="e1807-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="e1807-205">Lors de l’utilisation du code précédent, les méthodes `Accept` de contrôleur doivent retourner le format approprié en fonction de l’en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="e1807-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="e1807-206">Spécifier un format</span><span class="sxs-lookup"><span data-stu-id="e1807-206">Specify a format</span></span>

<span data-ttu-id="e1807-207">Pour restreindre les formats [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) de réponse, appliquez le filtre.</span><span class="sxs-lookup"><span data-stu-id="e1807-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="e1807-208">Comme la plupart `[Produces]` des [filtres](xref:mvc/controllers/filters), peut être appliqué à l’action, contrôleur, ou portée globale:</span><span class="sxs-lookup"><span data-stu-id="e1807-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="e1807-209">Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :</span><span class="sxs-lookup"><span data-stu-id="e1807-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="e1807-210">Force toutes les actions au sein du contrôleur à retourner les réponses formatées par JSON.</span><span class="sxs-lookup"><span data-stu-id="e1807-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="e1807-211">Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.</span><span class="sxs-lookup"><span data-stu-id="e1807-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="e1807-212">Pour plus d’informations, voir [Filtres](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="e1807-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="e1807-213">Formateurs de cas spéciaux</span><span class="sxs-lookup"><span data-stu-id="e1807-213">Special case formatters</span></span>

<span data-ttu-id="e1807-214">Certains cas spéciaux sont implémentés avec des formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="e1807-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="e1807-215">Par `string` défaut, les types de retour sont formatés comme `Accept` *texte/plaine* *(texte/html* si demandé via l’en-tête).</span><span class="sxs-lookup"><span data-stu-id="e1807-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="e1807-216">Ce comportement peut être supprimé <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>en supprimant le .</span><span class="sxs-lookup"><span data-stu-id="e1807-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="e1807-217">Les formateurs sont `ConfigureServices` supprimés dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="e1807-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="e1807-218">Actions qui ont un modèle `204 No Content` de `null`retour de type d’objet retour retour lors du retour .</span><span class="sxs-lookup"><span data-stu-id="e1807-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="e1807-219">Ce comportement peut être supprimé <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>en supprimant le .</span><span class="sxs-lookup"><span data-stu-id="e1807-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="e1807-220">Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="e1807-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="e1807-221">Sans `StringOutputFormatter`le , le formatter intégré `string` JSON formatter types de retour.</span><span class="sxs-lookup"><span data-stu-id="e1807-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="e1807-222">Si le formateur JSON intégré est supprimé et qu’un formateur XML `string` est disponible, les formateurs XML retournent les types.</span><span class="sxs-lookup"><span data-stu-id="e1807-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="e1807-223">Sinon, `string` les `406 Not Acceptable`types de retour reviennent .</span><span class="sxs-lookup"><span data-stu-id="e1807-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="e1807-224">Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré.</span><span class="sxs-lookup"><span data-stu-id="e1807-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="e1807-225">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e1807-225">For example:</span></span>

* <span data-ttu-id="e1807-226">Le formateur JSON retourne une `null`réponse avec un corps de .</span><span class="sxs-lookup"><span data-stu-id="e1807-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="e1807-227">Le formateur XML renvoie un élément `xsi:nil="true"` XML vide avec l’ensemble d’attributs.</span><span class="sxs-lookup"><span data-stu-id="e1807-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="e1807-228">Cartographies URL de format de réponse</span><span class="sxs-lookup"><span data-stu-id="e1807-228">Response format URL mappings</span></span>

<span data-ttu-id="e1807-229">Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :</span><span class="sxs-lookup"><span data-stu-id="e1807-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="e1807-230">Dans la chaîne de requête ou une partie du chemin.</span><span class="sxs-lookup"><span data-stu-id="e1807-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="e1807-231">En utilisant une extension de fichier spécifique au format tel que .xml ou .json.</span><span class="sxs-lookup"><span data-stu-id="e1807-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="e1807-232">Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API.</span><span class="sxs-lookup"><span data-stu-id="e1807-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="e1807-233">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e1807-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="e1807-234">L’itinéraire précédent permet de préciser le format demandé comme extension de fichier facultative.</span><span class="sxs-lookup"><span data-stu-id="e1807-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="e1807-235">L’attribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) vérifie l’existence de `RouteData` la valeur du format dans le format et cartographie le format de réponse au formateur approprié lorsque la réponse est créée.</span><span class="sxs-lookup"><span data-stu-id="e1807-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="e1807-236">Routage</span><span class="sxs-lookup"><span data-stu-id="e1807-236">Route</span></span>        |             <span data-ttu-id="e1807-237">Formateur</span><span class="sxs-lookup"><span data-stu-id="e1807-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="e1807-238">Le formateur de sortie par défaut</span><span class="sxs-lookup"><span data-stu-id="e1807-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="e1807-239">Le formateur JSON (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="e1807-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="e1807-240">Le formateur XML (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="e1807-240">The XML formatter (if configured)</span></span>  |
