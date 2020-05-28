---
<span data-ttu-id="b0588-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-102">'Blazor'</span></span>
- <span data-ttu-id="b0588-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-103">'Identity'</span></span>
- <span data-ttu-id="b0588-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-105">'Razor'</span></span>
- <span data-ttu-id="b0588-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="b0588-107">Intergiciel (middleware) de réécriture d’URL dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0588-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="b0588-108">Par [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="b0588-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0588-109">Ce document présente la réécriture d’URL avec des instructions sur la façon d’utiliser l’intergiciel (middleware) de réécriture d’URL dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0588-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="b0588-110">La réécriture d’URL consiste à modifier des URL de requête en fonction d’une ou de plusieurs règles prédéfinies.</span><span class="sxs-lookup"><span data-stu-id="b0588-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="b0588-111">La réécriture d’URL crée une abstraction entre les emplacements des ressources et leurs adresses pour que les emplacements et les adresses ne soient pas étroitement liés.</span><span class="sxs-lookup"><span data-stu-id="b0588-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="b0588-112">La réécriture d’URL est utile dans plusieurs scénarios pour :</span><span class="sxs-lookup"><span data-stu-id="b0588-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="b0588-113">Déplacer ou remplacer de façon temporaire ou permanente les ressources d’un serveur, tout en conservant des localisateurs stables pour ces ressources.</span><span class="sxs-lookup"><span data-stu-id="b0588-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="b0588-114">Diviser le traitement des requêtes entre différentes applications ou entre différentes parties d’une même application.</span><span class="sxs-lookup"><span data-stu-id="b0588-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="b0588-115">Supprimer, ajouter ou réorganiser les segments d’URL sur des requêtes entrantes.</span><span class="sxs-lookup"><span data-stu-id="b0588-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="b0588-116">Optimiser les URL publiques pour l’optimisation du référencement d’un site auprès d’un moteur de recherche (SEO).</span><span class="sxs-lookup"><span data-stu-id="b0588-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="b0588-117">Permettre l’utilisation des URL publiques conviviales pour aider les visiteurs à prédire le contenu retourné en demandant une ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="b0588-118">Rediriger des requêtes non sécurisées vers des points de terminaison sécurisés.</span><span class="sxs-lookup"><span data-stu-id="b0588-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="b0588-119">Empêcher les liaisons à chaud, où un site externe utilise une ressource statique hébergée sur un autre site en liant la ressource dans son propre contenu.</span><span class="sxs-lookup"><span data-stu-id="b0588-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-120">La réécriture d’URL peut réduire les performances d’une application.</span><span class="sxs-lookup"><span data-stu-id="b0588-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="b0588-121">Quand c’est possible, limitez le nombre et la complexité des règles.</span><span class="sxs-lookup"><span data-stu-id="b0588-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="b0588-122">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0588-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="b0588-123">Redirection d’URL et réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="b0588-124">La différence de formulation entre la *redirection d’URL* et la *réécriture d’URL* est subtile, mais elle a des implications importantes sur la fourniture de ressources aux clients.</span><span class="sxs-lookup"><span data-stu-id="b0588-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="b0588-125">L’intergiciel de réécriture d’URL d’ASP.NET Core est capables de répondre aux besoins des deux.</span><span class="sxs-lookup"><span data-stu-id="b0588-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="b0588-126">Une *redirection d’URL* implique une opération côté client, où le client est invité à accéder à une ressource à une autre adresse que celle demandée à l’origine.</span><span class="sxs-lookup"><span data-stu-id="b0588-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="b0588-127">Ceci nécessite un aller-retour avec le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-127">This requires a round trip to the server.</span></span> <span data-ttu-id="b0588-128">L’URL de redirection retournée au client s’affiche dans la barre d’adresse du navigateur quand le client effectue une nouvelle requête pour la ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="b0588-129">Si `/resource` est *redirigée* vers `/different-resource`, le serveur répond que le client doit obtenir la ressource à l’emplacement `/different-resource` avec un code d’état indiquant que la redirection est temporaire ou permanente.</span><span class="sxs-lookup"><span data-stu-id="b0588-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Un point de terminaison de service WebAPI a été changé temporairement de la version 1 (v1) à la version 2 (v2) sur le serveur.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="b0588-135">Lors de la redirection des requêtes vers une URL différente, indiquez si la redirection est permanente ou temporaire en spécifiant le code d’état avec la réponse :</span><span class="sxs-lookup"><span data-stu-id="b0588-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="b0588-136">Le code d’état *301 - Déplacé de façon permanente* est utilisé quand la ressource a une nouvelle URL permanente et que vous voulez indiquer au client que toutes les requêtes futures pour la ressource doivent utiliser la nouvelle URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="b0588-137">*Le client peut mettre en cache et réutiliser la réponse quand un code d’état 301 est reçu.*</span><span class="sxs-lookup"><span data-stu-id="b0588-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="b0588-138">Le code d’état *302 - Trouvé* est utilisé quand la redirection est temporaire ou généralement susceptible d’être modifiée.</span><span class="sxs-lookup"><span data-stu-id="b0588-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="b0588-139">Le code d’état 302 indique au client de ne pas stocker l’URL et de ne plus l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="b0588-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="b0588-140">Pour plus d’informations sur les codes d’état, consultez [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="b0588-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="b0588-141">Une *réécriture d’URL* est une opération côté serveur qui fournit une ressource à partir d’une adresse de ressource différente de celle demandée par le client.</span><span class="sxs-lookup"><span data-stu-id="b0588-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="b0588-142">La réécriture d’URL ne nécessite pas d’aller-retour avec le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="b0588-143">L’URL réécrite n’est pas retournée au client et n’apparaît pas dans la barre d’adresse du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b0588-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="b0588-144">Si `/resource` est *réécrite* en `/different-resource`, le serveur récupère la ressource *en interne* à l’emplacement `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="b0588-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="b0588-145">Même si le client peut récupérer la ressource à l’URL réécrite, il n’est pas informé que la ressource existe à l’URL réécrite quand il fait sa requête et reçoit la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Un point de terminaison de service WebAPI a été changé de la version 1 (v1) à la version 2 (v2) sur le serveur.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="b0588-150">Exemple d’application de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-150">URL rewriting sample app</span></span>

<span data-ttu-id="b0588-151">Vous pouvez explorer les fonctionnalités du middleware de réécriture d’URL avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="b0588-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="b0588-152">L’application applique des règles de redirection et de réécriture, et montre l’URL redirigée ou réécrite pour plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="b0588-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="b0588-153">Quand utiliser l’intergiciel (middleware) de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="b0588-154">Utilisez le middleware de réécriture d’URL quand vous ne pouvez pas utiliser les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="b0588-155">Module de réécriture d’URL avec IIS sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="b0588-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="b0588-156">Module mod_rewrite Apache sur Apache Server</span><span class="sxs-lookup"><span data-stu-id="b0588-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b0588-157">URL de réécriture sur Nginx</span><span class="sxs-lookup"><span data-stu-id="b0588-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="b0588-158">Utilisez aussi le middleware quand l’application est hébergée sur le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé WebListener).</span><span class="sxs-lookup"><span data-stu-id="b0588-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="b0588-159">Les principales raisons d’utiliser les technologies de réécriture d’URL basée sur le serveur dans IIS, Apache et Nginx sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="b0588-160">Le middleware ne prend pas en charge toutes les fonctionnalités de ces modules.</span><span class="sxs-lookup"><span data-stu-id="b0588-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="b0588-161">Certaines des fonctionnalités des modules serveur ne fonctionnent pas avec les projets ASP.NET Core, comme les contraintes `IsFile` et `IsDirectory` du module Réécriture IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="b0588-162">Dans ces scénarios, utilisez plutôt l’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="b0588-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="b0588-163">Les performances du middleware ne correspondent probablement pas à celles des modules.</span><span class="sxs-lookup"><span data-stu-id="b0588-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="b0588-164">Mener des tests de performances est la seule façon de savoir exactement quelle approche dégrade le plus les performances ou si la dégradation des performances est négligeable.</span><span class="sxs-lookup"><span data-stu-id="b0588-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="b0588-165">Paquet</span><span class="sxs-lookup"><span data-stu-id="b0588-165">Package</span></span>

<span data-ttu-id="b0588-166">L’intergiciel (middleware) de réécriture d’URL est fourni par le package [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), qui est implicitement inclus dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0588-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="b0588-167">Extension et options</span><span class="sxs-lookup"><span data-stu-id="b0588-167">Extension and options</span></span>

<span data-ttu-id="b0588-168">Établissez des règles de réécriture et de redirection d’URL en créant une instance de la classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) avec des méthodes d’extension pour chacune de vos règles de réécriture.</span><span class="sxs-lookup"><span data-stu-id="b0588-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="b0588-169">Chaînez plusieurs règles dans l’ordre dans lequel vous voulez qu’elles soient traitées.</span><span class="sxs-lookup"><span data-stu-id="b0588-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="b0588-170">Les `RewriteOptions` sont passées dans le middleware de réécriture d’URL quand il est ajouté au pipeline de requête avec <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="b0588-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="b0588-171">Redirection de demandes non-www en demandes www</span><span class="sxs-lookup"><span data-stu-id="b0588-171">Redirect non-www to www</span></span>

<span data-ttu-id="b0588-172">Trois options permettent à l’application de rediriger des demandes non-`www` en demandes `www` :</span><span class="sxs-lookup"><span data-stu-id="b0588-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="b0588-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Rediriger définitivement la demande vers le `www` sous-domaine si la demande n’est pas `www` .</span><span class="sxs-lookup"><span data-stu-id="b0588-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="b0588-174">Redirige avec un code d’état [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="b0588-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="b0588-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirigez la demande vers le `www` sous-domaine si la demande entrante n’est pas `www` .</span><span class="sxs-lookup"><span data-stu-id="b0588-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="b0588-176">Redirige avec un code d’état [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="b0588-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="b0588-177">Une surcharge vous permet de fournir le code d’état pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="b0588-178">Utilisez un champ de la classe <xref:Microsoft.AspNetCore.Http.StatusCodes> pour une affectation de code d’état.</span><span class="sxs-lookup"><span data-stu-id="b0588-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="b0588-179">Redirection d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-179">URL redirect</span></span>

<span data-ttu-id="b0588-180">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pour rediriger des requêtes.</span><span class="sxs-lookup"><span data-stu-id="b0588-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="b0588-181">Le premier paramètre contient votre expression régulière pour la mise en correspondance sur le chemin de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="b0588-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="b0588-182">Le deuxième paramètre est la chaîne de remplacement.</span><span class="sxs-lookup"><span data-stu-id="b0588-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="b0588-183">Le troisième paramètre, le cas échéant, spécifie le code d’état.</span><span class="sxs-lookup"><span data-stu-id="b0588-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="b0588-184">Si vous ne spécifiez pas le code d’état, sa valeur par défaut est *302 - Trouvé*, ce qui indique que la ressource est temporairement déplacée ou remplacée.</span><span class="sxs-lookup"><span data-stu-id="b0588-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="b0588-185">Dans un navigateur dans lequel les outils de développement sont activés, effectuez une requête à l’exemple d’application avec le chemin `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="b0588-186">L’expression régulière établit une correspondance avec le chemin de la requête sur `redirect-rule/(.*)`, et le chemin est remplacé par `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="b0588-187">L’URL de redirection est renvoyée au client avec le code d’état *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="b0588-188">Le navigateur effectue une nouvelle requête à l’URL de redirection, qui apparaît dans la barre d’adresse du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b0588-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="b0588-189">Comme aucune règle de l’exemple d’application ne correspond sur l’URL de redirection :</span><span class="sxs-lookup"><span data-stu-id="b0588-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="b0588-190">La deuxième requête reçoit une réponse *200 - OK* de l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="b0588-191">Le corps de la réponse montre l’URL de redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="b0588-192">Un aller-retour est effectué avec le serveur quand une URL est *redirigée*.</span><span class="sxs-lookup"><span data-stu-id="b0588-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="b0588-193">Soyez prudent lors de l’établissement de règles de redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="b0588-194">Les règles de redirection sont évaluées à chaque requête effectuée à l’application, notamment après une redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="b0588-195">Il est facile de créer accidentellement une *boucle de redirections infinies*.</span><span class="sxs-lookup"><span data-stu-id="b0588-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="b0588-196">Requête d’origine : `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b0588-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="b0588-198">La partie de l’expression entre parenthèses est appelée *groupe de capture*.</span><span class="sxs-lookup"><span data-stu-id="b0588-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="b0588-199">Le point (`.`) de l’expression signifie *mettre en correspondance n’importe quel caractère*.</span><span class="sxs-lookup"><span data-stu-id="b0588-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="b0588-200">L’astérisque (`*`) indique *mettre en correspondance zéro occurrence ou plus du caractère précédent*.</span><span class="sxs-lookup"><span data-stu-id="b0588-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="b0588-201">Par conséquent, les deux derniers segments de chemin de l’URL, `1234/5678`, sont capturés par le groupe de capture `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="b0588-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="b0588-202">Toute valeur fournie dans l’URL de la requête après `redirect-rule/` est capturée par ce groupe de capture unique.</span><span class="sxs-lookup"><span data-stu-id="b0588-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="b0588-203">Dans la chaîne de remplacement, les groupes capturés sont injectés dans la chaîne avec le signe dollar (`$`) suivi du numéro de séquence de la capture.</span><span class="sxs-lookup"><span data-stu-id="b0588-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="b0588-204">La valeur du premier groupe de capture est obtenue avec `$1`, la deuxième avec `$2`, et ainsi de suite en séquence pour les groupes de capture de votre expression régulière.</span><span class="sxs-lookup"><span data-stu-id="b0588-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="b0588-205">Comme il n’y a qu’un seul groupe capturé dans l’expression régulière de la règle de redirection de l’exemple d’application, un seul groupe est injecté dans la chaîne de remplacement, à savoir `$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="b0588-206">Quand la règle est appliquée, l’URL devient `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="b0588-207">Redirection d’URL vers un point de terminaison sécurisé</span><span class="sxs-lookup"><span data-stu-id="b0588-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="b0588-208">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pour rediriger les requêtes HTTP vers le même hôte et le même chemin avec le protocole HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0588-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="b0588-209">Si le code d’état n’est pas fourni, le middleware utilise par défaut *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="b0588-210">Si le port n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="b0588-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="b0588-211">Le middleware utilise par défaut `null`.</span><span class="sxs-lookup"><span data-stu-id="b0588-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="b0588-212">Le schéma change en `https` (protocole HTTPS), et le client accède à la ressource sur le port 443.</span><span class="sxs-lookup"><span data-stu-id="b0588-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="b0588-213">L’exemple suivant montre comment définir le code d’état sur *301 - Déplacé de façon permanente* et changer le port en 5001.</span><span class="sxs-lookup"><span data-stu-id="b0588-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="b0588-214">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> pour rediriger les requêtes non sécurisées vers le même hôte et le même chemin avec le protocole HTTPS sécurisé sur le port 443.</span><span class="sxs-lookup"><span data-stu-id="b0588-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="b0588-215">Le middleware définit le code d’état sur *301 - Déplacé de façon permanente*.</span><span class="sxs-lookup"><span data-stu-id="b0588-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="b0588-216">Si vous effectuez une redirection vers un point de terminaison sécurisé et que vous n’avez pas besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser le middleware de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0588-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b0588-217">Pour plus d’informations, consultez la rubrique [Appliquer HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="b0588-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="b0588-218">L’exemple d’application peut montrer comment utiliser `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="b0588-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="b0588-219">Ajoutez la méthode d’extension à `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0588-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="b0588-220">Effectuez une requête non sécurisée à l’application à n’importe quelle URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="b0588-221">Ignorez l’avertissement de sécurité du navigateur indiquant que le certificat auto-signé n’est pas approuvé ou créez une exception pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="b0588-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="b0588-222">Requête d’origine utilisant `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="b0588-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="b0588-224">Requête d’origine utilisant `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="b0588-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="b0588-226">Réécrire URL</span><span class="sxs-lookup"><span data-stu-id="b0588-226">URL rewrite</span></span>

<span data-ttu-id="b0588-227">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> pour créer une règle pour la réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="b0588-228">Le premier paramètre contient l’expression régulière pour la mise en correspondance sur le chemin de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="b0588-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="b0588-229">Le deuxième paramètre est la chaîne de remplacement.</span><span class="sxs-lookup"><span data-stu-id="b0588-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="b0588-230">Le troisième paramètre, `skipRemainingRules: {true|false}`, indique à l’intergiciel d’ignorer, ou non, les règles de réécriture supplémentaires si la règle actuelle est appliquée.</span><span class="sxs-lookup"><span data-stu-id="b0588-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="b0588-231">Requête d’origine : `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b0588-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="b0588-233">Le caret (`^`) au début de l’expression signifie que la correspondance commence au début du chemin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="b0588-234">Dans l’exemple précédent avec la règle de redirection, `redirect-rule/(.*)`, il n’existe pas de caret (`^`) au début de l’expression régulière.</span><span class="sxs-lookup"><span data-stu-id="b0588-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="b0588-235">Ainsi, n’importe quel caractère peut précéder `redirect-rule/` dans le chemin pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="b0588-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="b0588-236">Path</span><span class="sxs-lookup"><span data-stu-id="b0588-236">Path</span></span>                               | <span data-ttu-id="b0588-237">Correspond</span><span class="sxs-lookup"><span data-stu-id="b0588-237">Match</span></span> |
| ---
<span data-ttu-id="b0588-238">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-239">'Blazor'</span></span>
- <span data-ttu-id="b0588-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-240">'Identity'</span></span>
- <span data-ttu-id="b0588-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-242">'Razor'</span></span>
- <span data-ttu-id="b0588-243">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-244">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-245">'Blazor'</span></span>
- <span data-ttu-id="b0588-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-246">'Identity'</span></span>
- <span data-ttu-id="b0588-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-248">'Razor'</span></span>
- <span data-ttu-id="b0588-249">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-250">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-251">'Blazor'</span></span>
- <span data-ttu-id="b0588-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-252">'Identity'</span></span>
- <span data-ttu-id="b0588-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-254">'Razor'</span></span>
- <span data-ttu-id="b0588-255">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-256">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-257">'Blazor'</span></span>
- <span data-ttu-id="b0588-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-258">'Identity'</span></span>
- <span data-ttu-id="b0588-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-260">'Razor'</span></span>
- <span data-ttu-id="b0588-261">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-262">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-263">'Blazor'</span></span>
- <span data-ttu-id="b0588-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-264">'Identity'</span></span>
- <span data-ttu-id="b0588-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-266">'Razor'</span></span>
- <span data-ttu-id="b0588-267">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-268">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-269">'Blazor'</span></span>
- <span data-ttu-id="b0588-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-270">'Identity'</span></span>
- <span data-ttu-id="b0588-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-272">'Razor'</span></span>
- <span data-ttu-id="b0588-273">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-274">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-275">'Blazor'</span></span>
- <span data-ttu-id="b0588-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-276">'Identity'</span></span>
- <span data-ttu-id="b0588-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-278">'Razor'</span></span>
- <span data-ttu-id="b0588-279">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-280">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-281">'Blazor'</span></span>
- <span data-ttu-id="b0588-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-282">'Identity'</span></span>
- <span data-ttu-id="b0588-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-284">'Razor'</span></span>
- <span data-ttu-id="b0588-285">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-286">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-287">'Blazor'</span></span>
- <span data-ttu-id="b0588-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-288">'Identity'</span></span>
- <span data-ttu-id="b0588-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-290">'Razor'</span></span>
- <span data-ttu-id="b0588-291">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-292">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-293">'Blazor'</span></span>
- <span data-ttu-id="b0588-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-294">'Identity'</span></span>
- <span data-ttu-id="b0588-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-296">'Razor'</span></span>
- <span data-ttu-id="b0588-297">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-298">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-299">'Blazor'</span></span>
- <span data-ttu-id="b0588-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-300">'Identity'</span></span>
- <span data-ttu-id="b0588-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-302">'Razor'</span></span>
- <span data-ttu-id="b0588-303">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-304">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-305">'Blazor'</span></span>
- <span data-ttu-id="b0588-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-306">'Identity'</span></span>
- <span data-ttu-id="b0588-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-308">'Razor'</span></span>
- <span data-ttu-id="b0588-309">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-310">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-311">'Blazor'</span></span>
- <span data-ttu-id="b0588-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-312">'Identity'</span></span>
- <span data-ttu-id="b0588-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-314">'Razor'</span></span>
- <span data-ttu-id="b0588-315">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-316">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-317">'Blazor'</span></span>
- <span data-ttu-id="b0588-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-318">'Identity'</span></span>
- <span data-ttu-id="b0588-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-320">'Razor'</span></span>
- <span data-ttu-id="b0588-321">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-322">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-323">'Blazor'</span></span>
- <span data-ttu-id="b0588-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-324">'Identity'</span></span>
- <span data-ttu-id="b0588-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-326">'Razor'</span></span>
- <span data-ttu-id="b0588-327">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-327">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Oui | | `/my-cool-redirect-rule/1234/5678` | Oui | | `/anotherredirect-rule/1234/5678`  | Oui |</span><span class="sxs-lookup"><span data-stu-id="b0588-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="b0588-329">La règle de réécriture, `^rewrite-rule/(\d+)/(\d+)`, établit une correspondance uniquement avec des chemins d’accès s’ils commencent par `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="b0588-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="b0588-330">Dans le tableau suivant, notez la différence de correspondance.</span><span class="sxs-lookup"><span data-stu-id="b0588-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="b0588-331">Path</span><span class="sxs-lookup"><span data-stu-id="b0588-331">Path</span></span>                              | <span data-ttu-id="b0588-332">Correspond</span><span class="sxs-lookup"><span data-stu-id="b0588-332">Match</span></span> |
| ---
<span data-ttu-id="b0588-333">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-334">'Blazor'</span></span>
- <span data-ttu-id="b0588-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-335">'Identity'</span></span>
- <span data-ttu-id="b0588-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-337">'Razor'</span></span>
- <span data-ttu-id="b0588-338">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-339">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-340">'Blazor'</span></span>
- <span data-ttu-id="b0588-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-341">'Identity'</span></span>
- <span data-ttu-id="b0588-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-343">'Razor'</span></span>
- <span data-ttu-id="b0588-344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-345">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-346">'Blazor'</span></span>
- <span data-ttu-id="b0588-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-347">'Identity'</span></span>
- <span data-ttu-id="b0588-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-349">'Razor'</span></span>
- <span data-ttu-id="b0588-350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-351">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-352">'Blazor'</span></span>
- <span data-ttu-id="b0588-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-353">'Identity'</span></span>
- <span data-ttu-id="b0588-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-355">'Razor'</span></span>
- <span data-ttu-id="b0588-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-357">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-358">'Blazor'</span></span>
- <span data-ttu-id="b0588-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-359">'Identity'</span></span>
- <span data-ttu-id="b0588-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-361">'Razor'</span></span>
- <span data-ttu-id="b0588-362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-363">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-364">'Blazor'</span></span>
- <span data-ttu-id="b0588-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-365">'Identity'</span></span>
- <span data-ttu-id="b0588-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-367">'Razor'</span></span>
- <span data-ttu-id="b0588-368">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-369">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-370">'Blazor'</span></span>
- <span data-ttu-id="b0588-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-371">'Identity'</span></span>
- <span data-ttu-id="b0588-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-373">'Razor'</span></span>
- <span data-ttu-id="b0588-374">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-375">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-376">'Blazor'</span></span>
- <span data-ttu-id="b0588-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-377">'Identity'</span></span>
- <span data-ttu-id="b0588-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-379">'Razor'</span></span>
- <span data-ttu-id="b0588-380">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-381">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-382">'Blazor'</span></span>
- <span data-ttu-id="b0588-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-383">'Identity'</span></span>
- <span data-ttu-id="b0588-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-385">'Razor'</span></span>
- <span data-ttu-id="b0588-386">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-387">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-388">'Blazor'</span></span>
- <span data-ttu-id="b0588-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-389">'Identity'</span></span>
- <span data-ttu-id="b0588-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-391">'Razor'</span></span>
- <span data-ttu-id="b0588-392">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-393">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-394">'Blazor'</span></span>
- <span data-ttu-id="b0588-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-395">'Identity'</span></span>
- <span data-ttu-id="b0588-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-397">'Razor'</span></span>
- <span data-ttu-id="b0588-398">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-399">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-400">'Blazor'</span></span>
- <span data-ttu-id="b0588-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-401">'Identity'</span></span>
- <span data-ttu-id="b0588-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-403">'Razor'</span></span>
- <span data-ttu-id="b0588-404">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-405">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-406">'Blazor'</span></span>
- <span data-ttu-id="b0588-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-407">'Identity'</span></span>
- <span data-ttu-id="b0588-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-409">'Razor'</span></span>
- <span data-ttu-id="b0588-410">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-411">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-412">'Blazor'</span></span>
- <span data-ttu-id="b0588-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-413">'Identity'</span></span>
- <span data-ttu-id="b0588-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-415">'Razor'</span></span>
- <span data-ttu-id="b0588-416">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-416">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Oui | | `/my-cool-rewrite-rule/1234/5678` | Non | | `/anotherrewrite-rule/1234/5678`  | Non |</span><span class="sxs-lookup"><span data-stu-id="b0588-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="b0588-418">À la suite de la partie `^rewrite-rule/` de l’expression se trouvent deux groupes de capture, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="b0588-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="b0588-419">`\d` signifie *établir une correspondance avec un chiffre (nombre)*.</span><span class="sxs-lookup"><span data-stu-id="b0588-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="b0588-420">Le signe plus (`+`) signifie *établir une correspondance avec une ou plusieurs occurrences du caractère précédent*.</span><span class="sxs-lookup"><span data-stu-id="b0588-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="b0588-421">Par conséquent, l’URL doit contenir un nombre suivi d’une barre oblique, elle-même suivie d’un autre nombre.</span><span class="sxs-lookup"><span data-stu-id="b0588-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="b0588-422">Ces groupes sont injectés dans l’URL réécrite sous la forme `$1` et `$2`.</span><span class="sxs-lookup"><span data-stu-id="b0588-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="b0588-423">La chaîne de remplacement de la règle de réécriture place les groupes capturés dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="b0588-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="b0588-424">Le chemin demandé `/rewrite-rule/1234/5678` est réécrit pour obtenir la ressource à l’emplacement `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="b0588-425">Si une chaîne de requête est présente dans la requête d’origine, elle est conservée lors de la réécriture de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="b0588-426">Il n’y a pas d’aller-retour avec le serveur pour obtenir la ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="b0588-427">Si la ressource existe, elle est récupérée et retournée au client avec le code d’état *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="b0588-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="b0588-428">Comme le client n’est pas redirigé, l’URL dans la barre d’adresse du navigateur ne change pas.</span><span class="sxs-lookup"><span data-stu-id="b0588-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="b0588-429">Les clients ne peuvent pas détecter qu’une opération de réécriture d’URL s’est produite sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-430">Quand c’est possible, utilisez `skipRemainingRules: true`, car la mise en correspondance de règles est un processus gourmand en ressources qui augmente le temps de réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="b0588-431">Pour obtenir la réponse d’application la plus rapide :</span><span class="sxs-lookup"><span data-stu-id="b0588-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="b0588-432">Classez vos règles de réécriture en partant de la règle la plus souvent mise en correspondance jusqu’à la règle la moins souvent mise en correspondance.</span><span class="sxs-lookup"><span data-stu-id="b0588-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="b0588-433">Ignorez le traitement des règles restantes quand une correspondance est trouvée et qu’aucun traitement de règle supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0588-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="b0588-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b0588-434">Apache mod_rewrite</span></span>

<span data-ttu-id="b0588-435">Appliquez des règles Apache mod_rewrite avec <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="b0588-436">Vérifiez que le fichier de règles est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b0588-437">Pour obtenir plus d’informations et des exemples de règles mod_rewrite, consultez [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="b0588-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="b0588-438">Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="b0588-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="b0588-439">L’exemple d’application redirige les requêtes de `/apache-mod-rules-redirect/(.\*)` vers `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="b0588-440">Le code d’état de la réponse est *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="b0588-441">Requête d’origine : `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="b0588-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="b0588-443">L’intergiciel prend en charge les variables de serveur Apache mod_rewrite suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="b0588-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b0588-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b0588-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b0588-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b0588-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b0588-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="b0588-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="b0588-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="b0588-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b0588-449">HTTP_HOST</span></span>
* <span data-ttu-id="b0588-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b0588-450">HTTP_REFERER</span></span>
* <span data-ttu-id="b0588-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b0588-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b0588-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b0588-452">HTTPS</span></span>
* <span data-ttu-id="b0588-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="b0588-453">IPV6</span></span>
* <span data-ttu-id="b0588-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b0588-454">QUERY_STRING</span></span>
* <span data-ttu-id="b0588-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-456">REMOTE_PORT</span></span>
* <span data-ttu-id="b0588-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b0588-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="b0588-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="b0588-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="b0588-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="b0588-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b0588-460">REQUEST_URI</span></span>
* <span data-ttu-id="b0588-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="b0588-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-462">SERVER_ADDR</span></span>
* <span data-ttu-id="b0588-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-463">SERVER_PORT</span></span>
* <span data-ttu-id="b0588-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="b0588-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="b0588-465">TEMPS</span><span class="sxs-lookup"><span data-stu-id="b0588-465">TIME</span></span>
* <span data-ttu-id="b0588-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="b0588-466">TIME_DAY</span></span>
* <span data-ttu-id="b0588-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="b0588-467">TIME_HOUR</span></span>
* <span data-ttu-id="b0588-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="b0588-468">TIME_MIN</span></span>
* <span data-ttu-id="b0588-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="b0588-469">TIME_MON</span></span>
* <span data-ttu-id="b0588-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="b0588-470">TIME_SEC</span></span>
* <span data-ttu-id="b0588-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="b0588-471">TIME_WDAY</span></span>
* <span data-ttu-id="b0588-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="b0588-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="b0588-473">Règles du module de réécriture d’URL IIS</span><span class="sxs-lookup"><span data-stu-id="b0588-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="b0588-474">Pour utiliser le même ensemble de règles que celui qui s’applique au module de réécriture d’URL IIS, utilisez <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="b0588-475">Vérifiez que le fichier de règles est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b0588-476">N’indiquez pas au middleware d’utiliser le fichier *web.config* de l’application en cas d’exécution sur Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="b0588-477">Avec IIS, ces règles doivent être stockées en dehors du fichier *web.config* de l’application pour éviter les conflits avec le module de réécriture IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="b0588-478">Pour obtenir plus d’informations et des exemples de règles du module de réécriture d’URL IIS, consultez [Utilisation du module de réécriture d’URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) et [Informations de référence sur la configuration du module de réécriture d’URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="b0588-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="b0588-479">Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles *IISUrlRewrite. xml* :</span><span class="sxs-lookup"><span data-stu-id="b0588-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="b0588-480">L’exemple d’application réécrit les requêtes de `/iis-rules-rewrite/(.*)` vers `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="b0588-481">La réponse est envoyée au client avec le code d’état *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="b0588-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="b0588-482">Requête d’origine : `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="b0588-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="b0588-484">Si vous avez un module de réécriture IIS actif pour lequel des règles au niveau du serveur qui affecteraient de façon non souhaitée votre application sont configurées, vous pouvez le désactiver pour une application.</span><span class="sxs-lookup"><span data-stu-id="b0588-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="b0588-485">Pour plus d’informations, consultez [Désactivation de modules IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="b0588-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="b0588-486">Fonctionnalités non prises en charge</span><span class="sxs-lookup"><span data-stu-id="b0588-486">Unsupported features</span></span>

<span data-ttu-id="b0588-487">L’intergiciel intégré à ASP.NET Core 2.x ne prend pas en charge les fonctionnalités de module de réécriture d’URL IIS suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="b0588-488">Règles de trafic sortant</span><span class="sxs-lookup"><span data-stu-id="b0588-488">Outbound Rules</span></span>
* <span data-ttu-id="b0588-489">Variables serveur personnalisées</span><span class="sxs-lookup"><span data-stu-id="b0588-489">Custom Server Variables</span></span>
* <span data-ttu-id="b0588-490">Caractères génériques</span><span class="sxs-lookup"><span data-stu-id="b0588-490">Wildcards</span></span>
* <span data-ttu-id="b0588-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="b0588-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="b0588-492">Variables de serveur prises en charge</span><span class="sxs-lookup"><span data-stu-id="b0588-492">Supported server variables</span></span>

<span data-ttu-id="b0588-493">L’intergiciel prend en charge les variables serveur du module de réécriture d’URL IIS suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="b0588-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="b0588-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="b0588-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="b0588-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="b0588-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b0588-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b0588-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b0588-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b0588-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b0588-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="b0588-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b0588-499">HTTP_HOST</span></span>
* <span data-ttu-id="b0588-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b0588-500">HTTP_REFERER</span></span>
* <span data-ttu-id="b0588-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="b0588-501">HTTP_URL</span></span>
* <span data-ttu-id="b0588-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b0588-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b0588-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b0588-503">HTTPS</span></span>
* <span data-ttu-id="b0588-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="b0588-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b0588-505">QUERY_STRING</span></span>
* <span data-ttu-id="b0588-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-507">REMOTE_PORT</span></span>
* <span data-ttu-id="b0588-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b0588-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b0588-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-510">Vous pouvez également obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> par le biais d’un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b0588-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="b0588-511">Cette approche peut fournir davantage de flexibilité pour l’emplacement de vos fichiers de règles de réécriture.</span><span class="sxs-lookup"><span data-stu-id="b0588-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="b0588-512">Vérifiez que vos fichiers de règles de réécriture sont déployés sur le serveur dans le chemin que vous fournissez.</span><span class="sxs-lookup"><span data-stu-id="b0588-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="b0588-513">Règle basée sur une méthode</span><span class="sxs-lookup"><span data-stu-id="b0588-513">Method-based rule</span></span>

<span data-ttu-id="b0588-514">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour implémenter votre propre logique de règle dans une méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="b0588-515">`Add` expose <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, ce qui rend <xref:Microsoft.AspNetCore.Http.HttpContext> disponible pour une utilisation dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="b0588-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) détermine la façon dont le traitement du pipeline supplémentaire est géré.</span><span class="sxs-lookup"><span data-stu-id="b0588-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="b0588-517">Définissez la valeur sur un des champs <xref:Microsoft.AspNetCore.Rewrite.RuleResult> décrits dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b0588-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="b0588-518">Action</span><span class="sxs-lookup"><span data-stu-id="b0588-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="b0588-519">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-520">'Blazor'</span></span>
- <span data-ttu-id="b0588-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-521">'Identity'</span></span>
- <span data-ttu-id="b0588-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-523">'Razor'</span></span>
- <span data-ttu-id="b0588-524">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-525">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-526">'Blazor'</span></span>
- <span data-ttu-id="b0588-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-527">'Identity'</span></span>
- <span data-ttu-id="b0588-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-529">'Razor'</span></span>
- <span data-ttu-id="b0588-530">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-531">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-532">'Blazor'</span></span>
- <span data-ttu-id="b0588-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-533">'Identity'</span></span>
- <span data-ttu-id="b0588-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-535">'Razor'</span></span>
- <span data-ttu-id="b0588-536">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-537">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-538">'Blazor'</span></span>
- <span data-ttu-id="b0588-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-539">'Identity'</span></span>
- <span data-ttu-id="b0588-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-541">'Razor'</span></span>
- <span data-ttu-id="b0588-542">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-543">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-544">'Blazor'</span></span>
- <span data-ttu-id="b0588-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-545">'Identity'</span></span>
- <span data-ttu-id="b0588-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-547">'Razor'</span></span>
- <span data-ttu-id="b0588-548">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-549">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-550">'Blazor'</span></span>
- <span data-ttu-id="b0588-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-551">'Identity'</span></span>
- <span data-ttu-id="b0588-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-553">'Razor'</span></span>
- <span data-ttu-id="b0588-554">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-555">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-556">'Blazor'</span></span>
- <span data-ttu-id="b0588-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-557">'Identity'</span></span>
- <span data-ttu-id="b0588-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-559">'Razor'</span></span>
- <span data-ttu-id="b0588-560">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-561">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-562">'Blazor'</span></span>
- <span data-ttu-id="b0588-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-563">'Identity'</span></span>
- <span data-ttu-id="b0588-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-565">'Razor'</span></span>
- <span data-ttu-id="b0588-566">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-567">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-568">'Blazor'</span></span>
- <span data-ttu-id="b0588-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-569">'Identity'</span></span>
- <span data-ttu-id="b0588-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-571">'Razor'</span></span>
- <span data-ttu-id="b0588-572">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-573">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-574">'Blazor'</span></span>
- <span data-ttu-id="b0588-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-575">'Identity'</span></span>
- <span data-ttu-id="b0588-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-577">'Razor'</span></span>
- <span data-ttu-id="b0588-578">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-579">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-580">'Blazor'</span></span>
- <span data-ttu-id="b0588-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-581">'Identity'</span></span>
- <span data-ttu-id="b0588-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-583">'Razor'</span></span>
- <span data-ttu-id="b0588-584">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-585">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-586">'Blazor'</span></span>
- <span data-ttu-id="b0588-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-587">'Identity'</span></span>
- <span data-ttu-id="b0588-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-589">'Razor'</span></span>
- <span data-ttu-id="b0588-590">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-591">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-592">'Blazor'</span></span>
- <span data-ttu-id="b0588-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-593">'Identity'</span></span>
- <span data-ttu-id="b0588-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-595">'Razor'</span></span>
- <span data-ttu-id="b0588-596">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-597">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-598">'Blazor'</span></span>
- <span data-ttu-id="b0588-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-599">'Identity'</span></span>
- <span data-ttu-id="b0588-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-601">'Razor'</span></span>
- <span data-ttu-id="b0588-602">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-603">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-604">'Blazor'</span></span>
- <span data-ttu-id="b0588-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-605">'Identity'</span></span>
- <span data-ttu-id="b0588-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-607">'Razor'</span></span>
- <span data-ttu-id="b0588-608">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-609">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-610">'Blazor'</span></span>
- <span data-ttu-id="b0588-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-611">'Identity'</span></span>
- <span data-ttu-id="b0588-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-613">'Razor'</span></span>
- <span data-ttu-id="b0588-614">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-614">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-615">------------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-616">'Blazor'</span></span>
- <span data-ttu-id="b0588-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-617">'Identity'</span></span>
- <span data-ttu-id="b0588-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-619">'Razor'</span></span>
- <span data-ttu-id="b0588-620">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-621">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-622">'Blazor'</span></span>
- <span data-ttu-id="b0588-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-623">'Identity'</span></span>
- <span data-ttu-id="b0588-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-625">'Razor'</span></span>
- <span data-ttu-id="b0588-626">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-627">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-628">'Blazor'</span></span>
- <span data-ttu-id="b0588-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-629">'Identity'</span></span>
- <span data-ttu-id="b0588-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-631">'Razor'</span></span>
- <span data-ttu-id="b0588-632">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-633">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-634">'Blazor'</span></span>
- <span data-ttu-id="b0588-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-635">'Identity'</span></span>
- <span data-ttu-id="b0588-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-637">'Razor'</span></span>
- <span data-ttu-id="b0588-638">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-639">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-640">'Blazor'</span></span>
- <span data-ttu-id="b0588-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-641">'Identity'</span></span>
- <span data-ttu-id="b0588-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-643">'Razor'</span></span>
- <span data-ttu-id="b0588-644">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-645">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-646">'Blazor'</span></span>
- <span data-ttu-id="b0588-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-647">'Identity'</span></span>
- <span data-ttu-id="b0588-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-649">'Razor'</span></span>
- <span data-ttu-id="b0588-650">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-651">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-652">'Blazor'</span></span>
- <span data-ttu-id="b0588-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-653">'Identity'</span></span>
- <span data-ttu-id="b0588-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-655">'Razor'</span></span>
- <span data-ttu-id="b0588-656">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-657">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-658">'Blazor'</span></span>
- <span data-ttu-id="b0588-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-659">'Identity'</span></span>
- <span data-ttu-id="b0588-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-661">'Razor'</span></span>
- <span data-ttu-id="b0588-662">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-663">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-664">'Blazor'</span></span>
- <span data-ttu-id="b0588-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-665">'Identity'</span></span>
- <span data-ttu-id="b0588-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-667">'Razor'</span></span>
- <span data-ttu-id="b0588-668">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-669">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-670">'Blazor'</span></span>
- <span data-ttu-id="b0588-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-671">'Identity'</span></span>
- <span data-ttu-id="b0588-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-673">'Razor'</span></span>
- <span data-ttu-id="b0588-674">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-675">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-676">'Blazor'</span></span>
- <span data-ttu-id="b0588-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-677">'Identity'</span></span>
- <span data-ttu-id="b0588-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-679">'Razor'</span></span>
- <span data-ttu-id="b0588-680">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-681">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-682">'Blazor'</span></span>
- <span data-ttu-id="b0588-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-683">'Identity'</span></span>
- <span data-ttu-id="b0588-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-685">'Razor'</span></span>
- <span data-ttu-id="b0588-686">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-687">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-688">'Blazor'</span></span>
- <span data-ttu-id="b0588-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-689">'Identity'</span></span>
- <span data-ttu-id="b0588-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-691">'Razor'</span></span>
- <span data-ttu-id="b0588-692">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-693">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-694">'Blazor'</span></span>
- <span data-ttu-id="b0588-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-695">'Identity'</span></span>
- <span data-ttu-id="b0588-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-697">'Razor'</span></span>
- <span data-ttu-id="b0588-698">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-699">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-700">'Blazor'</span></span>
- <span data-ttu-id="b0588-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-701">'Identity'</span></span>
- <span data-ttu-id="b0588-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-703">'Razor'</span></span>
- <span data-ttu-id="b0588-704">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-705">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-706">'Blazor'</span></span>
- <span data-ttu-id="b0588-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-707">'Identity'</span></span>
- <span data-ttu-id="b0588-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-709">'Razor'</span></span>
- <span data-ttu-id="b0588-710">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-711">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-712">'Blazor'</span></span>
- <span data-ttu-id="b0588-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-713">'Identity'</span></span>
- <span data-ttu-id="b0588-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-715">'Razor'</span></span>
- <span data-ttu-id="b0588-716">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-717">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-718">'Blazor'</span></span>
- <span data-ttu-id="b0588-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-719">'Identity'</span></span>
- <span data-ttu-id="b0588-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-721">'Razor'</span></span>
- <span data-ttu-id="b0588-722">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-723">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-724">'Blazor'</span></span>
- <span data-ttu-id="b0588-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-725">'Identity'</span></span>
- <span data-ttu-id="b0588-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-727">'Razor'</span></span>
- <span data-ttu-id="b0588-728">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-729">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-730">'Blazor'</span></span>
- <span data-ttu-id="b0588-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-731">'Identity'</span></span>
- <span data-ttu-id="b0588-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-733">'Razor'</span></span>
- <span data-ttu-id="b0588-734">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-735">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-736">'Blazor'</span></span>
- <span data-ttu-id="b0588-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-737">'Identity'</span></span>
- <span data-ttu-id="b0588-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-739">'Razor'</span></span>
- <span data-ttu-id="b0588-740">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-741">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-742">'Blazor'</span></span>
- <span data-ttu-id="b0588-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-743">'Identity'</span></span>
- <span data-ttu-id="b0588-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-745">'Razor'</span></span>
- <span data-ttu-id="b0588-746">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-747">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-748">'Blazor'</span></span>
- <span data-ttu-id="b0588-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-749">'Identity'</span></span>
- <span data-ttu-id="b0588-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-751">'Razor'</span></span>
- <span data-ttu-id="b0588-752">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-753">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-754">'Blazor'</span></span>
- <span data-ttu-id="b0588-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-755">'Identity'</span></span>
- <span data-ttu-id="b0588-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-757">'Razor'</span></span>
- <span data-ttu-id="b0588-758">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-759">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-760">'Blazor'</span></span>
- <span data-ttu-id="b0588-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-761">'Identity'</span></span>
- <span data-ttu-id="b0588-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-763">'Razor'</span></span>
- <span data-ttu-id="b0588-764">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-765">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-766">'Blazor'</span></span>
- <span data-ttu-id="b0588-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-767">'Identity'</span></span>
- <span data-ttu-id="b0588-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-769">'Razor'</span></span>
- <span data-ttu-id="b0588-770">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-771">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-772">'Blazor'</span></span>
- <span data-ttu-id="b0588-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-773">'Identity'</span></span>
- <span data-ttu-id="b0588-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-775">'Razor'</span></span>
- <span data-ttu-id="b0588-776">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-777">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-778">'Blazor'</span></span>
- <span data-ttu-id="b0588-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-779">'Identity'</span></span>
- <span data-ttu-id="b0588-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-781">'Razor'</span></span>
- <span data-ttu-id="b0588-782">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-783">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-784">'Blazor'</span></span>
- <span data-ttu-id="b0588-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-785">'Identity'</span></span>
- <span data-ttu-id="b0588-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-787">'Razor'</span></span>
- <span data-ttu-id="b0588-788">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-789">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-790">'Blazor'</span></span>
- <span data-ttu-id="b0588-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-791">'Identity'</span></span>
- <span data-ttu-id="b0588-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-793">'Razor'</span></span>
- <span data-ttu-id="b0588-794">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-794">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-795">-------------------------------- | | `RuleResult.ContinueRules`(par défaut) | Continuez à appliquer les règles.</span><span class="sxs-lookup"><span data-stu-id="b0588-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="b0588-796">| | `RuleResult.EndResponse`             | Arrêtez l’application des règles et envoyez la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="b0588-797">| | `RuleResult.SkipRemainingRules`      | Arrête l’application des règles et envoie le contexte à l’intergiciel (middleware) suivant.</span><span class="sxs-lookup"><span data-stu-id="b0588-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="b0588-798">L’exemple d’application présente une méthode qui redirige les requêtes de chemins qui se terminent par *.xml*.</span><span class="sxs-lookup"><span data-stu-id="b0588-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="b0588-799">Si une requête est faite pour `/file.xml`, la requête est redirigée vers `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="b0588-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="b0588-800">Le code d’état est défini sur *301 - Déplacé de façon permanente*.</span><span class="sxs-lookup"><span data-stu-id="b0588-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="b0588-801">Quand le navigateur fait une nouvelle requête pour */xmlfiles/file.xml*, le middleware de fichiers statiques délivre le fichier au client à partir du dossier *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="b0588-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="b0588-802">Pour une redirection, définissez explicitement le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="b0588-803">Sinon, un code d’état *200 - OK* est retourné et la redirection ne se produit pas sur le client.</span><span class="sxs-lookup"><span data-stu-id="b0588-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="b0588-804">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="b0588-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="b0588-805">Cette approche peut également réécrire des requêtes.</span><span class="sxs-lookup"><span data-stu-id="b0588-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="b0588-806">L’exemple d’application montre la réécriture du chemin pour toute requête demandant de délivrer le fichier texte *file.txt* à partir du dossier *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b0588-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="b0588-807">Le middleware de fichiers statiques délivre le fichier en fonction du chemin de requête mis à jour :</span><span class="sxs-lookup"><span data-stu-id="b0588-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="b0588-808">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="b0588-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="b0588-809">Règle basée sur IRule</span><span class="sxs-lookup"><span data-stu-id="b0588-809">IRule-based rule</span></span>

<span data-ttu-id="b0588-810">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour insérer votre propre logique de règle dans une classe qui implémente l’interface <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="b0588-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="b0588-811">`IRule` offre davantage de flexibilité par rapport à l’approche de la règle basée sur une méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="b0588-812">Votre classe d’implémentation peut inclure un constructeur qui vous permet de passer des paramètres pour la méthode <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="b0588-813">Les valeurs des paramètres dans l’exemple d’application pour `extension` et `newPath` sont vérifiées afin de remplir plusieurs conditions.</span><span class="sxs-lookup"><span data-stu-id="b0588-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="b0588-814">`extension` doit contenir une valeur, laquelle doit être *.png*, *.jpg* ou *.gif*.</span><span class="sxs-lookup"><span data-stu-id="b0588-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="b0588-815">Si `newPath` n’est pas valide, un <xref:System.ArgumentException> est levé.</span><span class="sxs-lookup"><span data-stu-id="b0588-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="b0588-816">Si une requête est faite pour *image.png*, la requête est redirigée vers `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="b0588-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="b0588-817">Si une requête est faite pour *image.jpg*, la requête est redirigée vers `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="b0588-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="b0588-818">Le code d’état est défini sur *301 - Déplacé de façon permanente*, et `context.Result` est défini de façon à cesser le traitement des règles et envoyer la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="b0588-819">Requête d’origine : `/image.png`</span><span class="sxs-lookup"><span data-stu-id="b0588-819">Original Request: `/image.png`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="b0588-821">Requête d’origine : `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="b0588-821">Original Request: `/image.jpg`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="b0588-823">Exemples d’expressions régulières</span><span class="sxs-lookup"><span data-stu-id="b0588-823">Regex examples</span></span>

| <span data-ttu-id="b0588-824">Objectif</span><span class="sxs-lookup"><span data-stu-id="b0588-824">Goal</span></span> | <span data-ttu-id="b0588-825">Chaîne d’expression régulière et</span><span class="sxs-lookup"><span data-stu-id="b0588-825">Regex String &</span></span><br><span data-ttu-id="b0588-826">exemple de correspondance</span><span class="sxs-lookup"><span data-stu-id="b0588-826">Match Example</span></span> | <span data-ttu-id="b0588-827">Chaîne de remplacement et</span><span class="sxs-lookup"><span data-stu-id="b0588-827">Replacement String &</span></span><br><span data-ttu-id="b0588-828">exemple de sortie</span><span class="sxs-lookup"><span data-stu-id="b0588-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="b0588-829">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-830">'Blazor'</span></span>
- <span data-ttu-id="b0588-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-831">'Identity'</span></span>
- <span data-ttu-id="b0588-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-833">'Razor'</span></span>
- <span data-ttu-id="b0588-834">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-835">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-836">'Blazor'</span></span>
- <span data-ttu-id="b0588-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-837">'Identity'</span></span>
- <span data-ttu-id="b0588-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-839">'Razor'</span></span>
- <span data-ttu-id="b0588-840">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-841">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-842">'Blazor'</span></span>
- <span data-ttu-id="b0588-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-843">'Identity'</span></span>
- <span data-ttu-id="b0588-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-845">'Razor'</span></span>
- <span data-ttu-id="b0588-846">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-847">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-848">'Blazor'</span></span>
- <span data-ttu-id="b0588-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-849">'Identity'</span></span>
- <span data-ttu-id="b0588-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-851">'Razor'</span></span>
- <span data-ttu-id="b0588-852">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-853">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-854">'Blazor'</span></span>
- <span data-ttu-id="b0588-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-855">'Identity'</span></span>
- <span data-ttu-id="b0588-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-857">'Razor'</span></span>
- <span data-ttu-id="b0588-858">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-859">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-860">'Blazor'</span></span>
- <span data-ttu-id="b0588-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-861">'Identity'</span></span>
- <span data-ttu-id="b0588-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-863">'Razor'</span></span>
- <span data-ttu-id="b0588-864">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-865">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-866">'Blazor'</span></span>
- <span data-ttu-id="b0588-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-867">'Identity'</span></span>
- <span data-ttu-id="b0588-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-869">'Razor'</span></span>
- <span data-ttu-id="b0588-870">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-871">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-872">'Blazor'</span></span>
- <span data-ttu-id="b0588-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-873">'Identity'</span></span>
- <span data-ttu-id="b0588-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-875">'Razor'</span></span>
- <span data-ttu-id="b0588-876">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-877">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-878">'Blazor'</span></span>
- <span data-ttu-id="b0588-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-879">'Identity'</span></span>
- <span data-ttu-id="b0588-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-881">'Razor'</span></span>
- <span data-ttu-id="b0588-882">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-883">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-884">'Blazor'</span></span>
- <span data-ttu-id="b0588-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-885">'Identity'</span></span>
- <span data-ttu-id="b0588-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-887">'Razor'</span></span>
- <span data-ttu-id="b0588-888">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-889">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-890">'Blazor'</span></span>
- <span data-ttu-id="b0588-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-891">'Identity'</span></span>
- <span data-ttu-id="b0588-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-893">'Razor'</span></span>
- <span data-ttu-id="b0588-894">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-895">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-896">'Blazor'</span></span>
- <span data-ttu-id="b0588-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-897">'Identity'</span></span>
- <span data-ttu-id="b0588-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-899">'Razor'</span></span>
- <span data-ttu-id="b0588-900">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-901">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-902">'Blazor'</span></span>
- <span data-ttu-id="b0588-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-903">'Identity'</span></span>
- <span data-ttu-id="b0588-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-905">'Razor'</span></span>
- <span data-ttu-id="b0588-906">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-906">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-907">---------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-908">'Blazor'</span></span>
- <span data-ttu-id="b0588-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-909">'Identity'</span></span>
- <span data-ttu-id="b0588-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-911">'Razor'</span></span>
- <span data-ttu-id="b0588-912">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-913">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-914">'Blazor'</span></span>
- <span data-ttu-id="b0588-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-915">'Identity'</span></span>
- <span data-ttu-id="b0588-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-917">'Razor'</span></span>
- <span data-ttu-id="b0588-918">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-919">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-920">'Blazor'</span></span>
- <span data-ttu-id="b0588-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-921">'Identity'</span></span>
- <span data-ttu-id="b0588-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-923">'Razor'</span></span>
- <span data-ttu-id="b0588-924">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-925">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-926">'Blazor'</span></span>
- <span data-ttu-id="b0588-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-927">'Identity'</span></span>
- <span data-ttu-id="b0588-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-929">'Razor'</span></span>
- <span data-ttu-id="b0588-930">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-931">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-932">'Blazor'</span></span>
- <span data-ttu-id="b0588-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-933">'Identity'</span></span>
- <span data-ttu-id="b0588-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-935">'Razor'</span></span>
- <span data-ttu-id="b0588-936">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-937">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-938">'Blazor'</span></span>
- <span data-ttu-id="b0588-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-939">'Identity'</span></span>
- <span data-ttu-id="b0588-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-941">'Razor'</span></span>
- <span data-ttu-id="b0588-942">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-943">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-944">'Blazor'</span></span>
- <span data-ttu-id="b0588-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-945">'Identity'</span></span>
- <span data-ttu-id="b0588-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-947">'Razor'</span></span>
- <span data-ttu-id="b0588-948">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-949">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-950">'Blazor'</span></span>
- <span data-ttu-id="b0588-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-951">'Identity'</span></span>
- <span data-ttu-id="b0588-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-953">'Razor'</span></span>
- <span data-ttu-id="b0588-954">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-955">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-956">'Blazor'</span></span>
- <span data-ttu-id="b0588-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-957">'Identity'</span></span>
- <span data-ttu-id="b0588-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-959">'Razor'</span></span>
- <span data-ttu-id="b0588-960">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-961">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-962">'Blazor'</span></span>
- <span data-ttu-id="b0588-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-963">'Identity'</span></span>
- <span data-ttu-id="b0588-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-965">'Razor'</span></span>
- <span data-ttu-id="b0588-966">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-967">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-968">'Blazor'</span></span>
- <span data-ttu-id="b0588-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-969">'Identity'</span></span>
- <span data-ttu-id="b0588-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-971">'Razor'</span></span>
- <span data-ttu-id="b0588-972">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-973">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-974">'Blazor'</span></span>
- <span data-ttu-id="b0588-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-975">'Identity'</span></span>
- <span data-ttu-id="b0588-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-977">'Razor'</span></span>
- <span data-ttu-id="b0588-978">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-979">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-980">'Blazor'</span></span>
- <span data-ttu-id="b0588-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-981">'Identity'</span></span>
- <span data-ttu-id="b0588-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-983">'Razor'</span></span>
- <span data-ttu-id="b0588-984">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-985">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-986">'Blazor'</span></span>
- <span data-ttu-id="b0588-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-987">'Identity'</span></span>
- <span data-ttu-id="b0588-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-989">'Razor'</span></span>
- <span data-ttu-id="b0588-990">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-991">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-992">'Blazor'</span></span>
- <span data-ttu-id="b0588-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-993">'Identity'</span></span>
- <span data-ttu-id="b0588-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-995">'Razor'</span></span>
- <span data-ttu-id="b0588-996">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-997">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-998">'Blazor'</span></span>
- <span data-ttu-id="b0588-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-999">'Identity'</span></span>
- <span data-ttu-id="b0588-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1001">'Razor'</span></span>
- <span data-ttu-id="b0588-1002">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1003">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1004">'Blazor'</span></span>
- <span data-ttu-id="b0588-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1005">'Identity'</span></span>
- <span data-ttu-id="b0588-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1007">'Razor'</span></span>
- <span data-ttu-id="b0588-1008">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1009">------------------- | | Réécrire le chemin dans QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="b0588-1010">`/path?var1=abc&var2=123`| | Barre oblique de fin de bande |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="b0588-1011">`/path`| | Appliquer la barre oblique de fin |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="b0588-1012">`/path/`| | Évitez de réécrire des requêtes spécifiques | `^(.*)(?<!\.axd)$`ou`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="b0588-1013">Oui : `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="b0588-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="b0588-1014">º`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="b0588-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="b0588-1015">`/resource.axd`| | Réorganiser les segments d’URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="b0588-1016">`path/3/2/1`| | Remplacer un segment d’URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0588-1017">Ce document présente la réécriture d’URL avec des instructions sur la façon d’utiliser l’intergiciel (middleware) de réécriture d’URL dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0588-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="b0588-1018">La réécriture d’URL consiste à modifier des URL de requête en fonction d’une ou de plusieurs règles prédéfinies.</span><span class="sxs-lookup"><span data-stu-id="b0588-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="b0588-1019">La réécriture d’URL crée une abstraction entre les emplacements des ressources et leurs adresses pour que les emplacements et les adresses ne soient pas étroitement liés.</span><span class="sxs-lookup"><span data-stu-id="b0588-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="b0588-1020">La réécriture d’URL est utile dans plusieurs scénarios pour :</span><span class="sxs-lookup"><span data-stu-id="b0588-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="b0588-1021">Déplacer ou remplacer de façon temporaire ou permanente les ressources d’un serveur, tout en conservant des localisateurs stables pour ces ressources.</span><span class="sxs-lookup"><span data-stu-id="b0588-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="b0588-1022">Diviser le traitement des requêtes entre différentes applications ou entre différentes parties d’une même application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="b0588-1023">Supprimer, ajouter ou réorganiser les segments d’URL sur des requêtes entrantes.</span><span class="sxs-lookup"><span data-stu-id="b0588-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="b0588-1024">Optimiser les URL publiques pour l’optimisation du référencement d’un site auprès d’un moteur de recherche (SEO).</span><span class="sxs-lookup"><span data-stu-id="b0588-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="b0588-1025">Permettre l’utilisation des URL publiques conviviales pour aider les visiteurs à prédire le contenu retourné en demandant une ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="b0588-1026">Rediriger des requêtes non sécurisées vers des points de terminaison sécurisés.</span><span class="sxs-lookup"><span data-stu-id="b0588-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="b0588-1027">Empêcher les liaisons à chaud, où un site externe utilise une ressource statique hébergée sur un autre site en liant la ressource dans son propre contenu.</span><span class="sxs-lookup"><span data-stu-id="b0588-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-1028">La réécriture d’URL peut réduire les performances d’une application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="b0588-1029">Quand c’est possible, limitez le nombre et la complexité des règles.</span><span class="sxs-lookup"><span data-stu-id="b0588-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="b0588-1030">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0588-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="b0588-1031">Redirection d’URL et réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="b0588-1032">La différence de formulation entre la *redirection d’URL* et la *réécriture d’URL* est subtile, mais elle a des implications importantes sur la fourniture de ressources aux clients.</span><span class="sxs-lookup"><span data-stu-id="b0588-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="b0588-1033">L’intergiciel de réécriture d’URL d’ASP.NET Core est capables de répondre aux besoins des deux.</span><span class="sxs-lookup"><span data-stu-id="b0588-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="b0588-1034">Une *redirection d’URL* implique une opération côté client, où le client est invité à accéder à une ressource à une autre adresse que celle demandée à l’origine.</span><span class="sxs-lookup"><span data-stu-id="b0588-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="b0588-1035">Ceci nécessite un aller-retour avec le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="b0588-1036">L’URL de redirection retournée au client s’affiche dans la barre d’adresse du navigateur quand le client effectue une nouvelle requête pour la ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="b0588-1037">Si `/resource` est *redirigée* vers `/different-resource`, le serveur répond que le client doit obtenir la ressource à l’emplacement `/different-resource` avec un code d’état indiquant que la redirection est temporaire ou permanente.</span><span class="sxs-lookup"><span data-stu-id="b0588-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Un point de terminaison de service WebAPI a été changé temporairement de la version 1 (v1) à la version 2 (v2) sur le serveur.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="b0588-1043">Lors de la redirection des requêtes vers une URL différente, indiquez si la redirection est permanente ou temporaire en spécifiant le code d’état avec la réponse :</span><span class="sxs-lookup"><span data-stu-id="b0588-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="b0588-1044">Le code d’état *301 - Déplacé de façon permanente* est utilisé quand la ressource a une nouvelle URL permanente et que vous voulez indiquer au client que toutes les requêtes futures pour la ressource doivent utiliser la nouvelle URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="b0588-1045">*Le client peut mettre en cache et réutiliser la réponse quand un code d’état 301 est reçu.*</span><span class="sxs-lookup"><span data-stu-id="b0588-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="b0588-1046">Le code d’état *302 - Trouvé* est utilisé quand la redirection est temporaire ou généralement susceptible d’être modifiée.</span><span class="sxs-lookup"><span data-stu-id="b0588-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="b0588-1047">Le code d’état 302 indique au client de ne pas stocker l’URL et de ne plus l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="b0588-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="b0588-1048">Pour plus d’informations sur les codes d’état, consultez [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="b0588-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="b0588-1049">Une *réécriture d’URL* est une opération côté serveur qui fournit une ressource à partir d’une adresse de ressource différente de celle demandée par le client.</span><span class="sxs-lookup"><span data-stu-id="b0588-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="b0588-1050">La réécriture d’URL ne nécessite pas d’aller-retour avec le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="b0588-1051">L’URL réécrite n’est pas retournée au client et n’apparaît pas dans la barre d’adresse du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b0588-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="b0588-1052">Si `/resource` est *réécrite* en `/different-resource`, le serveur récupère la ressource *en interne* à l’emplacement `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="b0588-1053">Même si le client peut récupérer la ressource à l’URL réécrite, il n’est pas informé que la ressource existe à l’URL réécrite quand il fait sa requête et reçoit la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Un point de terminaison de service WebAPI a été changé de la version 1 (v1) à la version 2 (v2) sur le serveur.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="b0588-1058">Exemple d’application de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1058">URL rewriting sample app</span></span>

<span data-ttu-id="b0588-1059">Vous pouvez explorer les fonctionnalités du middleware de réécriture d’URL avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="b0588-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="b0588-1060">L’application applique des règles de redirection et de réécriture, et montre l’URL redirigée ou réécrite pour plusieurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="b0588-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="b0588-1061">Quand utiliser l’intergiciel (middleware) de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="b0588-1062">Utilisez le middleware de réécriture d’URL quand vous ne pouvez pas utiliser les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="b0588-1063">Module de réécriture d’URL avec IIS sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="b0588-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="b0588-1064">Module mod_rewrite Apache sur Apache Server</span><span class="sxs-lookup"><span data-stu-id="b0588-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b0588-1065">URL de réécriture sur Nginx</span><span class="sxs-lookup"><span data-stu-id="b0588-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="b0588-1066">Utilisez aussi le middleware quand l’application est hébergée sur le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé WebListener).</span><span class="sxs-lookup"><span data-stu-id="b0588-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="b0588-1067">Les principales raisons d’utiliser les technologies de réécriture d’URL basée sur le serveur dans IIS, Apache et Nginx sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="b0588-1068">Le middleware ne prend pas en charge toutes les fonctionnalités de ces modules.</span><span class="sxs-lookup"><span data-stu-id="b0588-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="b0588-1069">Certaines des fonctionnalités des modules serveur ne fonctionnent pas avec les projets ASP.NET Core, comme les contraintes `IsFile` et `IsDirectory` du module Réécriture IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="b0588-1070">Dans ces scénarios, utilisez plutôt l’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="b0588-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="b0588-1071">Les performances du middleware ne correspondent probablement pas à celles des modules.</span><span class="sxs-lookup"><span data-stu-id="b0588-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="b0588-1072">Mener des tests de performances est la seule façon de savoir exactement quelle approche dégrade le plus les performances ou si la dégradation des performances est négligeable.</span><span class="sxs-lookup"><span data-stu-id="b0588-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="b0588-1073">Paquet</span><span class="sxs-lookup"><span data-stu-id="b0588-1073">Package</span></span>

<span data-ttu-id="b0588-1074">Pour inclure le middleware dans votre projet, ajoutez une référence de package au [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet, qui contient le package [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="b0588-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="b0588-1075">Quand vous n’utilisez pas le métapackage `Microsoft.AspNetCore.App`, ajoutez une référence de projet au package `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="b0588-1076">Extension et options</span><span class="sxs-lookup"><span data-stu-id="b0588-1076">Extension and options</span></span>

<span data-ttu-id="b0588-1077">Établissez des règles de réécriture et de redirection d’URL en créant une instance de la classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) avec des méthodes d’extension pour chacune de vos règles de réécriture.</span><span class="sxs-lookup"><span data-stu-id="b0588-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="b0588-1078">Chaînez plusieurs règles dans l’ordre dans lequel vous voulez qu’elles soient traitées.</span><span class="sxs-lookup"><span data-stu-id="b0588-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="b0588-1079">Les `RewriteOptions` sont passées dans le middleware de réécriture d’URL quand il est ajouté au pipeline de requête avec <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="b0588-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="b0588-1080">Redirection de demandes non-www en demandes www</span><span class="sxs-lookup"><span data-stu-id="b0588-1080">Redirect non-www to www</span></span>

<span data-ttu-id="b0588-1081">Trois options permettent à l’application de rediriger des demandes non-`www` en demandes `www` :</span><span class="sxs-lookup"><span data-stu-id="b0588-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="b0588-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Rediriger définitivement la demande vers le `www` sous-domaine si la demande n’est pas `www` .</span><span class="sxs-lookup"><span data-stu-id="b0588-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="b0588-1083">Redirige avec un code d’état [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="b0588-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="b0588-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirigez la demande vers le `www` sous-domaine si la demande entrante n’est pas `www` .</span><span class="sxs-lookup"><span data-stu-id="b0588-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="b0588-1085">Redirige avec un code d’état [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="b0588-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="b0588-1086">Une surcharge vous permet de fournir le code d’état pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="b0588-1087">Utilisez un champ de la classe <xref:Microsoft.AspNetCore.Http.StatusCodes> pour une affectation de code d’état.</span><span class="sxs-lookup"><span data-stu-id="b0588-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="b0588-1088">Redirection d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1088">URL redirect</span></span>

<span data-ttu-id="b0588-1089">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pour rediriger des requêtes.</span><span class="sxs-lookup"><span data-stu-id="b0588-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="b0588-1090">Le premier paramètre contient votre expression régulière pour la mise en correspondance sur le chemin de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="b0588-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="b0588-1091">Le deuxième paramètre est la chaîne de remplacement.</span><span class="sxs-lookup"><span data-stu-id="b0588-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="b0588-1092">Le troisième paramètre, le cas échéant, spécifie le code d’état.</span><span class="sxs-lookup"><span data-stu-id="b0588-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="b0588-1093">Si vous ne spécifiez pas le code d’état, sa valeur par défaut est *302 - Trouvé*, ce qui indique que la ressource est temporairement déplacée ou remplacée.</span><span class="sxs-lookup"><span data-stu-id="b0588-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="b0588-1094">Dans un navigateur dans lequel les outils de développement sont activés, effectuez une requête à l’exemple d’application avec le chemin `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="b0588-1095">L’expression régulière établit une correspondance avec le chemin de la requête sur `redirect-rule/(.*)`, et le chemin est remplacé par `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="b0588-1096">L’URL de redirection est renvoyée au client avec le code d’état *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="b0588-1097">Le navigateur effectue une nouvelle requête à l’URL de redirection, qui apparaît dans la barre d’adresse du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b0588-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="b0588-1098">Comme aucune règle de l’exemple d’application ne correspond sur l’URL de redirection :</span><span class="sxs-lookup"><span data-stu-id="b0588-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="b0588-1099">La deuxième requête reçoit une réponse *200 - OK* de l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="b0588-1100">Le corps de la réponse montre l’URL de redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="b0588-1101">Un aller-retour est effectué avec le serveur quand une URL est *redirigée*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="b0588-1102">Soyez prudent lors de l’établissement de règles de redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="b0588-1103">Les règles de redirection sont évaluées à chaque requête effectuée à l’application, notamment après une redirection.</span><span class="sxs-lookup"><span data-stu-id="b0588-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="b0588-1104">Il est facile de créer accidentellement une *boucle de redirections infinies*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="b0588-1105">Requête d’origine : `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b0588-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="b0588-1107">La partie de l’expression entre parenthèses est appelée *groupe de capture*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="b0588-1108">Le point (`.`) de l’expression signifie *mettre en correspondance n’importe quel caractère*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="b0588-1109">L’astérisque (`*`) indique *mettre en correspondance zéro occurrence ou plus du caractère précédent*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="b0588-1110">Par conséquent, les deux derniers segments de chemin de l’URL, `1234/5678`, sont capturés par le groupe de capture `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="b0588-1111">Toute valeur fournie dans l’URL de la requête après `redirect-rule/` est capturée par ce groupe de capture unique.</span><span class="sxs-lookup"><span data-stu-id="b0588-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="b0588-1112">Dans la chaîne de remplacement, les groupes capturés sont injectés dans la chaîne avec le signe dollar (`$`) suivi du numéro de séquence de la capture.</span><span class="sxs-lookup"><span data-stu-id="b0588-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="b0588-1113">La valeur du premier groupe de capture est obtenue avec `$1`, la deuxième avec `$2`, et ainsi de suite en séquence pour les groupes de capture de votre expression régulière.</span><span class="sxs-lookup"><span data-stu-id="b0588-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="b0588-1114">Comme il n’y a qu’un seul groupe capturé dans l’expression régulière de la règle de redirection de l’exemple d’application, un seul groupe est injecté dans la chaîne de remplacement, à savoir `$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="b0588-1115">Quand la règle est appliquée, l’URL devient `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="b0588-1116">Redirection d’URL vers un point de terminaison sécurisé</span><span class="sxs-lookup"><span data-stu-id="b0588-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="b0588-1117">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pour rediriger les requêtes HTTP vers le même hôte et le même chemin avec le protocole HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0588-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="b0588-1118">Si le code d’état n’est pas fourni, le middleware utilise par défaut *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="b0588-1119">Si le port n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="b0588-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="b0588-1120">Le middleware utilise par défaut `null`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="b0588-1121">Le schéma change en `https` (protocole HTTPS), et le client accède à la ressource sur le port 443.</span><span class="sxs-lookup"><span data-stu-id="b0588-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="b0588-1122">L’exemple suivant montre comment définir le code d’état sur *301 - Déplacé de façon permanente* et changer le port en 5001.</span><span class="sxs-lookup"><span data-stu-id="b0588-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="b0588-1123">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> pour rediriger les requêtes non sécurisées vers le même hôte et le même chemin avec le protocole HTTPS sécurisé sur le port 443.</span><span class="sxs-lookup"><span data-stu-id="b0588-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="b0588-1124">Le middleware définit le code d’état sur *301 - Déplacé de façon permanente*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="b0588-1125">Si vous effectuez une redirection vers un point de terminaison sécurisé et que vous n’avez pas besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser le middleware de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0588-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b0588-1126">Pour plus d’informations, consultez la rubrique [Appliquer HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="b0588-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="b0588-1127">L’exemple d’application peut montrer comment utiliser `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="b0588-1128">Ajoutez la méthode d’extension à `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="b0588-1129">Effectuez une requête non sécurisée à l’application à n’importe quelle URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="b0588-1130">Ignorez l’avertissement de sécurité du navigateur indiquant que le certificat auto-signé n’est pas approuvé ou créez une exception pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="b0588-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="b0588-1131">Requête d’origine utilisant `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="b0588-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="b0588-1133">Requête d’origine utilisant `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="b0588-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="b0588-1135">Réécrire URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1135">URL rewrite</span></span>

<span data-ttu-id="b0588-1136">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> pour créer une règle pour la réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="b0588-1137">Le premier paramètre contient l’expression régulière pour la mise en correspondance sur le chemin de l’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="b0588-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="b0588-1138">Le deuxième paramètre est la chaîne de remplacement.</span><span class="sxs-lookup"><span data-stu-id="b0588-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="b0588-1139">Le troisième paramètre, `skipRemainingRules: {true|false}`, indique à l’intergiciel d’ignorer, ou non, les règles de réécriture supplémentaires si la règle actuelle est appliquée.</span><span class="sxs-lookup"><span data-stu-id="b0588-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="b0588-1140">Requête d’origine : `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b0588-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="b0588-1142">Le caret (`^`) au début de l’expression signifie que la correspondance commence au début du chemin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="b0588-1143">Dans l’exemple précédent avec la règle de redirection, `redirect-rule/(.*)`, il n’existe pas de caret (`^`) au début de l’expression régulière.</span><span class="sxs-lookup"><span data-stu-id="b0588-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="b0588-1144">Ainsi, n’importe quel caractère peut précéder `redirect-rule/` dans le chemin pour qu’une correspondance soit établie.</span><span class="sxs-lookup"><span data-stu-id="b0588-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="b0588-1145">Path</span><span class="sxs-lookup"><span data-stu-id="b0588-1145">Path</span></span>                               | <span data-ttu-id="b0588-1146">Correspond</span><span class="sxs-lookup"><span data-stu-id="b0588-1146">Match</span></span> |
| ---
<span data-ttu-id="b0588-1147">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1148">'Blazor'</span></span>
- <span data-ttu-id="b0588-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1149">'Identity'</span></span>
- <span data-ttu-id="b0588-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1151">'Razor'</span></span>
- <span data-ttu-id="b0588-1152">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1153">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1154">'Blazor'</span></span>
- <span data-ttu-id="b0588-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1155">'Identity'</span></span>
- <span data-ttu-id="b0588-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1157">'Razor'</span></span>
- <span data-ttu-id="b0588-1158">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1159">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1160">'Blazor'</span></span>
- <span data-ttu-id="b0588-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1161">'Identity'</span></span>
- <span data-ttu-id="b0588-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1163">'Razor'</span></span>
- <span data-ttu-id="b0588-1164">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1165">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1166">'Blazor'</span></span>
- <span data-ttu-id="b0588-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1167">'Identity'</span></span>
- <span data-ttu-id="b0588-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1169">'Razor'</span></span>
- <span data-ttu-id="b0588-1170">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1171">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1172">'Blazor'</span></span>
- <span data-ttu-id="b0588-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1173">'Identity'</span></span>
- <span data-ttu-id="b0588-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1175">'Razor'</span></span>
- <span data-ttu-id="b0588-1176">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1177">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1178">'Blazor'</span></span>
- <span data-ttu-id="b0588-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1179">'Identity'</span></span>
- <span data-ttu-id="b0588-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1181">'Razor'</span></span>
- <span data-ttu-id="b0588-1182">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1183">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1184">'Blazor'</span></span>
- <span data-ttu-id="b0588-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1185">'Identity'</span></span>
- <span data-ttu-id="b0588-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1187">'Razor'</span></span>
- <span data-ttu-id="b0588-1188">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1189">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1190">'Blazor'</span></span>
- <span data-ttu-id="b0588-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1191">'Identity'</span></span>
- <span data-ttu-id="b0588-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1193">'Razor'</span></span>
- <span data-ttu-id="b0588-1194">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1195">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1196">'Blazor'</span></span>
- <span data-ttu-id="b0588-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1197">'Identity'</span></span>
- <span data-ttu-id="b0588-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1199">'Razor'</span></span>
- <span data-ttu-id="b0588-1200">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1201">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1202">'Blazor'</span></span>
- <span data-ttu-id="b0588-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1203">'Identity'</span></span>
- <span data-ttu-id="b0588-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1205">'Razor'</span></span>
- <span data-ttu-id="b0588-1206">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1207">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1208">'Blazor'</span></span>
- <span data-ttu-id="b0588-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1209">'Identity'</span></span>
- <span data-ttu-id="b0588-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1211">'Razor'</span></span>
- <span data-ttu-id="b0588-1212">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1213">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1214">'Blazor'</span></span>
- <span data-ttu-id="b0588-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1215">'Identity'</span></span>
- <span data-ttu-id="b0588-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1217">'Razor'</span></span>
- <span data-ttu-id="b0588-1218">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1219">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1220">'Blazor'</span></span>
- <span data-ttu-id="b0588-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1221">'Identity'</span></span>
- <span data-ttu-id="b0588-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1223">'Razor'</span></span>
- <span data-ttu-id="b0588-1224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1225">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1226">'Blazor'</span></span>
- <span data-ttu-id="b0588-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1227">'Identity'</span></span>
- <span data-ttu-id="b0588-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1229">'Razor'</span></span>
- <span data-ttu-id="b0588-1230">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1231">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1232">'Blazor'</span></span>
- <span data-ttu-id="b0588-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1233">'Identity'</span></span>
- <span data-ttu-id="b0588-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1235">'Razor'</span></span>
- <span data-ttu-id="b0588-1236">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Oui | | `/my-cool-redirect-rule/1234/5678` | Oui | | `/anotherredirect-rule/1234/5678`  | Oui |</span><span class="sxs-lookup"><span data-stu-id="b0588-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="b0588-1238">La règle de réécriture, `^rewrite-rule/(\d+)/(\d+)`, établit une correspondance uniquement avec des chemins d’accès s’ils commencent par `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="b0588-1239">Dans le tableau suivant, notez la différence de correspondance.</span><span class="sxs-lookup"><span data-stu-id="b0588-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="b0588-1240">Path</span><span class="sxs-lookup"><span data-stu-id="b0588-1240">Path</span></span>                              | <span data-ttu-id="b0588-1241">Correspond</span><span class="sxs-lookup"><span data-stu-id="b0588-1241">Match</span></span> |
| ---
<span data-ttu-id="b0588-1242">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1243">'Blazor'</span></span>
- <span data-ttu-id="b0588-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1244">'Identity'</span></span>
- <span data-ttu-id="b0588-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1246">'Razor'</span></span>
- <span data-ttu-id="b0588-1247">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1248">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1249">'Blazor'</span></span>
- <span data-ttu-id="b0588-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1250">'Identity'</span></span>
- <span data-ttu-id="b0588-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1252">'Razor'</span></span>
- <span data-ttu-id="b0588-1253">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1254">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1255">'Blazor'</span></span>
- <span data-ttu-id="b0588-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1256">'Identity'</span></span>
- <span data-ttu-id="b0588-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1258">'Razor'</span></span>
- <span data-ttu-id="b0588-1259">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1260">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1261">'Blazor'</span></span>
- <span data-ttu-id="b0588-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1262">'Identity'</span></span>
- <span data-ttu-id="b0588-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1264">'Razor'</span></span>
- <span data-ttu-id="b0588-1265">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1266">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1267">'Blazor'</span></span>
- <span data-ttu-id="b0588-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1268">'Identity'</span></span>
- <span data-ttu-id="b0588-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1270">'Razor'</span></span>
- <span data-ttu-id="b0588-1271">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1272">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1273">'Blazor'</span></span>
- <span data-ttu-id="b0588-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1274">'Identity'</span></span>
- <span data-ttu-id="b0588-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1276">'Razor'</span></span>
- <span data-ttu-id="b0588-1277">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1278">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1279">'Blazor'</span></span>
- <span data-ttu-id="b0588-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1280">'Identity'</span></span>
- <span data-ttu-id="b0588-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1282">'Razor'</span></span>
- <span data-ttu-id="b0588-1283">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1284">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1285">'Blazor'</span></span>
- <span data-ttu-id="b0588-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1286">'Identity'</span></span>
- <span data-ttu-id="b0588-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1288">'Razor'</span></span>
- <span data-ttu-id="b0588-1289">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1290">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1291">'Blazor'</span></span>
- <span data-ttu-id="b0588-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1292">'Identity'</span></span>
- <span data-ttu-id="b0588-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1294">'Razor'</span></span>
- <span data-ttu-id="b0588-1295">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1296">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1297">'Blazor'</span></span>
- <span data-ttu-id="b0588-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1298">'Identity'</span></span>
- <span data-ttu-id="b0588-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1300">'Razor'</span></span>
- <span data-ttu-id="b0588-1301">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1302">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1303">'Blazor'</span></span>
- <span data-ttu-id="b0588-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1304">'Identity'</span></span>
- <span data-ttu-id="b0588-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1306">'Razor'</span></span>
- <span data-ttu-id="b0588-1307">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1308">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1309">'Blazor'</span></span>
- <span data-ttu-id="b0588-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1310">'Identity'</span></span>
- <span data-ttu-id="b0588-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1312">'Razor'</span></span>
- <span data-ttu-id="b0588-1313">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1314">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1315">'Blazor'</span></span>
- <span data-ttu-id="b0588-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1316">'Identity'</span></span>
- <span data-ttu-id="b0588-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1318">'Razor'</span></span>
- <span data-ttu-id="b0588-1319">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1320">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1321">'Blazor'</span></span>
- <span data-ttu-id="b0588-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1322">'Identity'</span></span>
- <span data-ttu-id="b0588-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1324">'Razor'</span></span>
- <span data-ttu-id="b0588-1325">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Oui | | `/my-cool-rewrite-rule/1234/5678` | Non | | `/anotherrewrite-rule/1234/5678`  | Non |</span><span class="sxs-lookup"><span data-stu-id="b0588-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="b0588-1327">À la suite de la partie `^rewrite-rule/` de l’expression se trouvent deux groupes de capture, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="b0588-1328">`\d` signifie *établir une correspondance avec un chiffre (nombre)*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="b0588-1329">Le signe plus (`+`) signifie *établir une correspondance avec une ou plusieurs occurrences du caractère précédent*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="b0588-1330">Par conséquent, l’URL doit contenir un nombre suivi d’une barre oblique, elle-même suivie d’un autre nombre.</span><span class="sxs-lookup"><span data-stu-id="b0588-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="b0588-1331">Ces groupes sont injectés dans l’URL réécrite sous la forme `$1` et `$2`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="b0588-1332">La chaîne de remplacement de la règle de réécriture place les groupes capturés dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="b0588-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="b0588-1333">Le chemin demandé `/rewrite-rule/1234/5678` est réécrit pour obtenir la ressource à l’emplacement `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="b0588-1334">Si une chaîne de requête est présente dans la requête d’origine, elle est conservée lors de la réécriture de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b0588-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="b0588-1335">Il n’y a pas d’aller-retour avec le serveur pour obtenir la ressource.</span><span class="sxs-lookup"><span data-stu-id="b0588-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="b0588-1336">Si la ressource existe, elle est récupérée et retournée au client avec le code d’état *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="b0588-1337">Comme le client n’est pas redirigé, l’URL dans la barre d’adresse du navigateur ne change pas.</span><span class="sxs-lookup"><span data-stu-id="b0588-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="b0588-1338">Les clients ne peuvent pas détecter qu’une opération de réécriture d’URL s’est produite sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="b0588-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-1339">Quand c’est possible, utilisez `skipRemainingRules: true`, car la mise en correspondance de règles est un processus gourmand en ressources qui augmente le temps de réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="b0588-1340">Pour obtenir la réponse d’application la plus rapide :</span><span class="sxs-lookup"><span data-stu-id="b0588-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="b0588-1341">Classez vos règles de réécriture en partant de la règle la plus souvent mise en correspondance jusqu’à la règle la moins souvent mise en correspondance.</span><span class="sxs-lookup"><span data-stu-id="b0588-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="b0588-1342">Ignorez le traitement des règles restantes quand une correspondance est trouvée et qu’aucun traitement de règle supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0588-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="b0588-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b0588-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="b0588-1344">Appliquez des règles Apache mod_rewrite avec <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="b0588-1345">Vérifiez que le fichier de règles est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b0588-1346">Pour obtenir plus d’informations et des exemples de règles mod_rewrite, consultez [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="b0588-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="b0588-1347">Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="b0588-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="b0588-1348">L’exemple d’application redirige les requêtes de `/apache-mod-rules-redirect/(.\*)` vers `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="b0588-1349">Le code d’état de la réponse est *302 - Trouvé*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="b0588-1350">Requête d’origine : `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="b0588-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="b0588-1352">L’intergiciel prend en charge les variables de serveur Apache mod_rewrite suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="b0588-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b0588-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b0588-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b0588-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b0588-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b0588-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="b0588-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="b0588-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="b0588-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b0588-1358">HTTP_HOST</span></span>
* <span data-ttu-id="b0588-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b0588-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="b0588-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b0588-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b0588-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b0588-1361">HTTPS</span></span>
* <span data-ttu-id="b0588-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="b0588-1362">IPV6</span></span>
* <span data-ttu-id="b0588-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b0588-1363">QUERY_STRING</span></span>
* <span data-ttu-id="b0588-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="b0588-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b0588-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="b0588-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="b0588-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="b0588-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="b0588-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b0588-1369">REQUEST_URI</span></span>
* <span data-ttu-id="b0588-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="b0588-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="b0588-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-1372">SERVER_PORT</span></span>
* <span data-ttu-id="b0588-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="b0588-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="b0588-1374">TEMPS</span><span class="sxs-lookup"><span data-stu-id="b0588-1374">TIME</span></span>
* <span data-ttu-id="b0588-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="b0588-1375">TIME_DAY</span></span>
* <span data-ttu-id="b0588-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="b0588-1376">TIME_HOUR</span></span>
* <span data-ttu-id="b0588-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="b0588-1377">TIME_MIN</span></span>
* <span data-ttu-id="b0588-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="b0588-1378">TIME_MON</span></span>
* <span data-ttu-id="b0588-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="b0588-1379">TIME_SEC</span></span>
* <span data-ttu-id="b0588-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="b0588-1380">TIME_WDAY</span></span>
* <span data-ttu-id="b0588-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="b0588-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="b0588-1382">Règles du module de réécriture d’URL IIS</span><span class="sxs-lookup"><span data-stu-id="b0588-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="b0588-1383">Pour utiliser le même ensemble de règles que celui qui s’applique au module de réécriture d’URL IIS, utilisez <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="b0588-1384">Vérifiez que le fichier de règles est déployé avec l’application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b0588-1385">N’indiquez pas au middleware d’utiliser le fichier *web.config* de l’application en cas d’exécution sur Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="b0588-1386">Avec IIS, ces règles doivent être stockées en dehors du fichier *web.config* de l’application pour éviter les conflits avec le module de réécriture IIS.</span><span class="sxs-lookup"><span data-stu-id="b0588-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="b0588-1387">Pour obtenir plus d’informations et des exemples de règles du module de réécriture d’URL IIS, consultez [Utilisation du module de réécriture d’URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) et [Informations de référence sur la configuration du module de réécriture d’URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="b0588-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="b0588-1388">Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles *IISUrlRewrite. xml* :</span><span class="sxs-lookup"><span data-stu-id="b0588-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="b0588-1389">L’exemple d’application réécrit les requêtes de `/iis-rules-rewrite/(.*)` vers `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="b0588-1390">La réponse est envoyée au client avec le code d’état *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="b0588-1391">Requête d’origine : `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="b0588-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="b0588-1393">Si vous avez un module de réécriture IIS actif pour lequel des règles au niveau du serveur qui affecteraient de façon non souhaitée votre application sont configurées, vous pouvez le désactiver pour une application.</span><span class="sxs-lookup"><span data-stu-id="b0588-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="b0588-1394">Pour plus d’informations, consultez [Désactivation de modules IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="b0588-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="b0588-1395">Fonctionnalités non prises en charge</span><span class="sxs-lookup"><span data-stu-id="b0588-1395">Unsupported features</span></span>

<span data-ttu-id="b0588-1396">L’intergiciel intégré à ASP.NET Core 2.x ne prend pas en charge les fonctionnalités de module de réécriture d’URL IIS suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="b0588-1397">Règles de trafic sortant</span><span class="sxs-lookup"><span data-stu-id="b0588-1397">Outbound Rules</span></span>
* <span data-ttu-id="b0588-1398">Variables serveur personnalisées</span><span class="sxs-lookup"><span data-stu-id="b0588-1398">Custom Server Variables</span></span>
* <span data-ttu-id="b0588-1399">Caractères génériques</span><span class="sxs-lookup"><span data-stu-id="b0588-1399">Wildcards</span></span>
* <span data-ttu-id="b0588-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="b0588-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="b0588-1401">Variables de serveur prises en charge</span><span class="sxs-lookup"><span data-stu-id="b0588-1401">Supported server variables</span></span>

<span data-ttu-id="b0588-1402">L’intergiciel prend en charge les variables serveur du module de réécriture d’URL IIS suivantes :</span><span class="sxs-lookup"><span data-stu-id="b0588-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="b0588-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="b0588-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="b0588-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="b0588-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="b0588-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b0588-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b0588-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b0588-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b0588-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b0588-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="b0588-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b0588-1408">HTTP_HOST</span></span>
* <span data-ttu-id="b0588-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b0588-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="b0588-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1410">HTTP_URL</span></span>
* <span data-ttu-id="b0588-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b0588-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b0588-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b0588-1412">HTTPS</span></span>
* <span data-ttu-id="b0588-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="b0588-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b0588-1414">QUERY_STRING</span></span>
* <span data-ttu-id="b0588-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b0588-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="b0588-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b0588-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="b0588-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b0588-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b0588-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b0588-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="b0588-1419">Vous pouvez également obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> par le biais d’un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="b0588-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="b0588-1420">Cette approche peut fournir davantage de flexibilité pour l’emplacement de vos fichiers de règles de réécriture.</span><span class="sxs-lookup"><span data-stu-id="b0588-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="b0588-1421">Vérifiez que vos fichiers de règles de réécriture sont déployés sur le serveur dans le chemin que vous fournissez.</span><span class="sxs-lookup"><span data-stu-id="b0588-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="b0588-1422">Règle basée sur une méthode</span><span class="sxs-lookup"><span data-stu-id="b0588-1422">Method-based rule</span></span>

<span data-ttu-id="b0588-1423">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour implémenter votre propre logique de règle dans une méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="b0588-1424">`Add` expose <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, ce qui rend <xref:Microsoft.AspNetCore.Http.HttpContext> disponible pour une utilisation dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="b0588-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) détermine la façon dont le traitement du pipeline supplémentaire est géré.</span><span class="sxs-lookup"><span data-stu-id="b0588-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="b0588-1426">Définissez la valeur sur un des champs <xref:Microsoft.AspNetCore.Rewrite.RuleResult> décrits dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b0588-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="b0588-1427">Action</span><span class="sxs-lookup"><span data-stu-id="b0588-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="b0588-1428">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1429">'Blazor'</span></span>
- <span data-ttu-id="b0588-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1430">'Identity'</span></span>
- <span data-ttu-id="b0588-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1432">'Razor'</span></span>
- <span data-ttu-id="b0588-1433">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1434">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1435">'Blazor'</span></span>
- <span data-ttu-id="b0588-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1436">'Identity'</span></span>
- <span data-ttu-id="b0588-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1438">'Razor'</span></span>
- <span data-ttu-id="b0588-1439">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1440">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1441">'Blazor'</span></span>
- <span data-ttu-id="b0588-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1442">'Identity'</span></span>
- <span data-ttu-id="b0588-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1444">'Razor'</span></span>
- <span data-ttu-id="b0588-1445">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1446">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1447">'Blazor'</span></span>
- <span data-ttu-id="b0588-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1448">'Identity'</span></span>
- <span data-ttu-id="b0588-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1450">'Razor'</span></span>
- <span data-ttu-id="b0588-1451">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1452">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1453">'Blazor'</span></span>
- <span data-ttu-id="b0588-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1454">'Identity'</span></span>
- <span data-ttu-id="b0588-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1456">'Razor'</span></span>
- <span data-ttu-id="b0588-1457">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1458">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1459">'Blazor'</span></span>
- <span data-ttu-id="b0588-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1460">'Identity'</span></span>
- <span data-ttu-id="b0588-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1462">'Razor'</span></span>
- <span data-ttu-id="b0588-1463">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1464">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1465">'Blazor'</span></span>
- <span data-ttu-id="b0588-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1466">'Identity'</span></span>
- <span data-ttu-id="b0588-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1468">'Razor'</span></span>
- <span data-ttu-id="b0588-1469">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1470">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1471">'Blazor'</span></span>
- <span data-ttu-id="b0588-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1472">'Identity'</span></span>
- <span data-ttu-id="b0588-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1474">'Razor'</span></span>
- <span data-ttu-id="b0588-1475">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1476">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1477">'Blazor'</span></span>
- <span data-ttu-id="b0588-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1478">'Identity'</span></span>
- <span data-ttu-id="b0588-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1480">'Razor'</span></span>
- <span data-ttu-id="b0588-1481">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1482">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1483">'Blazor'</span></span>
- <span data-ttu-id="b0588-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1484">'Identity'</span></span>
- <span data-ttu-id="b0588-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1486">'Razor'</span></span>
- <span data-ttu-id="b0588-1487">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1488">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1489">'Blazor'</span></span>
- <span data-ttu-id="b0588-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1490">'Identity'</span></span>
- <span data-ttu-id="b0588-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1492">'Razor'</span></span>
- <span data-ttu-id="b0588-1493">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1494">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1495">'Blazor'</span></span>
- <span data-ttu-id="b0588-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1496">'Identity'</span></span>
- <span data-ttu-id="b0588-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1498">'Razor'</span></span>
- <span data-ttu-id="b0588-1499">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1500">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1501">'Blazor'</span></span>
- <span data-ttu-id="b0588-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1502">'Identity'</span></span>
- <span data-ttu-id="b0588-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1504">'Razor'</span></span>
- <span data-ttu-id="b0588-1505">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1506">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1507">'Blazor'</span></span>
- <span data-ttu-id="b0588-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1508">'Identity'</span></span>
- <span data-ttu-id="b0588-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1510">'Razor'</span></span>
- <span data-ttu-id="b0588-1511">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1512">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1513">'Blazor'</span></span>
- <span data-ttu-id="b0588-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1514">'Identity'</span></span>
- <span data-ttu-id="b0588-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1516">'Razor'</span></span>
- <span data-ttu-id="b0588-1517">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1518">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1519">'Blazor'</span></span>
- <span data-ttu-id="b0588-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1520">'Identity'</span></span>
- <span data-ttu-id="b0588-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1522">'Razor'</span></span>
- <span data-ttu-id="b0588-1523">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1524">------------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1525">'Blazor'</span></span>
- <span data-ttu-id="b0588-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1526">'Identity'</span></span>
- <span data-ttu-id="b0588-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1528">'Razor'</span></span>
- <span data-ttu-id="b0588-1529">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1530">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1531">'Blazor'</span></span>
- <span data-ttu-id="b0588-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1532">'Identity'</span></span>
- <span data-ttu-id="b0588-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1534">'Razor'</span></span>
- <span data-ttu-id="b0588-1535">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1536">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1537">'Blazor'</span></span>
- <span data-ttu-id="b0588-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1538">'Identity'</span></span>
- <span data-ttu-id="b0588-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1540">'Razor'</span></span>
- <span data-ttu-id="b0588-1541">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1542">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1543">'Blazor'</span></span>
- <span data-ttu-id="b0588-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1544">'Identity'</span></span>
- <span data-ttu-id="b0588-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1546">'Razor'</span></span>
- <span data-ttu-id="b0588-1547">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1548">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1549">'Blazor'</span></span>
- <span data-ttu-id="b0588-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1550">'Identity'</span></span>
- <span data-ttu-id="b0588-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1552">'Razor'</span></span>
- <span data-ttu-id="b0588-1553">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1554">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1555">'Blazor'</span></span>
- <span data-ttu-id="b0588-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1556">'Identity'</span></span>
- <span data-ttu-id="b0588-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1558">'Razor'</span></span>
- <span data-ttu-id="b0588-1559">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1560">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1561">'Blazor'</span></span>
- <span data-ttu-id="b0588-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1562">'Identity'</span></span>
- <span data-ttu-id="b0588-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1564">'Razor'</span></span>
- <span data-ttu-id="b0588-1565">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1566">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1567">'Blazor'</span></span>
- <span data-ttu-id="b0588-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1568">'Identity'</span></span>
- <span data-ttu-id="b0588-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1570">'Razor'</span></span>
- <span data-ttu-id="b0588-1571">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1572">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1573">'Blazor'</span></span>
- <span data-ttu-id="b0588-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1574">'Identity'</span></span>
- <span data-ttu-id="b0588-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1576">'Razor'</span></span>
- <span data-ttu-id="b0588-1577">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1578">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1579">'Blazor'</span></span>
- <span data-ttu-id="b0588-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1580">'Identity'</span></span>
- <span data-ttu-id="b0588-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1582">'Razor'</span></span>
- <span data-ttu-id="b0588-1583">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1584">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1585">'Blazor'</span></span>
- <span data-ttu-id="b0588-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1586">'Identity'</span></span>
- <span data-ttu-id="b0588-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1588">'Razor'</span></span>
- <span data-ttu-id="b0588-1589">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1590">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1591">'Blazor'</span></span>
- <span data-ttu-id="b0588-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1592">'Identity'</span></span>
- <span data-ttu-id="b0588-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1594">'Razor'</span></span>
- <span data-ttu-id="b0588-1595">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1596">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1597">'Blazor'</span></span>
- <span data-ttu-id="b0588-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1598">'Identity'</span></span>
- <span data-ttu-id="b0588-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1600">'Razor'</span></span>
- <span data-ttu-id="b0588-1601">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1602">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1603">'Blazor'</span></span>
- <span data-ttu-id="b0588-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1604">'Identity'</span></span>
- <span data-ttu-id="b0588-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1606">'Razor'</span></span>
- <span data-ttu-id="b0588-1607">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1608">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1609">'Blazor'</span></span>
- <span data-ttu-id="b0588-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1610">'Identity'</span></span>
- <span data-ttu-id="b0588-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1612">'Razor'</span></span>
- <span data-ttu-id="b0588-1613">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1614">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1615">'Blazor'</span></span>
- <span data-ttu-id="b0588-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1616">'Identity'</span></span>
- <span data-ttu-id="b0588-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1618">'Razor'</span></span>
- <span data-ttu-id="b0588-1619">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1620">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1621">'Blazor'</span></span>
- <span data-ttu-id="b0588-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1622">'Identity'</span></span>
- <span data-ttu-id="b0588-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1624">'Razor'</span></span>
- <span data-ttu-id="b0588-1625">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1626">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1627">'Blazor'</span></span>
- <span data-ttu-id="b0588-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1628">'Identity'</span></span>
- <span data-ttu-id="b0588-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1630">'Razor'</span></span>
- <span data-ttu-id="b0588-1631">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1632">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1633">'Blazor'</span></span>
- <span data-ttu-id="b0588-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1634">'Identity'</span></span>
- <span data-ttu-id="b0588-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1636">'Razor'</span></span>
- <span data-ttu-id="b0588-1637">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1638">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1639">'Blazor'</span></span>
- <span data-ttu-id="b0588-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1640">'Identity'</span></span>
- <span data-ttu-id="b0588-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1642">'Razor'</span></span>
- <span data-ttu-id="b0588-1643">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1644">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1645">'Blazor'</span></span>
- <span data-ttu-id="b0588-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1646">'Identity'</span></span>
- <span data-ttu-id="b0588-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1648">'Razor'</span></span>
- <span data-ttu-id="b0588-1649">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1650">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1651">'Blazor'</span></span>
- <span data-ttu-id="b0588-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1652">'Identity'</span></span>
- <span data-ttu-id="b0588-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1654">'Razor'</span></span>
- <span data-ttu-id="b0588-1655">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1656">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1657">'Blazor'</span></span>
- <span data-ttu-id="b0588-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1658">'Identity'</span></span>
- <span data-ttu-id="b0588-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1660">'Razor'</span></span>
- <span data-ttu-id="b0588-1661">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1662">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1663">'Blazor'</span></span>
- <span data-ttu-id="b0588-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1664">'Identity'</span></span>
- <span data-ttu-id="b0588-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1666">'Razor'</span></span>
- <span data-ttu-id="b0588-1667">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1668">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1669">'Blazor'</span></span>
- <span data-ttu-id="b0588-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1670">'Identity'</span></span>
- <span data-ttu-id="b0588-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1672">'Razor'</span></span>
- <span data-ttu-id="b0588-1673">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1674">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1675">'Blazor'</span></span>
- <span data-ttu-id="b0588-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1676">'Identity'</span></span>
- <span data-ttu-id="b0588-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1678">'Razor'</span></span>
- <span data-ttu-id="b0588-1679">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1680">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1681">'Blazor'</span></span>
- <span data-ttu-id="b0588-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1682">'Identity'</span></span>
- <span data-ttu-id="b0588-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1684">'Razor'</span></span>
- <span data-ttu-id="b0588-1685">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1686">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1687">'Blazor'</span></span>
- <span data-ttu-id="b0588-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1688">'Identity'</span></span>
- <span data-ttu-id="b0588-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1690">'Razor'</span></span>
- <span data-ttu-id="b0588-1691">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1692">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1693">'Blazor'</span></span>
- <span data-ttu-id="b0588-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1694">'Identity'</span></span>
- <span data-ttu-id="b0588-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1696">'Razor'</span></span>
- <span data-ttu-id="b0588-1697">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1698">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1699">'Blazor'</span></span>
- <span data-ttu-id="b0588-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1700">'Identity'</span></span>
- <span data-ttu-id="b0588-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1702">'Razor'</span></span>
- <span data-ttu-id="b0588-1703">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1704">-------------------------------- | | `RuleResult.ContinueRules`(par défaut) | Continuez à appliquer les règles.</span><span class="sxs-lookup"><span data-stu-id="b0588-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="b0588-1705">| | `RuleResult.EndResponse`             | Arrêtez l’application des règles et envoyez la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="b0588-1706">| | `RuleResult.SkipRemainingRules`      | Arrête l’application des règles et envoie le contexte à l’intergiciel (middleware) suivant.</span><span class="sxs-lookup"><span data-stu-id="b0588-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="b0588-1707">L’exemple d’application présente une méthode qui redirige les requêtes de chemins qui se terminent par *.xml*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="b0588-1708">Si une requête est faite pour `/file.xml`, la requête est redirigée vers `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="b0588-1709">Le code d’état est défini sur *301 - Déplacé de façon permanente*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="b0588-1710">Quand le navigateur fait une nouvelle requête pour */xmlfiles/file.xml*, le middleware de fichiers statiques délivre le fichier au client à partir du dossier *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="b0588-1711">Pour une redirection, définissez explicitement le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="b0588-1712">Sinon, un code d’état *200 - OK* est retourné et la redirection ne se produit pas sur le client.</span><span class="sxs-lookup"><span data-stu-id="b0588-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="b0588-1713">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="b0588-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="b0588-1714">Cette approche peut également réécrire des requêtes.</span><span class="sxs-lookup"><span data-stu-id="b0588-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="b0588-1715">L’exemple d’application montre la réécriture du chemin pour toute requête demandant de délivrer le fichier texte *file.txt* à partir du dossier *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="b0588-1716">Le middleware de fichiers statiques délivre le fichier en fonction du chemin de requête mis à jour :</span><span class="sxs-lookup"><span data-stu-id="b0588-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="b0588-1717">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="b0588-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="b0588-1718">Règle basée sur IRule</span><span class="sxs-lookup"><span data-stu-id="b0588-1718">IRule-based rule</span></span>

<span data-ttu-id="b0588-1719">Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour insérer votre propre logique de règle dans une classe qui implémente l’interface <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="b0588-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="b0588-1720">`IRule` offre davantage de flexibilité par rapport à l’approche de la règle basée sur une méthode.</span><span class="sxs-lookup"><span data-stu-id="b0588-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="b0588-1721">Votre classe d’implémentation peut inclure un constructeur qui vous permet de passer des paramètres pour la méthode <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="b0588-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="b0588-1722">Les valeurs des paramètres dans l’exemple d’application pour `extension` et `newPath` sont vérifiées afin de remplir plusieurs conditions.</span><span class="sxs-lookup"><span data-stu-id="b0588-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="b0588-1723">`extension` doit contenir une valeur, laquelle doit être *.png*, *.jpg* ou *.gif*.</span><span class="sxs-lookup"><span data-stu-id="b0588-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="b0588-1724">Si `newPath` n’est pas valide, un <xref:System.ArgumentException> est levé.</span><span class="sxs-lookup"><span data-stu-id="b0588-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="b0588-1725">Si une requête est faite pour *image.png*, la requête est redirigée vers `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="b0588-1726">Si une requête est faite pour *image.jpg*, la requête est redirigée vers `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="b0588-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="b0588-1727">Le code d’état est défini sur *301 - Déplacé de façon permanente*, et `context.Result` est défini de façon à cesser le traitement des règles et envoyer la réponse.</span><span class="sxs-lookup"><span data-stu-id="b0588-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="b0588-1728">Requête d’origine : `/image.png`</span><span class="sxs-lookup"><span data-stu-id="b0588-1728">Original Request: `/image.png`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="b0588-1730">Requête d’origine : `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="b0588-1730">Original Request: `/image.jpg`</span></span>

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="b0588-1732">Exemples d’expressions régulières</span><span class="sxs-lookup"><span data-stu-id="b0588-1732">Regex examples</span></span>

| <span data-ttu-id="b0588-1733">Objectif</span><span class="sxs-lookup"><span data-stu-id="b0588-1733">Goal</span></span> | <span data-ttu-id="b0588-1734">Chaîne d’expression régulière et</span><span class="sxs-lookup"><span data-stu-id="b0588-1734">Regex String &</span></span><br><span data-ttu-id="b0588-1735">exemple de correspondance</span><span class="sxs-lookup"><span data-stu-id="b0588-1735">Match Example</span></span> | <span data-ttu-id="b0588-1736">Chaîne de remplacement et</span><span class="sxs-lookup"><span data-stu-id="b0588-1736">Replacement String &</span></span><br><span data-ttu-id="b0588-1737">exemple de sortie</span><span class="sxs-lookup"><span data-stu-id="b0588-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="b0588-1738">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1739">'Blazor'</span></span>
- <span data-ttu-id="b0588-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1740">'Identity'</span></span>
- <span data-ttu-id="b0588-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1742">'Razor'</span></span>
- <span data-ttu-id="b0588-1743">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1744">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1745">'Blazor'</span></span>
- <span data-ttu-id="b0588-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1746">'Identity'</span></span>
- <span data-ttu-id="b0588-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1748">'Razor'</span></span>
- <span data-ttu-id="b0588-1749">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1750">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1751">'Blazor'</span></span>
- <span data-ttu-id="b0588-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1752">'Identity'</span></span>
- <span data-ttu-id="b0588-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1754">'Razor'</span></span>
- <span data-ttu-id="b0588-1755">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1756">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1757">'Blazor'</span></span>
- <span data-ttu-id="b0588-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1758">'Identity'</span></span>
- <span data-ttu-id="b0588-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1760">'Razor'</span></span>
- <span data-ttu-id="b0588-1761">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1762">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1763">'Blazor'</span></span>
- <span data-ttu-id="b0588-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1764">'Identity'</span></span>
- <span data-ttu-id="b0588-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1766">'Razor'</span></span>
- <span data-ttu-id="b0588-1767">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1768">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1769">'Blazor'</span></span>
- <span data-ttu-id="b0588-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1770">'Identity'</span></span>
- <span data-ttu-id="b0588-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1772">'Razor'</span></span>
- <span data-ttu-id="b0588-1773">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1774">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1775">'Blazor'</span></span>
- <span data-ttu-id="b0588-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1776">'Identity'</span></span>
- <span data-ttu-id="b0588-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1778">'Razor'</span></span>
- <span data-ttu-id="b0588-1779">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1780">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1781">'Blazor'</span></span>
- <span data-ttu-id="b0588-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1782">'Identity'</span></span>
- <span data-ttu-id="b0588-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1784">'Razor'</span></span>
- <span data-ttu-id="b0588-1785">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1786">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1787">'Blazor'</span></span>
- <span data-ttu-id="b0588-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1788">'Identity'</span></span>
- <span data-ttu-id="b0588-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1790">'Razor'</span></span>
- <span data-ttu-id="b0588-1791">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1792">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1793">'Blazor'</span></span>
- <span data-ttu-id="b0588-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1794">'Identity'</span></span>
- <span data-ttu-id="b0588-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1796">'Razor'</span></span>
- <span data-ttu-id="b0588-1797">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1798">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1799">'Blazor'</span></span>
- <span data-ttu-id="b0588-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1800">'Identity'</span></span>
- <span data-ttu-id="b0588-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1802">'Razor'</span></span>
- <span data-ttu-id="b0588-1803">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1804">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1805">'Blazor'</span></span>
- <span data-ttu-id="b0588-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1806">'Identity'</span></span>
- <span data-ttu-id="b0588-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1808">'Razor'</span></span>
- <span data-ttu-id="b0588-1809">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1810">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1811">'Blazor'</span></span>
- <span data-ttu-id="b0588-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1812">'Identity'</span></span>
- <span data-ttu-id="b0588-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1814">'Razor'</span></span>
- <span data-ttu-id="b0588-1815">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1816">---------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1817">'Blazor'</span></span>
- <span data-ttu-id="b0588-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1818">'Identity'</span></span>
- <span data-ttu-id="b0588-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1820">'Razor'</span></span>
- <span data-ttu-id="b0588-1821">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1822">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1823">'Blazor'</span></span>
- <span data-ttu-id="b0588-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1824">'Identity'</span></span>
- <span data-ttu-id="b0588-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1826">'Razor'</span></span>
- <span data-ttu-id="b0588-1827">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1828">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1829">'Blazor'</span></span>
- <span data-ttu-id="b0588-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1830">'Identity'</span></span>
- <span data-ttu-id="b0588-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1832">'Razor'</span></span>
- <span data-ttu-id="b0588-1833">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1834">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1835">'Blazor'</span></span>
- <span data-ttu-id="b0588-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1836">'Identity'</span></span>
- <span data-ttu-id="b0588-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1838">'Razor'</span></span>
- <span data-ttu-id="b0588-1839">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1840">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1841">'Blazor'</span></span>
- <span data-ttu-id="b0588-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1842">'Identity'</span></span>
- <span data-ttu-id="b0588-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1844">'Razor'</span></span>
- <span data-ttu-id="b0588-1845">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1846">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1847">'Blazor'</span></span>
- <span data-ttu-id="b0588-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1848">'Identity'</span></span>
- <span data-ttu-id="b0588-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1850">'Razor'</span></span>
- <span data-ttu-id="b0588-1851">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1852">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1853">'Blazor'</span></span>
- <span data-ttu-id="b0588-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1854">'Identity'</span></span>
- <span data-ttu-id="b0588-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1856">'Razor'</span></span>
- <span data-ttu-id="b0588-1857">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1858">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1859">'Blazor'</span></span>
- <span data-ttu-id="b0588-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1860">'Identity'</span></span>
- <span data-ttu-id="b0588-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1862">'Razor'</span></span>
- <span data-ttu-id="b0588-1863">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1864">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1865">'Blazor'</span></span>
- <span data-ttu-id="b0588-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1866">'Identity'</span></span>
- <span data-ttu-id="b0588-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1868">'Razor'</span></span>
- <span data-ttu-id="b0588-1869">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1870">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1871">'Blazor'</span></span>
- <span data-ttu-id="b0588-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1872">'Identity'</span></span>
- <span data-ttu-id="b0588-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1874">'Razor'</span></span>
- <span data-ttu-id="b0588-1875">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1876">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1877">'Blazor'</span></span>
- <span data-ttu-id="b0588-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1878">'Identity'</span></span>
- <span data-ttu-id="b0588-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1880">'Razor'</span></span>
- <span data-ttu-id="b0588-1881">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1882">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1883">'Blazor'</span></span>
- <span data-ttu-id="b0588-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1884">'Identity'</span></span>
- <span data-ttu-id="b0588-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1886">'Razor'</span></span>
- <span data-ttu-id="b0588-1887">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1888">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1889">'Blazor'</span></span>
- <span data-ttu-id="b0588-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1890">'Identity'</span></span>
- <span data-ttu-id="b0588-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1892">'Razor'</span></span>
- <span data-ttu-id="b0588-1893">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1894">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1895">'Blazor'</span></span>
- <span data-ttu-id="b0588-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1896">'Identity'</span></span>
- <span data-ttu-id="b0588-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1898">'Razor'</span></span>
- <span data-ttu-id="b0588-1899">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1900">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1901">'Blazor'</span></span>
- <span data-ttu-id="b0588-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1902">'Identity'</span></span>
- <span data-ttu-id="b0588-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1904">'Razor'</span></span>
- <span data-ttu-id="b0588-1905">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1906">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1907">'Blazor'</span></span>
- <span data-ttu-id="b0588-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1908">'Identity'</span></span>
- <span data-ttu-id="b0588-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1910">'Razor'</span></span>
- <span data-ttu-id="b0588-1911">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b0588-1912">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b0588-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b0588-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1913">'Blazor'</span></span>
- <span data-ttu-id="b0588-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0588-1914">'Identity'</span></span>
- <span data-ttu-id="b0588-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0588-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0588-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0588-1916">'Razor'</span></span>
- <span data-ttu-id="b0588-1917">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="b0588-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="b0588-1918">------------------- | | Réécrire le chemin dans QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="b0588-1919">`/path?var1=abc&var2=123`| | Barre oblique de fin de bande |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="b0588-1920">`/path`| | Appliquer la barre oblique de fin |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="b0588-1921">`/path/`| | Évitez de réécrire des requêtes spécifiques | `^(.*)(?<!\.axd)$`ou`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="b0588-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="b0588-1922">Oui : `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="b0588-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="b0588-1923">º`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="b0588-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="b0588-1924">`/resource.axd`| | Réorganiser les segments d’URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="b0588-1925">`path/3/2/1`| | Remplacer un segment d’URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="b0588-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b0588-1926">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b0588-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="b0588-1927">Expressions régulières dans .NET</span><span class="sxs-lookup"><span data-stu-id="b0588-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="b0588-1928">Langage des expressions régulières-aide-mémoire</span><span class="sxs-lookup"><span data-stu-id="b0588-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="b0588-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b0588-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b0588-1930">Utilisation du module de réécriture d’URL 2.0 (pour IIS)</span><span class="sxs-lookup"><span data-stu-id="b0588-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="b0588-1931">Informations de référence sur la configuration du module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="b0588-1932">Forum du module de réécriture d’URL IIS</span><span class="sxs-lookup"><span data-stu-id="b0588-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="b0588-1933">Maintenir une structure d’URL simple</span><span class="sxs-lookup"><span data-stu-id="b0588-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="b0588-1934">10 conseils et astuces pour la réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="b0588-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="b0588-1935">Mettre ou ne pas mettre une barre oblique</span><span class="sxs-lookup"><span data-stu-id="b0588-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
