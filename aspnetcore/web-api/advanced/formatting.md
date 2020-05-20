---
<span data-ttu-id="085d3-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-102">'Blazor'</span></span>
- <span data-ttu-id="085d3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-103">'Identity'</span></span>
- <span data-ttu-id="085d3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-105">'Razor'</span></span>
- <span data-ttu-id="085d3-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="085d3-107">Mettre en forme les données des réponses dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="085d3-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="085d3-108">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="085d3-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="085d3-109">ASP.NET Core MVC prend en charge la mise en forme des données de réponse.</span><span class="sxs-lookup"><span data-stu-id="085d3-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="085d3-110">Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.</span><span class="sxs-lookup"><span data-stu-id="085d3-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="085d3-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="085d3-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="085d3-112">Résultats des actions spécifiques au format</span><span class="sxs-lookup"><span data-stu-id="085d3-112">Format-specific Action Results</span></span>

<span data-ttu-id="085d3-113">Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="085d3-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="085d3-114">Les actions peuvent retourner des résultats mis en forme dans un format particulier, indépendamment des préférences du client.</span><span class="sxs-lookup"><span data-stu-id="085d3-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="085d3-115">Par exemple, le retour de `JsonResult` retourne des données au format JSON.</span><span class="sxs-lookup"><span data-stu-id="085d3-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="085d3-116">Le retour de `ContentResult` ou une chaîne retourne des données de chaîne au format texte brut.</span><span class="sxs-lookup"><span data-stu-id="085d3-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="085d3-117">Une action n’est pas requise pour retourner un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="085d3-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="085d3-118">ASP.NET Core prend en charge toute valeur de retour d’objet.</span><span class="sxs-lookup"><span data-stu-id="085d3-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="085d3-119">Les résultats des actions qui retournent des objets qui ne sont pas des <xref:Microsoft.AspNetCore.Mvc.IActionResult> types sont sérialisés à l’aide de l' <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implémentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="085d3-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="085d3-120">Pour plus d'informations, consultez <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="085d3-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="085d3-121">La méthode d’assistance intégrée retourne des <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> données au format JSON :[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="085d3-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="085d3-122">L’exemple de téléchargement retourne la liste des auteurs.</span><span class="sxs-lookup"><span data-stu-id="085d3-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="085d3-123">À l’aide des outils de développement du navigateur F12 ou du [poster](https://www.getpostman.com/tools) avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="085d3-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="085d3-124">L’en-tête de réponse contenant **Content-type :** `application/json; charset=utf-8` est affiché.</span><span class="sxs-lookup"><span data-stu-id="085d3-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="085d3-125">Les en-têtes de demande s’affichent.</span><span class="sxs-lookup"><span data-stu-id="085d3-125">The request headers are displayed.</span></span> <span data-ttu-id="085d3-126">Par exemple, l' `Accept` en-tête.</span><span class="sxs-lookup"><span data-stu-id="085d3-126">For example, the `Accept` header.</span></span> <span data-ttu-id="085d3-127">L' `Accept` en-tête est ignoré par le code précédent.</span><span class="sxs-lookup"><span data-stu-id="085d3-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="085d3-128">Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> et le helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> :</span><span class="sxs-lookup"><span data-stu-id="085d3-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="085d3-129">Dans le code précédent, le `Content-Type` retourné est `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="085d3-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="085d3-130">Retour d’une chaîne `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="085d3-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="085d3-131">Pour les actions avec plusieurs types de retour, retournez `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="085d3-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="085d3-132">Par exemple, le retour de codes d’état HTTP différents en fonction du résultat des opérations effectuées.</span><span class="sxs-lookup"><span data-stu-id="085d3-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="085d3-133">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="085d3-133">Content negotiation</span></span>

<span data-ttu-id="085d3-134">La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="085d3-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="085d3-135">Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="085d3-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="085d3-136">La négociation de contenu est :</span><span class="sxs-lookup"><span data-stu-id="085d3-136">Content negotiation is:</span></span>

