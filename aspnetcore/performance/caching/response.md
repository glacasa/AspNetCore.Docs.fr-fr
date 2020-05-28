---
<span data-ttu-id="99436-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-102">'Blazor'</span></span>
- <span data-ttu-id="99436-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-103">'Identity'</span></span>
- <span data-ttu-id="99436-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-105">'Razor'</span></span>
- <span data-ttu-id="99436-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="99436-107">Mise en cache des réponses dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99436-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="99436-108">Par [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="99436-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="99436-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99436-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="99436-110">La mise en cache des réponses réduit le nombre de demandes qu’un client ou un proxy effectue sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="99436-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="99436-111">La mise en cache des réponses réduit également la quantité de travail effectuée par le serveur Web pour générer une réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="99436-112">La mise en cache des réponses est contrôlée par des en-têtes qui spécifient comment vous souhaitez que le client, le proxy et l’intergiciel (middleware) effectuent le cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="99436-113">L' [attribut ResponseCache](#responsecache-attribute) participe à la définition des en-têtes de mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="99436-114">Les clients et les proxys intermédiaires doivent honorer les en-têtes pour la mise en cache des réponses sous la [spécification de Caching HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="99436-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="99436-115">Pour la mise en cache côté serveur qui suit la spécification de mise en cache HTTP 1,1, utilisez l’intergiciel (middleware) de [mise en](xref:performance/caching/middleware)cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="99436-116">L’intergiciel (middleware) peut utiliser les <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Propriétés pour influencer le comportement de mise en cache côté serveur.</span><span class="sxs-lookup"><span data-stu-id="99436-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="99436-117">Mise en cache des réponses HTTP</span><span class="sxs-lookup"><span data-stu-id="99436-117">HTTP-based response caching</span></span>

