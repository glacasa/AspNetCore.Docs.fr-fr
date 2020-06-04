---
<span data-ttu-id="ea8e6-101">title : mettre en forme les données de réponse dans ASP.NET Core auteur de l’API Web : ardalis Description : Découvrez comment mettre en forme les données de réponse dans ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="ea8e6-102">ms. Author : Riande ms. Custom : H1Hack27Feb2017 ms. Date : 04/17/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="ea8e6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ea8e6-103">'Blazor'</span></span>
- <span data-ttu-id="ea8e6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ea8e6-104">'Identity'</span></span>
- <span data-ttu-id="ea8e6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ea8e6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ea8e6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ea8e6-106">'Razor'</span></span>
- <span data-ttu-id="ea8e6-107">' SignalR 'UID : Web-API/avancé/mise en forme</span><span class="sxs-lookup"><span data-stu-id="ea8e6-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="ea8e6-108">Mettre en forme les données des réponses dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea8e6-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="ea8e6-109">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ea8e6-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ea8e6-110">ASP.NET Core MVC prend en charge la mise en forme des données de réponse.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="ea8e6-111">Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="ea8e6-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea8e6-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="ea8e6-113">Résultats des actions spécifiques au format</span><span class="sxs-lookup"><span data-stu-id="ea8e6-113">Format-specific Action Results</span></span>

<span data-ttu-id="ea8e6-114">Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="ea8e6-115">Les actions peuvent retourner des résultats mis en forme dans un format particulier, indépendamment des préférences du client.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="ea8e6-116">Par exemple, le retour de `JsonResult` retourne des données au format JSON.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="ea8e6-117">Le retour de `ContentResult` ou une chaîne retourne des données de chaîne au format texte brut.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="ea8e6-118">Une action n’est pas requise pour retourner un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="ea8e6-119">ASP.NET Core prend en charge toute valeur de retour d’objet.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="ea8e6-120">Les résultats des actions qui retournent des objets qui ne sont pas des <xref:Microsoft.AspNetCore.Mvc.IActionResult> types sont sérialisés à l’aide de l' <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implémentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="ea8e6-121">Pour plus d’informations, consultez <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="ea8e6-122">La méthode d’assistance intégrée retourne des <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> données au format JSON :[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="ea8e6-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="ea8e6-123">L’exemple de téléchargement retourne la liste des auteurs.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="ea8e6-124">À l’aide des outils de développement du navigateur F12 ou du [poster](https://www.getpostman.com/tools) avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="ea8e6-125">L’en-tête de réponse contenant **Content-type :** `application/json; charset=utf-8` est affiché.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="ea8e6-126">Les en-têtes de demande s’affichent.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-126">The request headers are displayed.</span></span> <span data-ttu-id="ea8e6-127">Par exemple, l' `Accept` en-tête.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-127">For example, the `Accept` header.</span></span> <span data-ttu-id="ea8e6-128">L' `Accept` en-tête est ignoré par le code précédent.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="ea8e6-129">Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> et le helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="ea8e6-130">Dans le code précédent, le `Content-Type` retourné est `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="ea8e6-131">Retour d’une chaîne `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="ea8e6-132">Pour les actions avec plusieurs types de retour, retournez `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="ea8e6-133">Par exemple, le retour de codes d’état HTTP différents en fonction du résultat des opérations effectuées.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="ea8e6-134">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="ea8e6-134">Content negotiation</span></span>

<span data-ttu-id="ea8e6-135">La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="ea8e6-136">Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="ea8e6-137">La négociation de contenu est :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-137">Content negotiation is:</span></span>