* <span data-ttu-id="085d3-137">Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="085d3-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="085d3-138">Intégré aux résultats d’action spécifiques au code d’État retournés par les méthodes d’assistance.</span><span class="sxs-lookup"><span data-stu-id="085d3-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="085d3-139">Les méthodes d’assistance des résultats d’action sont basées sur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="085d3-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="085d3-140">Quand un type de modèle est retourné, le type de retour est `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="085d3-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="085d3-141">La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="085d3-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="085d3-142">Par défaut, ASP.NET Core prend en charge `application/json` `text/json` les `text/plain` types de média, et.</span><span class="sxs-lookup"><span data-stu-id="085d3-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="085d3-143">Les outils tels que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/tools) peuvent définir l' `Accept` en-tête de demande pour spécifier le format de retour.</span><span class="sxs-lookup"><span data-stu-id="085d3-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="085d3-144">Lorsque l' `Accept` en-tête contient un type pris en charge par le serveur, ce type est retourné.</span><span class="sxs-lookup"><span data-stu-id="085d3-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="085d3-145">La section suivante montre comment ajouter des formateurs supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="085d3-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="085d3-146">Les actions de contrôleur peuvent retourner des POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="085d3-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="085d3-147">Lorsqu’un POCO est retourné, le runtime crée automatiquement un `ObjectResult` objet qui encapsule l’objet.</span><span class="sxs-lookup"><span data-stu-id="085d3-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="085d3-148">Le client obtient l’objet sérialisé mis en forme.</span><span class="sxs-lookup"><span data-stu-id="085d3-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="085d3-149">Si l’objet retourné est `null` , une `204 No Content` réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="085d3-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="085d3-150">Retour d’un type d’objet :</span><span class="sxs-lookup"><span data-stu-id="085d3-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="085d3-151">Dans le code précédent, une demande d’alias d’auteur valide retourne une `200 OK` réponse avec les données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="085d3-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="085d3-152">Une demande d’alias non valide retourne une `204 No Content` réponse.</span><span class="sxs-lookup"><span data-stu-id="085d3-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="085d3-153">En-tête Accept</span><span class="sxs-lookup"><span data-stu-id="085d3-153">The Accept header</span></span>

<span data-ttu-id="085d3-154">La *négociation* de contenu a lieu lorsqu’un `Accept` en-tête apparaît dans la demande.</span><span class="sxs-lookup"><span data-stu-id="085d3-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="085d3-155">Quand une demande contient un en-tête Accept, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="085d3-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="085d3-156">Énumère les types de médias dans l’en-tête Accept dans l’ordre de préférence.</span><span class="sxs-lookup"><span data-stu-id="085d3-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="085d3-157">Tente de trouver un formateur capable de produire une réponse dans l’un des formats spécifiés.</span><span class="sxs-lookup"><span data-stu-id="085d3-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="085d3-158">Si aucun formateur trouvé pouvant satisfaire la demande du client, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="085d3-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="085d3-159">Retourne `406 Not Acceptable` si <xref:Microsoft.AspNetCore.Mvc.MvcOptions> a été défini, ou-</span><span class="sxs-lookup"><span data-stu-id="085d3-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="085d3-160">Tente de trouver le premier formateur qui peut produire une réponse.</span><span class="sxs-lookup"><span data-stu-id="085d3-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="085d3-161">Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut mettre en forme l’objet est utilisé.</span><span class="sxs-lookup"><span data-stu-id="085d3-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="085d3-162">Si aucun `Accept` en-tête n’apparaît dans la requête :</span><span class="sxs-lookup"><span data-stu-id="085d3-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="085d3-163">Le premier formateur qui peut gérer l’objet est utilisé pour sérialiser la réponse.</span><span class="sxs-lookup"><span data-stu-id="085d3-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="085d3-164">Aucune négociation n’a lieu.</span><span class="sxs-lookup"><span data-stu-id="085d3-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="085d3-165">Le serveur détermine le format à retourner.</span><span class="sxs-lookup"><span data-stu-id="085d3-165">The server is determining what format to return.</span></span>