<span data-ttu-id="99436-118">La [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234) décrit le comportement des caches Internet.</span><span class="sxs-lookup"><span data-stu-id="99436-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="99436-119">L’en-tête HTTP principal utilisé pour la mise en cache est [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), qui est utilisé pour spécifier les *directives*de cache.</span><span class="sxs-lookup"><span data-stu-id="99436-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="99436-120">Les directives contrôlent le comportement de mise en cache en fonction des demandes en provenance des clients et des serveurs, tandis que les réponses sont rétablies des serveurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="99436-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="99436-121">Les demandes et les réponses passent par les serveurs proxy, et les serveurs proxy doivent également se conformer à la spécification de mise en cache HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="99436-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="99436-122">`Cache-Control`Les directives communes sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="99436-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="99436-123">Directive</span><span class="sxs-lookup"><span data-stu-id="99436-123">Directive</span></span>                                                       | <span data-ttu-id="99436-124">Action</span><span class="sxs-lookup"><span data-stu-id="99436-124">Action</span></span> |
| ---
<span data-ttu-id="99436-125">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-126">'Blazor'</span></span>
- <span data-ttu-id="99436-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-127">'Identity'</span></span>
- <span data-ttu-id="99436-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-129">'Razor'</span></span>
- <span data-ttu-id="99436-130">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-131">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-132">'Blazor'</span></span>
- <span data-ttu-id="99436-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-133">'Identity'</span></span>
- <span data-ttu-id="99436-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-135">'Razor'</span></span>
- <span data-ttu-id="99436-136">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-137">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-138">'Blazor'</span></span>
- <span data-ttu-id="99436-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-139">'Identity'</span></span>
- <span data-ttu-id="99436-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-141">'Razor'</span></span>
- <span data-ttu-id="99436-142">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-143">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-144">'Blazor'</span></span>
- <span data-ttu-id="99436-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-145">'Identity'</span></span>
- <span data-ttu-id="99436-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-147">'Razor'</span></span>
- <span data-ttu-id="99436-148">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-149">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-150">'Blazor'</span></span>
- <span data-ttu-id="99436-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-151">'Identity'</span></span>
- <span data-ttu-id="99436-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-153">'Razor'</span></span>
- <span data-ttu-id="99436-154">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-155">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-156">'Blazor'</span></span>
- <span data-ttu-id="99436-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-157">'Identity'</span></span>
- <span data-ttu-id="99436-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-159">'Razor'</span></span>
- <span data-ttu-id="99436-160">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-161">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-162">'Blazor'</span></span>
- <span data-ttu-id="99436-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-163">'Identity'</span></span>
- <span data-ttu-id="99436-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-165">'Razor'</span></span>
- <span data-ttu-id="99436-166">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-167">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-168">'Blazor'</span></span>
- <span data-ttu-id="99436-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-169">'Identity'</span></span>
- <span data-ttu-id="99436-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-171">'Razor'</span></span>
- <span data-ttu-id="99436-172">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-173">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-174">'Blazor'</span></span>
- <span data-ttu-id="99436-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-175">'Identity'</span></span>
- <span data-ttu-id="99436-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-177">'Razor'</span></span>
- <span data-ttu-id="99436-178">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-179">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-180">'Blazor'</span></span>
- <span data-ttu-id="99436-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-181">'Identity'</span></span>
- <span data-ttu-id="99436-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-183">'Razor'</span></span>
- <span data-ttu-id="99436-184">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-185">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-186">'Blazor'</span></span>
- <span data-ttu-id="99436-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-187">'Identity'</span></span>
- <span data-ttu-id="99436-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-189">'Razor'</span></span>
- <span data-ttu-id="99436-190">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-191">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-192">'Blazor'</span></span>
- <span data-ttu-id="99436-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-193">'Identity'</span></span>
- <span data-ttu-id="99436-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-195">'Razor'</span></span>
- <span data-ttu-id="99436-196">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-197">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-198">'Blazor'</span></span>
- <span data-ttu-id="99436-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-199">'Identity'</span></span>
- <span data-ttu-id="99436-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-201">'Razor'</span></span>
- <span data-ttu-id="99436-202">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-203">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-204">'Blazor'</span></span>
- <span data-ttu-id="99436-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-205">'Identity'</span></span>
- <span data-ttu-id="99436-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-207">'Razor'</span></span>
- <span data-ttu-id="99436-208">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-209">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-210">'Blazor'</span></span>
- <span data-ttu-id="99436-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-211">'Identity'</span></span>
- <span data-ttu-id="99436-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-213">'Razor'</span></span>
- <span data-ttu-id="99436-214">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-215">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-216">'Blazor'</span></span>
- <span data-ttu-id="99436-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-217">'Identity'</span></span>
- <span data-ttu-id="99436-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-219">'Razor'</span></span>
- <span data-ttu-id="99436-220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-221">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-222">'Blazor'</span></span>
- <span data-ttu-id="99436-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-223">'Identity'</span></span>
- <span data-ttu-id="99436-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-225">'Razor'</span></span>
- <span data-ttu-id="99436-226">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-227">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-228">'Blazor'</span></span>
- <span data-ttu-id="99436-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-229">'Identity'</span></span>
- <span data-ttu-id="99436-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-231">'Razor'</span></span>
- <span data-ttu-id="99436-232">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-233">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-234">'Blazor'</span></span>
- <span data-ttu-id="99436-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-235">'Identity'</span></span>
- <span data-ttu-id="99436-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-237">'Razor'</span></span>
- <span data-ttu-id="99436-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-239">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-240">'Blazor'</span></span>
- <span data-ttu-id="99436-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-241">'Identity'</span></span>
- <span data-ttu-id="99436-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-243">'Razor'</span></span>
- <span data-ttu-id="99436-244">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-245">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-246">'Blazor'</span></span>
- <span data-ttu-id="99436-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-247">'Identity'</span></span>
- <span data-ttu-id="99436-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-249">'Razor'</span></span>
- <span data-ttu-id="99436-250">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-251">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-252">'Blazor'</span></span>
- <span data-ttu-id="99436-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-253">'Identity'</span></span>
- <span data-ttu-id="99436-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-255">'Razor'</span></span>
- <span data-ttu-id="99436-256">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-257">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-258">'Blazor'</span></span>
- <span data-ttu-id="99436-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-259">'Identity'</span></span>
- <span data-ttu-id="99436-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-261">'Razor'</span></span>
- <span data-ttu-id="99436-262">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-263">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-264">'Blazor'</span></span>
- <span data-ttu-id="99436-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-265">'Identity'</span></span>
- <span data-ttu-id="99436-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-267">'Razor'</span></span>
- <span data-ttu-id="99436-268">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-269">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-270">'Blazor'</span></span>
- <span data-ttu-id="99436-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-271">'Identity'</span></span>
- <span data-ttu-id="99436-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-273">'Razor'</span></span>
- <span data-ttu-id="99436-274">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-275">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-276">'Blazor'</span></span>
- <span data-ttu-id="99436-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-277">'Identity'</span></span>
- <span data-ttu-id="99436-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-279">'Razor'</span></span>
- <span data-ttu-id="99436-280">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-281">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-282">'Blazor'</span></span>
- <span data-ttu-id="99436-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-283">'Identity'</span></span>
- <span data-ttu-id="99436-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-285">'Razor'</span></span>
- <span data-ttu-id="99436-286">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-287">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-288">'Blazor'</span></span>
- <span data-ttu-id="99436-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-289">'Identity'</span></span>
- <span data-ttu-id="99436-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-291">'Razor'</span></span>
- <span data-ttu-id="99436-292">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-293">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-294">'Blazor'</span></span>
- <span data-ttu-id="99436-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-295">'Identity'</span></span>
- <span data-ttu-id="99436-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-297">'Razor'</span></span>
- <span data-ttu-id="99436-298">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-298">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-299">-------------------------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-300">'Blazor'</span></span>
- <span data-ttu-id="99436-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-301">'Identity'</span></span>
- <span data-ttu-id="99436-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-303">'Razor'</span></span>
- <span data-ttu-id="99436-304">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-304">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Un cache peut stocker la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="99436-306">| | [privé](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | La réponse ne doit pas être stockée par un cache partagé.</span><span class="sxs-lookup"><span data-stu-id="99436-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="99436-307">Un cache privé peut stocker et réutiliser la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="99436-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Le client n’accepte pas de réponse dont l’âge est supérieur au nombre de secondes spécifié.</span><span class="sxs-lookup"><span data-stu-id="99436-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="99436-309">Exemples : `max-age=60` (60 secondes), `max-age=2592000` (1 mois) | | [non-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **Sur les demandes**: un cache ne doit pas utiliser une réponse stockée pour satisfaire la demande.</span><span class="sxs-lookup"><span data-stu-id="99436-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="99436-310">Le serveur d’origine régénère la réponse pour le client, et l’intergiciel met à jour la réponse stockée dans son cache.</span><span class="sxs-lookup"><span data-stu-id="99436-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="99436-311">**Sur les réponses**: la réponse ne doit pas être utilisée pour une demande ultérieure sans validation sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="99436-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="99436-312">| | [non-Store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Sur les demandes**: un cache ne doit pas stocker la demande.</span><span class="sxs-lookup"><span data-stu-id="99436-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="99436-313">**Sur les réponses**: un cache ne doit pas stocker une partie de la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="99436-314">Les autres en-têtes de cache qui jouent un rôle dans la mise en cache sont répertoriés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="99436-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="99436-315">Header</span><span class="sxs-lookup"><span data-stu-id="99436-315">Header</span></span>                                                     | <span data-ttu-id="99436-316">Fonction</span><span class="sxs-lookup"><span data-stu-id="99436-316">Function</span></span> |
| ---
<span data-ttu-id="99436-317">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-318">'Blazor'</span></span>
- <span data-ttu-id="99436-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-319">'Identity'</span></span>
- <span data-ttu-id="99436-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-321">'Razor'</span></span>
- <span data-ttu-id="99436-322">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-323">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-324">'Blazor'</span></span>
- <span data-ttu-id="99436-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-325">'Identity'</span></span>
- <span data-ttu-id="99436-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-327">'Razor'</span></span>
- <span data-ttu-id="99436-328">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-329">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-330">'Blazor'</span></span>
- <span data-ttu-id="99436-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-331">'Identity'</span></span>
- <span data-ttu-id="99436-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-333">'Razor'</span></span>
- <span data-ttu-id="99436-334">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-335">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-336">'Blazor'</span></span>
- <span data-ttu-id="99436-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-337">'Identity'</span></span>
- <span data-ttu-id="99436-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-339">'Razor'</span></span>
- <span data-ttu-id="99436-340">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-341">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-342">'Blazor'</span></span>
- <span data-ttu-id="99436-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-343">'Identity'</span></span>
- <span data-ttu-id="99436-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-345">'Razor'</span></span>
- <span data-ttu-id="99436-346">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-347">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-348">'Blazor'</span></span>
- <span data-ttu-id="99436-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-349">'Identity'</span></span>
- <span data-ttu-id="99436-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-351">'Razor'</span></span>
- <span data-ttu-id="99436-352">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-353">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-354">'Blazor'</span></span>
- <span data-ttu-id="99436-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-355">'Identity'</span></span>
- <span data-ttu-id="99436-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-357">'Razor'</span></span>
- <span data-ttu-id="99436-358">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-359">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-360">'Blazor'</span></span>
- <span data-ttu-id="99436-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-361">'Identity'</span></span>
- <span data-ttu-id="99436-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-363">'Razor'</span></span>
- <span data-ttu-id="99436-364">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-365">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-366">'Blazor'</span></span>
- <span data-ttu-id="99436-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-367">'Identity'</span></span>
- <span data-ttu-id="99436-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-369">'Razor'</span></span>
- <span data-ttu-id="99436-370">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-371">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-372">'Blazor'</span></span>
- <span data-ttu-id="99436-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-373">'Identity'</span></span>
- <span data-ttu-id="99436-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-375">'Razor'</span></span>
- <span data-ttu-id="99436-376">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-377">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-378">'Blazor'</span></span>
- <span data-ttu-id="99436-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-379">'Identity'</span></span>
- <span data-ttu-id="99436-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-381">'Razor'</span></span>
- <span data-ttu-id="99436-382">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-383">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-384">'Blazor'</span></span>
- <span data-ttu-id="99436-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-385">'Identity'</span></span>
- <span data-ttu-id="99436-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-387">'Razor'</span></span>
- <span data-ttu-id="99436-388">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-389">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-390">'Blazor'</span></span>
- <span data-ttu-id="99436-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-391">'Identity'</span></span>
- <span data-ttu-id="99436-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-393">'Razor'</span></span>
- <span data-ttu-id="99436-394">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-395">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-396">'Blazor'</span></span>
- <span data-ttu-id="99436-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-397">'Identity'</span></span>
- <span data-ttu-id="99436-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-399">'Razor'</span></span>
- <span data-ttu-id="99436-400">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-401">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-402">'Blazor'</span></span>
- <span data-ttu-id="99436-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-403">'Identity'</span></span>
- <span data-ttu-id="99436-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-405">'Razor'</span></span>
- <span data-ttu-id="99436-406">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-407">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-408">'Blazor'</span></span>
- <span data-ttu-id="99436-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-409">'Identity'</span></span>
- <span data-ttu-id="99436-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-411">'Razor'</span></span>
- <span data-ttu-id="99436-412">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-413">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-414">'Blazor'</span></span>
- <span data-ttu-id="99436-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-415">'Identity'</span></span>
- <span data-ttu-id="99436-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-417">'Razor'</span></span>
- <span data-ttu-id="99436-418">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-419">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-420">'Blazor'</span></span>
- <span data-ttu-id="99436-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-421">'Identity'</span></span>
- <span data-ttu-id="99436-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-423">'Razor'</span></span>
- <span data-ttu-id="99436-424">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-425">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-426">'Blazor'</span></span>
- <span data-ttu-id="99436-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-427">'Identity'</span></span>
- <span data-ttu-id="99436-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-429">'Razor'</span></span>
- <span data-ttu-id="99436-430">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-431">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-432">'Blazor'</span></span>
- <span data-ttu-id="99436-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-433">'Identity'</span></span>
- <span data-ttu-id="99436-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-435">'Razor'</span></span>
- <span data-ttu-id="99436-436">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-437">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-438">'Blazor'</span></span>
- <span data-ttu-id="99436-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-439">'Identity'</span></span>
- <span data-ttu-id="99436-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-441">'Razor'</span></span>
- <span data-ttu-id="99436-442">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-443">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-444">'Blazor'</span></span>
- <span data-ttu-id="99436-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-445">'Identity'</span></span>
- <span data-ttu-id="99436-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-447">'Razor'</span></span>
- <span data-ttu-id="99436-448">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-449">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-450">'Blazor'</span></span>
- <span data-ttu-id="99436-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-451">'Identity'</span></span>
- <span data-ttu-id="99436-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-453">'Razor'</span></span>
- <span data-ttu-id="99436-454">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-455">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-456">'Blazor'</span></span>
- <span data-ttu-id="99436-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-457">'Identity'</span></span>
- <span data-ttu-id="99436-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-459">'Razor'</span></span>
- <span data-ttu-id="99436-460">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-461">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-462">'Blazor'</span></span>
- <span data-ttu-id="99436-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-463">'Identity'</span></span>
- <span data-ttu-id="99436-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-465">'Razor'</span></span>
- <span data-ttu-id="99436-466">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-467">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-468">'Blazor'</span></span>
- <span data-ttu-id="99436-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-469">'Identity'</span></span>
- <span data-ttu-id="99436-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-471">'Razor'</span></span>
- <span data-ttu-id="99436-472">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-473">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-474">'Blazor'</span></span>
- <span data-ttu-id="99436-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-475">'Identity'</span></span>
- <span data-ttu-id="99436-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-477">'Razor'</span></span>
- <span data-ttu-id="99436-478">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-478">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-479">----------------------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-480">'Blazor'</span></span>
- <span data-ttu-id="99436-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-481">'Identity'</span></span>
- <span data-ttu-id="99436-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-483">'Razor'</span></span>
- <span data-ttu-id="99436-484">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-485">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-486">'Blazor'</span></span>
- <span data-ttu-id="99436-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-487">'Identity'</span></span>
- <span data-ttu-id="99436-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-489">'Razor'</span></span>
- <span data-ttu-id="99436-490">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-490">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-491">---- | | [Âge](https://tools.ietf.org/html/rfc7234#section-5.1) | Estimation de la durée en secondes depuis que la réponse a été générée ou validée sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="99436-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="99436-492">| | [Expire](https://tools.ietf.org/html/rfc7234#section-5.3) | Heure après laquelle la réponse est considérée comme périmée.</span><span class="sxs-lookup"><span data-stu-id="99436-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="99436-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Existe pour la compatibilité descendante avec les caches HTTP/1.0 pour la définition du `no-cache` comportement.</span><span class="sxs-lookup"><span data-stu-id="99436-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="99436-494">Si l' `Cache-Control` en-tête est présent, l' `Pragma` en-tête est ignoré.</span><span class="sxs-lookup"><span data-stu-id="99436-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="99436-495">| | [Variation](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Spécifie qu’une réponse mise en cache ne doit pas être envoyée, sauf si tous les `Vary` champs d’en-tête correspondent à la fois à la demande d’origine de la réponse mise en cache et à la nouvelle requête.</span><span class="sxs-lookup"><span data-stu-id="99436-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="99436-496">La mise en cache basée sur HTTP respecte les directives de contrôle de cache de demande</span><span class="sxs-lookup"><span data-stu-id="99436-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="99436-497">La [spécification de mise en cache HTTP 1,1 pour l’en-tête Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requiert un cache pour honorer un `Cache-Control` en-tête valide envoyé par le client.</span><span class="sxs-lookup"><span data-stu-id="99436-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="99436-498">Un client peut faire des demandes avec une `no-cache` valeur d’en-tête et forcer le serveur à générer une nouvelle réponse pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="99436-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="99436-499">L’Honoring `Cache-Control` des en-têtes de demande client est judicieux si vous considérez l’objectif de la mise en cache http.</span><span class="sxs-lookup"><span data-stu-id="99436-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="99436-500">Dans le cadre de la spécification officielle, la mise en cache est destinée à réduire la latence et la surcharge réseau de la satisfaction des demandes sur un réseau de clients, de proxys et de serveurs.</span><span class="sxs-lookup"><span data-stu-id="99436-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="99436-501">Il ne s’agit pas nécessairement d’un moyen de contrôler la charge sur un serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="99436-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="99436-502">Il n’existe aucun contrôle de développeur sur ce comportement de mise en cache lors de l’utilisation de l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware) , car celui-ci adhère à la spécification officielle de mise en cache.</span><span class="sxs-lookup"><span data-stu-id="99436-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="99436-503">[Les améliorations prévues pour l’intergiciel (middleware](https://github.com/dotnet/AspNetCore/issues/2612) ) permettent de configurer l’intergiciel afin d’ignorer l' `Cache-Control` en-tête d’une demande lorsque vous décidez de traiter une réponse mise en cache.</span><span class="sxs-lookup"><span data-stu-id="99436-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="99436-504">Les améliorations planifiées permettent de mieux contrôler la charge du serveur.</span><span class="sxs-lookup"><span data-stu-id="99436-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="99436-505">Autres technologies de mise en cache dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99436-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="99436-506">Mise en cache en mémoire</span><span class="sxs-lookup"><span data-stu-id="99436-506">In-memory caching</span></span>

<span data-ttu-id="99436-507">La mise en cache en mémoire utilise la mémoire du serveur pour stocker les données mises en cache.</span><span class="sxs-lookup"><span data-stu-id="99436-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="99436-508">Ce type de mise en cache convient pour un serveur unique ou plusieurs serveurs à l’aide de *sessions rémanentes*.</span><span class="sxs-lookup"><span data-stu-id="99436-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="99436-509">Les sessions rémanentes signifient que les demandes d’un client sont toujours routées vers le même serveur pour traitement.</span><span class="sxs-lookup"><span data-stu-id="99436-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="99436-510">Pour plus d'informations, consultez <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="99436-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="99436-511">Cache distribué</span><span class="sxs-lookup"><span data-stu-id="99436-511">Distributed Cache</span></span>

<span data-ttu-id="99436-512">Utilisez un cache distribué pour stocker les données en mémoire lorsque l’application est hébergée dans une batterie de serveurs ou un Cloud.</span><span class="sxs-lookup"><span data-stu-id="99436-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="99436-513">Le cache est partagé entre les serveurs qui traitent les demandes.</span><span class="sxs-lookup"><span data-stu-id="99436-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="99436-514">Un client peut soumettre une demande gérée par n’importe quel serveur du groupe si les données mises en cache pour le client sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="99436-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="99436-515">ASP.NET Core fonctionne avec les caches distribués SQL Server, [ReDim](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)et [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="99436-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="99436-516">Pour plus d'informations, consultez <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="99436-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="99436-517">Tag Helper de cache</span><span class="sxs-lookup"><span data-stu-id="99436-517">Cache Tag Helper</span></span>

<span data-ttu-id="99436-518">Mettez en cache le contenu à partir d’une vue ou d’une Razor page MVC avec le tag Helper cache.</span><span class="sxs-lookup"><span data-stu-id="99436-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="99436-519">Le tag Helper cache utilise la mise en cache en mémoire pour stocker les données.</span><span class="sxs-lookup"><span data-stu-id="99436-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="99436-520">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="99436-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="99436-521">Tag Helper Cache distribué</span><span class="sxs-lookup"><span data-stu-id="99436-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="99436-522">Mettez en cache le contenu à partir d’une vue ou d’une Razor page MVC dans des scénarios de batterie de serveurs Web ou de Cloud distribué avec le tag Helper de cache distribué.</span><span class="sxs-lookup"><span data-stu-id="99436-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="99436-523">Le tag Helper de cache distribué utilise SQL Server, les [redims](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)ou [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) pour stocker des données.</span><span class="sxs-lookup"><span data-stu-id="99436-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="99436-524">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="99436-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="99436-525">Attribut ResponseCache</span><span class="sxs-lookup"><span data-stu-id="99436-525">ResponseCache attribute</span></span>

<span data-ttu-id="99436-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Spécifie les paramètres nécessaires à la définition des en-têtes appropriés dans la mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="99436-527">Désactivez la mise en cache pour le contenu qui contient des informations pour les clients authentifiés.</span><span class="sxs-lookup"><span data-stu-id="99436-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="99436-528">La mise en cache ne doit être activée que pour le contenu qui ne change pas en fonction de l’identité d’un utilisateur ou si un utilisateur est connecté.</span><span class="sxs-lookup"><span data-stu-id="99436-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="99436-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>fait varier la réponse stockée en fonction des valeurs de la liste donnée des clés de requête.</span><span class="sxs-lookup"><span data-stu-id="99436-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="99436-530">Quand une seule valeur `*` est fournie, l’intergiciel (middleware) varie les réponses par tous les paramètres de chaîne de requête de la demande.</span><span class="sxs-lookup"><span data-stu-id="99436-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="99436-531">L' [intergiciel de mise en cache des réponses](xref:performance/caching/middleware) doit être activé pour définir la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriété.</span><span class="sxs-lookup"><span data-stu-id="99436-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="99436-532">Dans le cas contraire, une exception Runtime est levée.</span><span class="sxs-lookup"><span data-stu-id="99436-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="99436-533">Il n’existe pas d’en-tête HTTP correspondant pour la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propriété.</span><span class="sxs-lookup"><span data-stu-id="99436-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="99436-534">La propriété est une fonctionnalité HTTP gérée par l’intergiciel (middleware) de mise en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="99436-535">Pour que l’intergiciel serve une réponse mise en cache, la chaîne de requête et la valeur de chaîne de requête doivent correspondre à une demande précédente.</span><span class="sxs-lookup"><span data-stu-id="99436-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="99436-536">Par exemple, considérez la séquence de requêtes et les résultats présentés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="99436-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="99436-537">Requête</span><span class="sxs-lookup"><span data-stu-id="99436-537">Request</span></span>                          | <span data-ttu-id="99436-538">Résultat</span><span class="sxs-lookup"><span data-stu-id="99436-538">Result</span></span>                    |
| ---
<span data-ttu-id="99436-539">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-540">'Blazor'</span></span>
- <span data-ttu-id="99436-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-541">'Identity'</span></span>
- <span data-ttu-id="99436-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-543">'Razor'</span></span>
- <span data-ttu-id="99436-544">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-545">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-546">'Blazor'</span></span>
- <span data-ttu-id="99436-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-547">'Identity'</span></span>
- <span data-ttu-id="99436-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-549">'Razor'</span></span>
- <span data-ttu-id="99436-550">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-551">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-552">'Blazor'</span></span>
- <span data-ttu-id="99436-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-553">'Identity'</span></span>
- <span data-ttu-id="99436-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-555">'Razor'</span></span>
- <span data-ttu-id="99436-556">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-557">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-558">'Blazor'</span></span>
- <span data-ttu-id="99436-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-559">'Identity'</span></span>
- <span data-ttu-id="99436-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-561">'Razor'</span></span>
- <span data-ttu-id="99436-562">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-563">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-564">'Blazor'</span></span>
- <span data-ttu-id="99436-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-565">'Identity'</span></span>
- <span data-ttu-id="99436-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-567">'Razor'</span></span>
- <span data-ttu-id="99436-568">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-569">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-570">'Blazor'</span></span>
- <span data-ttu-id="99436-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-571">'Identity'</span></span>
- <span data-ttu-id="99436-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-573">'Razor'</span></span>
- <span data-ttu-id="99436-574">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-575">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-576">'Blazor'</span></span>
- <span data-ttu-id="99436-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-577">'Identity'</span></span>
- <span data-ttu-id="99436-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-579">'Razor'</span></span>
- <span data-ttu-id="99436-580">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-581">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-582">'Blazor'</span></span>
- <span data-ttu-id="99436-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-583">'Identity'</span></span>
- <span data-ttu-id="99436-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-585">'Razor'</span></span>
- <span data-ttu-id="99436-586">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-587">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-588">'Blazor'</span></span>
- <span data-ttu-id="99436-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-589">'Identity'</span></span>
- <span data-ttu-id="99436-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-591">'Razor'</span></span>
- <span data-ttu-id="99436-592">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-593">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-594">'Blazor'</span></span>
- <span data-ttu-id="99436-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-595">'Identity'</span></span>
- <span data-ttu-id="99436-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-597">'Razor'</span></span>
- <span data-ttu-id="99436-598">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-599">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-600">'Blazor'</span></span>
- <span data-ttu-id="99436-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-601">'Identity'</span></span>
- <span data-ttu-id="99436-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-603">'Razor'</span></span>
- <span data-ttu-id="99436-604">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-605">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-606">'Blazor'</span></span>
- <span data-ttu-id="99436-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-607">'Identity'</span></span>
- <span data-ttu-id="99436-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-609">'Razor'</span></span>
- <span data-ttu-id="99436-610">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-611">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-612">'Blazor'</span></span>
- <span data-ttu-id="99436-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-613">'Identity'</span></span>
- <span data-ttu-id="99436-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-615">'Razor'</span></span>
- <span data-ttu-id="99436-616">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-617">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-618">'Blazor'</span></span>
- <span data-ttu-id="99436-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-619">'Identity'</span></span>
- <span data-ttu-id="99436-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-621">'Razor'</span></span>
- <span data-ttu-id="99436-622">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-622">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-623">---------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-624">'Blazor'</span></span>
- <span data-ttu-id="99436-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-625">'Identity'</span></span>
- <span data-ttu-id="99436-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-627">'Razor'</span></span>
- <span data-ttu-id="99436-628">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-629">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-630">'Blazor'</span></span>
- <span data-ttu-id="99436-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-631">'Identity'</span></span>
- <span data-ttu-id="99436-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-633">'Razor'</span></span>
- <span data-ttu-id="99436-634">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-635">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-636">'Blazor'</span></span>
- <span data-ttu-id="99436-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-637">'Identity'</span></span>
- <span data-ttu-id="99436-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-639">'Razor'</span></span>
- <span data-ttu-id="99436-640">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-641">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-642">'Blazor'</span></span>
- <span data-ttu-id="99436-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-643">'Identity'</span></span>
- <span data-ttu-id="99436-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-645">'Razor'</span></span>
- <span data-ttu-id="99436-646">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-647">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-648">'Blazor'</span></span>
- <span data-ttu-id="99436-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-649">'Identity'</span></span>
- <span data-ttu-id="99436-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-651">'Razor'</span></span>
- <span data-ttu-id="99436-652">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-653">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-654">'Blazor'</span></span>
- <span data-ttu-id="99436-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-655">'Identity'</span></span>
- <span data-ttu-id="99436-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-657">'Razor'</span></span>
- <span data-ttu-id="99436-658">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-659">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-660">'Blazor'</span></span>
- <span data-ttu-id="99436-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-661">'Identity'</span></span>
- <span data-ttu-id="99436-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-663">'Razor'</span></span>
- <span data-ttu-id="99436-664">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-665">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-666">'Blazor'</span></span>
- <span data-ttu-id="99436-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-667">'Identity'</span></span>
- <span data-ttu-id="99436-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-669">'Razor'</span></span>
- <span data-ttu-id="99436-670">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-671">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-672">'Blazor'</span></span>
- <span data-ttu-id="99436-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-673">'Identity'</span></span>
- <span data-ttu-id="99436-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-675">'Razor'</span></span>
- <span data-ttu-id="99436-676">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="99436-677">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="99436-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="99436-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="99436-678">'Blazor'</span></span>
- <span data-ttu-id="99436-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="99436-679">'Identity'</span></span>
- <span data-ttu-id="99436-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="99436-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="99436-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="99436-681">'Razor'</span></span>
- <span data-ttu-id="99436-682">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="99436-682">'SignalR' uid:</span></span> 

<span data-ttu-id="99436-683">------------- | | `http://example.com?key1=value1` | Retourné à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="99436-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="99436-684">| | `http://example.com?key1=value1` | Renvoyé par l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="99436-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="99436-685">| | `http://example.com?key1=value2` | Retourné à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="99436-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="99436-686">La première demande est retournée par le serveur et mise en cache dans l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="99436-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="99436-687">La deuxième demande est retournée par l’intergiciel (middleware), car la chaîne de requête correspond à la requête précédente.</span><span class="sxs-lookup"><span data-stu-id="99436-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="99436-688">La troisième demande n’est pas dans le cache de l’intergiciel (middleware), car la valeur de la chaîne de requête ne correspond pas à une demande précédente.</span><span class="sxs-lookup"><span data-stu-id="99436-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="99436-689">Le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> est utilisé pour configurer et créer (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) un `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="99436-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="99436-690">Le `ResponseCacheFilter` effectue le travail de mise à jour des en-têtes et des fonctionnalités http appropriées de la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="99436-691">Le filtre :</span><span class="sxs-lookup"><span data-stu-id="99436-691">The filter:</span></span>

* <span data-ttu-id="99436-692">Supprime tous les en-têtes existants pour `Vary` , `Cache-Control` et `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="99436-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="99436-693">Écrit les en-têtes appropriés en fonction des propriétés définies dans le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="99436-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="99436-694">Met à jour la fonctionnalité HTTP de mise en cache des réponses si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> est défini.</span><span class="sxs-lookup"><span data-stu-id="99436-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="99436-695">Varier</span><span class="sxs-lookup"><span data-stu-id="99436-695">Vary</span></span>

<span data-ttu-id="99436-696">Cet en-tête est écrit uniquement lorsque la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriété est définie.</span><span class="sxs-lookup"><span data-stu-id="99436-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="99436-697">Propriété définie sur la `Vary` valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="99436-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="99436-698">L’exemple suivant utilise la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriété :</span><span class="sxs-lookup"><span data-stu-id="99436-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="99436-699">À l’aide de l’exemple d’application, affichez les en-têtes de réponse avec les outils réseau du navigateur.</span><span class="sxs-lookup"><span data-stu-id="99436-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="99436-700">Les en-têtes de réponse suivants sont envoyés avec la réponse de la page Cache1 :</span><span class="sxs-lookup"><span data-stu-id="99436-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="99436-701">NoStore et location. None</span><span class="sxs-lookup"><span data-stu-id="99436-701">NoStore and Location.None</span></span>

<span data-ttu-id="99436-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>remplace la plupart des autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="99436-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="99436-703">Quand cette propriété a la valeur `true` , l' `Cache-Control` en-tête a la valeur `no-store` .</span><span class="sxs-lookup"><span data-stu-id="99436-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="99436-704">Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> a la valeur `None` :</span><span class="sxs-lookup"><span data-stu-id="99436-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="99436-705">`Cache-Control` est défini sur `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="99436-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="99436-706">`Pragma` est défini sur `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="99436-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="99436-707">Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> est `false` et <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> est `None` , et `Cache-Control` ont la `Pragma` valeur `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="99436-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="99436-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>est généralement défini sur `true` pour les pages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="99436-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="99436-709">La page cache2 de l’exemple d’application génère des en-têtes de réponse qui indiquent au client de ne pas stocker la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="99436-710">L’exemple d’application retourne la page cache2 avec les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="99436-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="99436-711">Emplacement et durée</span><span class="sxs-lookup"><span data-stu-id="99436-711">Location and Duration</span></span>

<span data-ttu-id="99436-712">Pour activer la mise en cache, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> doit être défini sur une valeur positive et <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> doit être `Any` (valeur par défaut) ou `Client` .</span><span class="sxs-lookup"><span data-stu-id="99436-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="99436-713">L’infrastructure affecte `Cache-Control` à l’en-tête la valeur d’emplacement suivie du `max-age` de la réponse.</span><span class="sxs-lookup"><span data-stu-id="99436-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="99436-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>les options de `Any` et se `Client` traduisent en `Cache-Control` valeurs d’en-tête de `public` et `private` , respectivement.</span><span class="sxs-lookup"><span data-stu-id="99436-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="99436-715">Comme indiqué dans la section [NoStore et location. None](#nostore-and-locationnone) , le paramètre <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> pour `None` définit les deux `Cache-Control` et `Pragma` en-têtes sur `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="99436-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="99436-716">`Location.Any`( `Cache-Control` défini sur `public` ) indique que le *client ou un proxy intermédiaire* peut mettre en cache la valeur, y compris l' [intergiciel de mise en](xref:performance/caching/middleware)cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="99436-717">`Location.Client`( `Cache-Control` défini sur `private` ) indique que *seul le client* peut mettre en cache la valeur.</span><span class="sxs-lookup"><span data-stu-id="99436-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="99436-718">Aucun cache intermédiaire ne doit mettre en cache la valeur, y compris l' [intergiciel de mise en](xref:performance/caching/middleware)cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="99436-719">Les en-têtes de contrôle de cache fournissent simplement des conseils aux clients et aux proxies intermédiaires quand et comment mettre en cache des réponses.</span><span class="sxs-lookup"><span data-stu-id="99436-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="99436-720">Il n’y a aucune garantie que les clients et les proxys honoreront la [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="99436-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="99436-721">L’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware) suit toujours les règles de mise en cache établies par la spécification.</span><span class="sxs-lookup"><span data-stu-id="99436-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="99436-722">L’exemple suivant montre le modèle de page cache3 de l’exemple d’application et les en-têtes produits par la définition <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> et la conservation de la valeur par défaut <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> :</span><span class="sxs-lookup"><span data-stu-id="99436-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="99436-723">L’exemple d’application retourne la page cache3 avec l’en-tête suivant :</span><span class="sxs-lookup"><span data-stu-id="99436-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="99436-724">Profils de cache</span><span class="sxs-lookup"><span data-stu-id="99436-724">Cache profiles</span></span>

<span data-ttu-id="99436-725">Au lieu de dupliquer les paramètres du cache de réponse sur de nombreux attributs d’action de contrôleur, les profils de cache peuvent être configurés en tant qu’options lors de la configuration de MVC/ Razor pages dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="99436-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99436-726">Les valeurs trouvées dans un profil de cache référencé sont utilisées comme valeurs par défaut par <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> et sont remplacées par les propriétés spécifiées sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="99436-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="99436-727">Configurez un profil de cache.</span><span class="sxs-lookup"><span data-stu-id="99436-727">Set up a cache profile.</span></span> <span data-ttu-id="99436-728">L’exemple suivant montre un profil de cache de 30 secondes dans l’exemple d’application `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="99436-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="99436-729">Le modèle de page cache4 de l’exemple d’application fait référence au `Default30` profil de cache :</span><span class="sxs-lookup"><span data-stu-id="99436-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="99436-730">Le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> peut être appliqué aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="99436-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="99436-731">Gestionnaires de page (classes) : les attributs ne peuvent pas être appliqués aux méthodes de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="99436-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="99436-732">Contrôleurs MVC (classes).</span><span class="sxs-lookup"><span data-stu-id="99436-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="99436-733">Actions MVC (méthodes) : les attributs au niveau de la méthode remplacent les paramètres spécifiés dans les attributs de niveau classe.</span><span class="sxs-lookup"><span data-stu-id="99436-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="99436-734">En-tête résultant appliqué à la réponse de la page cache4 par le `Default30` profil de cache :</span><span class="sxs-lookup"><span data-stu-id="99436-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="99436-735">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="99436-735">Additional resources</span></span>

* [<span data-ttu-id="99436-736">Stockage des réponses dans les caches</span><span class="sxs-lookup"><span data-stu-id="99436-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="99436-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="99436-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