* <span data-ttu-id="ea8e6-138">Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="ea8e6-139">Intégré aux résultats d’action spécifiques au code d’État retournés par les méthodes d’assistance.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="ea8e6-140">Les méthodes d’assistance des résultats d’action sont basées sur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="ea8e6-141">Quand un type de modèle est retourné, le type de retour est `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="ea8e6-142">La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="ea8e6-143">Par défaut, ASP.NET Core prend en charge `application/json` `text/json` les `text/plain` types de média, et.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="ea8e6-144">Les outils tels que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/tools) peuvent définir l' `Accept` en-tête de demande pour spécifier le format de retour.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="ea8e6-145">Lorsque l' `Accept` en-tête contient un type pris en charge par le serveur, ce type est retourné.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="ea8e6-146">La section suivante montre comment ajouter des formateurs supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="ea8e6-147">Les actions de contrôleur peuvent retourner des POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="ea8e6-148">Lorsqu’un POCO est retourné, le runtime crée automatiquement un `ObjectResult` objet qui encapsule l’objet.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="ea8e6-149">Le client obtient l’objet sérialisé mis en forme.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="ea8e6-150">Si l’objet retourné est `null` , une `204 No Content` réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="ea8e6-151">Retour d’un type d’objet :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="ea8e6-152">Dans le code précédent, une demande d’alias d’auteur valide retourne une `200 OK` réponse avec les données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="ea8e6-153">Une demande d’alias non valide retourne une `204 No Content` réponse.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="ea8e6-154">En-tête Accept</span><span class="sxs-lookup"><span data-stu-id="ea8e6-154">The Accept header</span></span>

<span data-ttu-id="ea8e6-155">La *négociation* de contenu a lieu lorsqu’un `Accept` en-tête apparaît dans la demande.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="ea8e6-156">Quand une demande contient un en-tête Accept, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="ea8e6-157">Énumère les types de médias dans l’en-tête Accept dans l’ordre de préférence.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="ea8e6-158">Tente de trouver un formateur capable de produire une réponse dans l’un des formats spécifiés.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="ea8e6-159">Si aucun formateur trouvé pouvant satisfaire la demande du client, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="ea8e6-160">Retourne `406 Not Acceptable` si <xref:Microsoft.AspNetCore.Mvc.MvcOptions> a été défini, ou-</span><span class="sxs-lookup"><span data-stu-id="ea8e6-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="ea8e6-161">Tente de trouver le premier formateur qui peut produire une réponse.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="ea8e6-162">Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut mettre en forme l’objet est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="ea8e6-163">Si aucun `Accept` en-tête n’apparaît dans la requête :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="ea8e6-164">Le premier formateur qui peut gérer l’objet est utilisé pour sérialiser la réponse.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="ea8e6-165">Aucune négociation n’a lieu.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="ea8e6-166">Le serveur détermine le format à retourner.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-166">The server is determining what format to return.</span></span>

<span data-ttu-id="ea8e6-167">Si l’en-tête Accept contient `*/*` , l’en-tête est ignoré, sauf si a la valeur `RespectBrowserAcceptHeader` true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="ea8e6-168">Navigateurs et négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="ea8e6-168">Browsers and content negotiation</span></span>

<span data-ttu-id="ea8e6-169">Contrairement aux clients d’API typiques, les navigateurs Web fournissent des `Accept` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="ea8e6-170">Le navigateur Web spécifie de nombreux formats, y compris des caractères génériques.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="ea8e6-171">Par défaut, lorsque le Framework détecte que la demande provient d’un navigateur :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="ea8e6-172">L' `Accept` en-tête est ignoré.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="ea8e6-173">Le contenu est renvoyé au format JSON, sauf s’il a été configuré autrement.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="ea8e6-174">Cela offre une expérience plus cohérente entre les navigateurs lors de l’utilisation des API.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="ea8e6-175">Pour configurer une application pour honorer les en-têtes Accept du navigateur, définissez <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> sur `true` :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="ea8e6-176">Configurer les formateurs</span><span class="sxs-lookup"><span data-stu-id="ea8e6-176">Configure formatters</span></span>

<span data-ttu-id="ea8e6-177">Les applications qui doivent prendre en charge des formats supplémentaires peuvent ajouter les packages NuGet appropriés et configurer la prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="ea8e6-178">Il existe des formateurs distincts pour les entrées et pour les sorties.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="ea8e6-179">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ea8e6-180">Les formateurs de sortie sont utilisés pour mettre en forme les réponses.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="ea8e6-181">Pour plus d’informations sur la création d’un formateur personnalisé, consultez [formateurs personnalisés](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="ea8e6-182">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="ea8e6-182">Add XML format support</span></span>

<span data-ttu-id="ea8e6-183">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="ea8e6-184">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ea8e6-185">Lorsque vous utilisez le code précédent, les méthodes de contrôleur retournent le format approprié en fonction de l' `Accept` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="ea8e6-186">Configurer des formateurs basés sur System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="ea8e6-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="ea8e6-187">Les fonctionnalités pour les formateurs basés sur `System.Text.Json` peuvent être configurées avec `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ea8e6-188">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ea8e6-189">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="ea8e6-190">Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="ea8e6-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="ea8e6-191">Avant ASP.NET Core 3,0, les formateurs JSON utilisés par défaut ont été implémentés à l’aide du `Newtonsoft.Json` Package.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="ea8e6-192">Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="ea8e6-193">La prise en charge des `Newtonsoft.Json` formateurs et des fonctionnalités de base est disponible en installant le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet et en le configurant dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="ea8e6-194">Certaines fonctionnalités peuvent ne pas fonctionner correctement avec `System.Text.Json` les formateurs basés sur et nécessitent une référence aux `Newtonsoft.Json` formateurs basés sur.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="ea8e6-195">Continuer à utiliser les `Newtonsoft.Json` formateurs basés sur, si l’application :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="ea8e6-196">Utilise des `Newtonsoft.Json` attributs.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="ea8e6-197">Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="ea8e6-198">Personnalise les paramètres de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="ea8e6-199">S’appuie sur les fonctionnalités `Newtonsoft.Json` fournies par.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="ea8e6-200">Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="ea8e6-201">Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="ea8e6-202">Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="ea8e6-203">Les fonctionnalités des `Newtonsoft.Json` formateurs basés sur peuvent être configurées à l’aide de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ea8e6-204">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ea8e6-205">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-205">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="ea8e6-206">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="ea8e6-206">Add XML format support</span></span>

<span data-ttu-id="ea8e6-207">La mise en forme XML requiert le package NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="ea8e6-208">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="ea8e6-209">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ea8e6-210">Lorsque vous utilisez le code précédent, les méthodes de contrôleur doivent retourner le format approprié en fonction de l' `Accept` en-tête de la requête.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="ea8e6-211">Spécifier un format</span><span class="sxs-lookup"><span data-stu-id="ea8e6-211">Specify a format</span></span>

<span data-ttu-id="ea8e6-212">Pour restreindre les formats de réponse, appliquez le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="ea8e6-213">Comme la plupart des [filtres](xref:mvc/controllers/filters), `[Produces]` peut être appliqué au niveau de l’action, du contrôleur ou de l’étendue globale :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="ea8e6-214">Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="ea8e6-215">Force toutes les actions du contrôleur à retourner des réponses au format JSON.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="ea8e6-216">Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="ea8e6-217">Pour plus d’informations, consultez [filtres](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="ea8e6-218">Formateurs de cas spéciaux</span><span class="sxs-lookup"><span data-stu-id="ea8e6-218">Special case formatters</span></span>

<span data-ttu-id="ea8e6-219">Certains cas spéciaux sont implémentés avec des formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="ea8e6-220">Par défaut, `string` les types de retour sont mis en forme en tant que *texte/plain* (*texte/html* si demandé via l' `Accept` en-tête).</span><span class="sxs-lookup"><span data-stu-id="ea8e6-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="ea8e6-221">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="ea8e6-222">Les formateurs sont supprimés de la `ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="ea8e6-223">Les actions qui ont un type de retour d’objet de modèle retournent `204 No Content` lors du retour de `null` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="ea8e6-224">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="ea8e6-225">Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="ea8e6-226">Sans le `StringOutputFormatter` , le formateur JSON intégré met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="ea8e6-227">Si le formateur JSON intégré est supprimé et qu’un formateur XML est disponible, le formateur XML met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="ea8e6-228">Sinon, les `string` types de retour retournent `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="ea8e6-229">Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="ea8e6-230">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-230">For example:</span></span>

* <span data-ttu-id="ea8e6-231">Le formateur JSON retourne une réponse avec un corps de `null` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="ea8e6-232">Le formateur XML retourne un élément XML vide avec l’ensemble d’attributs `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="ea8e6-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="ea8e6-233">Mappages d’URL de format de réponse</span><span class="sxs-lookup"><span data-stu-id="ea8e6-233">Response format URL mappings</span></span>

<span data-ttu-id="ea8e6-234">Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="ea8e6-235">Dans la chaîne de requête ou dans une partie du chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="ea8e6-236">En utilisant une extension de fichier spécifique au format, par exemple. XML ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="ea8e6-237">Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="ea8e6-238">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea8e6-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ea8e6-239">L’itinéraire précédent permet de spécifier le format demandé en tant qu’extension de fichier facultative.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="ea8e6-240">L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lorsque la réponse est créée.</span><span class="sxs-lookup"><span data-stu-id="ea8e6-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="ea8e6-241">Routage</span><span class="sxs-lookup"><span data-stu-id="ea8e6-241">Route</span></span>        |             <span data-ttu-id="ea8e6-242">Formateur</span><span class="sxs-lookup"><span data-stu-id="ea8e6-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="ea8e6-243">Le formateur de sortie par défaut</span><span class="sxs-lookup"><span data-stu-id="ea8e6-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="ea8e6-244">Le formateur JSON (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="ea8e6-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="ea8e6-245">Le formateur XML (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="ea8e6-245">The XML formatter (if configured)</span></span>  |