<span data-ttu-id="085d3-166">Si l’en-tête Accept contient `*/*` , l’en-tête est ignoré, sauf si a la valeur `RespectBrowserAcceptHeader` true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="085d3-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="085d3-167">Navigateurs et négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="085d3-167">Browsers and content negotiation</span></span>

<span data-ttu-id="085d3-168">Contrairement aux clients d’API typiques, les navigateurs Web fournissent des `Accept` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="085d3-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="085d3-169">Le navigateur Web spécifie de nombreux formats, y compris des caractères génériques.</span><span class="sxs-lookup"><span data-stu-id="085d3-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="085d3-170">Par défaut, lorsque le Framework détecte que la demande provient d’un navigateur :</span><span class="sxs-lookup"><span data-stu-id="085d3-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="085d3-171">L' `Accept` en-tête est ignoré.</span><span class="sxs-lookup"><span data-stu-id="085d3-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="085d3-172">Le contenu est renvoyé au format JSON, sauf s’il a été configuré autrement.</span><span class="sxs-lookup"><span data-stu-id="085d3-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="085d3-173">Cela offre une expérience plus cohérente entre les navigateurs lors de l’utilisation des API.</span><span class="sxs-lookup"><span data-stu-id="085d3-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="085d3-174">Pour configurer une application pour honorer les en-têtes Accept du navigateur, définissez <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> sur `true` :</span><span class="sxs-lookup"><span data-stu-id="085d3-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="085d3-175">Configurer les formateurs</span><span class="sxs-lookup"><span data-stu-id="085d3-175">Configure formatters</span></span>

<span data-ttu-id="085d3-176">Les applications qui doivent prendre en charge des formats supplémentaires peuvent ajouter les packages NuGet appropriés et configurer la prise en charge.</span><span class="sxs-lookup"><span data-stu-id="085d3-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="085d3-177">Il existe des formateurs distincts pour les entrées et pour les sorties.</span><span class="sxs-lookup"><span data-stu-id="085d3-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="085d3-178">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="085d3-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="085d3-179">Les formateurs de sortie sont utilisés pour mettre en forme les réponses.</span><span class="sxs-lookup"><span data-stu-id="085d3-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="085d3-180">Pour plus d’informations sur la création d’un formateur personnalisé, consultez [formateurs personnalisés](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="085d3-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="085d3-181">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="085d3-181">Add XML format support</span></span>

<span data-ttu-id="085d3-182">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="085d3-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="085d3-183">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="085d3-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="085d3-184">Lorsque vous utilisez le code précédent, les méthodes de contrôleur retournent le format approprié en fonction de l' `Accept` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="085d3-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="085d3-185">Configurer des formateurs basés sur System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="085d3-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="085d3-186">Les fonctionnalités pour les formateurs basés sur `System.Text.Json` peuvent être configurées avec `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="085d3-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="085d3-187">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="085d3-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="085d3-188">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="085d3-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="085d3-189">Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="085d3-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="085d3-190">Avant ASP.NET Core 3,0, les formateurs JSON utilisés par défaut ont été implémentés à l’aide du `Newtonsoft.Json` Package.</span><span class="sxs-lookup"><span data-stu-id="085d3-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="085d3-191">Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="085d3-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="085d3-192">La prise en charge des `Newtonsoft.Json` formateurs et des fonctionnalités de base est disponible en installant le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet et en le configurant dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="085d3-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="085d3-193">Certaines fonctionnalités peuvent ne pas fonctionner correctement avec `System.Text.Json` les formateurs basés sur et nécessitent une référence aux `Newtonsoft.Json` formateurs basés sur.</span><span class="sxs-lookup"><span data-stu-id="085d3-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="085d3-194">Continuer à utiliser les `Newtonsoft.Json` formateurs basés sur, si l’application :</span><span class="sxs-lookup"><span data-stu-id="085d3-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="085d3-195">Utilise des `Newtonsoft.Json` attributs.</span><span class="sxs-lookup"><span data-stu-id="085d3-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="085d3-196">Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="085d3-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="085d3-197">Personnalise les paramètres de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="085d3-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="085d3-198">S’appuie sur les fonctionnalités `Newtonsoft.Json` fournies par.</span><span class="sxs-lookup"><span data-stu-id="085d3-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="085d3-199">Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="085d3-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="085d3-200">Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="085d3-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="085d3-201">Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="085d3-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="085d3-202">Les fonctionnalités des `Newtonsoft.Json` formateurs basés sur peuvent être configurées à l’aide de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="085d3-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="085d3-203">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="085d3-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="085d3-204">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="085d3-204">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="085d3-205">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="085d3-205">Add XML format support</span></span>

<span data-ttu-id="085d3-206">La mise en forme XML requiert le package NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="085d3-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="085d3-207">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="085d3-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="085d3-208">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="085d3-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="085d3-209">Lorsque vous utilisez le code précédent, les méthodes de contrôleur doivent retourner le format approprié en fonction de l' `Accept` en-tête de la requête.</span><span class="sxs-lookup"><span data-stu-id="085d3-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="085d3-210">Spécifier un format</span><span class="sxs-lookup"><span data-stu-id="085d3-210">Specify a format</span></span>

<span data-ttu-id="085d3-211">Pour restreindre les formats de réponse, appliquez le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre.</span><span class="sxs-lookup"><span data-stu-id="085d3-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="085d3-212">Comme la plupart des [filtres](xref:mvc/controllers/filters), `[Produces]` peut être appliqué au niveau de l’action, du contrôleur ou de l’étendue globale :</span><span class="sxs-lookup"><span data-stu-id="085d3-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="085d3-213">Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :</span><span class="sxs-lookup"><span data-stu-id="085d3-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="085d3-214">Force toutes les actions du contrôleur à retourner des réponses au format JSON.</span><span class="sxs-lookup"><span data-stu-id="085d3-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="085d3-215">Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.</span><span class="sxs-lookup"><span data-stu-id="085d3-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="085d3-216">Pour plus d’informations, consultez [filtres](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="085d3-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="085d3-217">Formateurs de cas spéciaux</span><span class="sxs-lookup"><span data-stu-id="085d3-217">Special case formatters</span></span>

<span data-ttu-id="085d3-218">Certains cas spéciaux sont implémentés avec des formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="085d3-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="085d3-219">Par défaut, `string` les types de retour sont mis en forme en tant que *texte/plain* (*texte/html* si demandé via l' `Accept` en-tête).</span><span class="sxs-lookup"><span data-stu-id="085d3-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="085d3-220">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="085d3-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="085d3-221">Les formateurs sont supprimés de la `ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="085d3-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="085d3-222">Les actions qui ont un type de retour d’objet de modèle retournent `204 No Content` lors du retour de `null` .</span><span class="sxs-lookup"><span data-stu-id="085d3-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="085d3-223">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="085d3-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="085d3-224">Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="085d3-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="085d3-225">Sans le `StringOutputFormatter` , le formateur JSON intégré met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="085d3-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="085d3-226">Si le formateur JSON intégré est supprimé et qu’un formateur XML est disponible, le formateur XML met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="085d3-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="085d3-227">Sinon, les `string` types de retour retournent `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="085d3-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="085d3-228">Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré.</span><span class="sxs-lookup"><span data-stu-id="085d3-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="085d3-229">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="085d3-229">For example:</span></span>

* <span data-ttu-id="085d3-230">Le formateur JSON retourne une réponse avec un corps de `null` .</span><span class="sxs-lookup"><span data-stu-id="085d3-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="085d3-231">Le formateur XML retourne un élément XML vide avec l’ensemble d’attributs `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="085d3-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="085d3-232">Mappages d’URL de format de réponse</span><span class="sxs-lookup"><span data-stu-id="085d3-232">Response format URL mappings</span></span>

<span data-ttu-id="085d3-233">Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :</span><span class="sxs-lookup"><span data-stu-id="085d3-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="085d3-234">Dans la chaîne de requête ou dans une partie du chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="085d3-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="085d3-235">En utilisant une extension de fichier spécifique au format, par exemple. XML ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="085d3-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="085d3-236">Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API.</span><span class="sxs-lookup"><span data-stu-id="085d3-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="085d3-237">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="085d3-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="085d3-238">L’itinéraire précédent permet de spécifier le format demandé en tant qu’extension de fichier facultative.</span><span class="sxs-lookup"><span data-stu-id="085d3-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="085d3-239">L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lorsque la réponse est créée.</span><span class="sxs-lookup"><span data-stu-id="085d3-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="085d3-240">Routage</span><span class="sxs-lookup"><span data-stu-id="085d3-240">Route</span></span>        |             <span data-ttu-id="085d3-241">Formateur</span><span class="sxs-lookup"><span data-stu-id="085d3-241">Formatter</span></span>              |
|---
<span data-ttu-id="085d3-242">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-243">'Blazor'</span></span>
- <span data-ttu-id="085d3-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-244">'Identity'</span></span>
- <span data-ttu-id="085d3-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-246">'Razor'</span></span>
- <span data-ttu-id="085d3-247">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-248">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-249">'Blazor'</span></span>
- <span data-ttu-id="085d3-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-250">'Identity'</span></span>
- <span data-ttu-id="085d3-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-252">'Razor'</span></span>
- <span data-ttu-id="085d3-253">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-254">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-255">'Blazor'</span></span>
- <span data-ttu-id="085d3-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-256">'Identity'</span></span>
- <span data-ttu-id="085d3-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-258">'Razor'</span></span>
- <span data-ttu-id="085d3-259">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-260">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-261">'Blazor'</span></span>
- <span data-ttu-id="085d3-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-262">'Identity'</span></span>
- <span data-ttu-id="085d3-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-264">'Razor'</span></span>
- <span data-ttu-id="085d3-265">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-266">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-267">'Blazor'</span></span>
- <span data-ttu-id="085d3-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-268">'Identity'</span></span>
- <span data-ttu-id="085d3-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-270">'Razor'</span></span>
- <span data-ttu-id="085d3-271">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-272">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-273">'Blazor'</span></span>
- <span data-ttu-id="085d3-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-274">'Identity'</span></span>
- <span data-ttu-id="085d3-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-276">'Razor'</span></span>
- <span data-ttu-id="085d3-277">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-278">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-279">'Blazor'</span></span>
- <span data-ttu-id="085d3-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-280">'Identity'</span></span>
- <span data-ttu-id="085d3-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-282">'Razor'</span></span>
- <span data-ttu-id="085d3-283">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-284">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-285">'Blazor'</span></span>
- <span data-ttu-id="085d3-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-286">'Identity'</span></span>
- <span data-ttu-id="085d3-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-288">'Razor'</span></span>
- <span data-ttu-id="085d3-289">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-290">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-291">'Blazor'</span></span>
- <span data-ttu-id="085d3-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-292">'Identity'</span></span>
- <span data-ttu-id="085d3-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-294">'Razor'</span></span>
- <span data-ttu-id="085d3-295">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-296">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-297">'Blazor'</span></span>
- <span data-ttu-id="085d3-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-298">'Identity'</span></span>
- <span data-ttu-id="085d3-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-300">'Razor'</span></span>
- <span data-ttu-id="085d3-301">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-301">'SignalR' uid:</span></span> 

<span data-ttu-id="085d3-302">------------|---titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-303">'Blazor'</span></span>
- <span data-ttu-id="085d3-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-304">'Identity'</span></span>
- <span data-ttu-id="085d3-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-306">'Razor'</span></span>
- <span data-ttu-id="085d3-307">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-308">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-309">'Blazor'</span></span>
- <span data-ttu-id="085d3-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-310">'Identity'</span></span>
- <span data-ttu-id="085d3-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-312">'Razor'</span></span>
- <span data-ttu-id="085d3-313">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-314">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-315">'Blazor'</span></span>
- <span data-ttu-id="085d3-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-316">'Identity'</span></span>
- <span data-ttu-id="085d3-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-318">'Razor'</span></span>
- <span data-ttu-id="085d3-319">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-320">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-321">'Blazor'</span></span>
- <span data-ttu-id="085d3-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-322">'Identity'</span></span>
- <span data-ttu-id="085d3-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-324">'Razor'</span></span>
- <span data-ttu-id="085d3-325">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-326">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-327">'Blazor'</span></span>
- <span data-ttu-id="085d3-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-328">'Identity'</span></span>
- <span data-ttu-id="085d3-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-330">'Razor'</span></span>
- <span data-ttu-id="085d3-331">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-332">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-333">'Blazor'</span></span>
- <span data-ttu-id="085d3-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-334">'Identity'</span></span>
- <span data-ttu-id="085d3-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-336">'Razor'</span></span>
- <span data-ttu-id="085d3-337">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-338">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-339">'Blazor'</span></span>
- <span data-ttu-id="085d3-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-340">'Identity'</span></span>
- <span data-ttu-id="085d3-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-342">'Razor'</span></span>
- <span data-ttu-id="085d3-343">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-344">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-345">'Blazor'</span></span>
- <span data-ttu-id="085d3-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-346">'Identity'</span></span>
- <span data-ttu-id="085d3-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-348">'Razor'</span></span>
- <span data-ttu-id="085d3-349">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-350">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-351">'Blazor'</span></span>
- <span data-ttu-id="085d3-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-352">'Identity'</span></span>
- <span data-ttu-id="085d3-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-354">'Razor'</span></span>
- <span data-ttu-id="085d3-355">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-356">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-357">'Blazor'</span></span>
- <span data-ttu-id="085d3-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-358">'Identity'</span></span>
- <span data-ttu-id="085d3-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-360">'Razor'</span></span>
- <span data-ttu-id="085d3-361">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-362">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-363">'Blazor'</span></span>
- <span data-ttu-id="085d3-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-364">'Identity'</span></span>
- <span data-ttu-id="085d3-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-366">'Razor'</span></span>
- <span data-ttu-id="085d3-367">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-368">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-369">'Blazor'</span></span>
- <span data-ttu-id="085d3-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-370">'Identity'</span></span>
- <span data-ttu-id="085d3-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-372">'Razor'</span></span>
- <span data-ttu-id="085d3-373">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-374">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-375">'Blazor'</span></span>
- <span data-ttu-id="085d3-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-376">'Identity'</span></span>
- <span data-ttu-id="085d3-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-378">'Razor'</span></span>
- <span data-ttu-id="085d3-379">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-380">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-381">'Blazor'</span></span>
- <span data-ttu-id="085d3-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-382">'Identity'</span></span>
- <span data-ttu-id="085d3-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-384">'Razor'</span></span>
- <span data-ttu-id="085d3-385">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-386">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-387">'Blazor'</span></span>
- <span data-ttu-id="085d3-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-388">'Identity'</span></span>
- <span data-ttu-id="085d3-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-390">'Razor'</span></span>
- <span data-ttu-id="085d3-391">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="085d3-392">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="085d3-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="085d3-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="085d3-393">'Blazor'</span></span>
- <span data-ttu-id="085d3-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="085d3-394">'Identity'</span></span>
- <span data-ttu-id="085d3-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="085d3-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="085d3-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="085d3-396">'Razor'</span></span>
- <span data-ttu-id="085d3-397">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="085d3-397">'SignalR' uid:</span></span> 

<span data-ttu-id="085d3-398">------------------| |   `/api/products/5`    |    Le formateur de sortie par défaut | | `/api/products/5.json` | Le module de formatage JSON (s’il est configuré) | | `/api/products/5.xml`  | Formateur XML (s’il est configuré) |</span><span class="sxs-lookup"><span data-stu-id="085d3-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
