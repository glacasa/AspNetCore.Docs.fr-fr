---
<span data-ttu-id="f3eb6-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-102">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-103">'Identity'</span></span>
- <span data-ttu-id="f3eb6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-105">'Razor'</span></span>
- <span data-ttu-id="f3eb6-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="f3eb6-107">Comparer les services gRPC avec les API HTTP</span><span class="sxs-lookup"><span data-stu-id="f3eb6-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="f3eb6-108">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f3eb6-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f3eb6-109">Cet article explique comment les [services gRPC](https://grpc.io/docs/guides/) sont comparés aux API http avec JSON (y compris les [API Web](xref:web-api/index)ASP.net Core).</span><span class="sxs-lookup"><span data-stu-id="f3eb6-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="f3eb6-110">La technologie utilisée pour fournir une API pour votre application est un choix important, et gRPC offre des avantages uniques par rapport aux API HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="f3eb6-111">Cet article présente les forces et les faiblesses de gRPC et recommande des scénarios d’utilisation de gRPC sur d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="f3eb6-112">Comparaison de haut niveau</span><span class="sxs-lookup"><span data-stu-id="f3eb6-112">High-level comparison</span></span>

<span data-ttu-id="f3eb6-113">Le tableau suivant présente une comparaison de haut niveau des fonctionnalités entre les API gRPC et HTTP avec JSON.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="f3eb6-114">Fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="f3eb6-114">Feature</span></span>          | <span data-ttu-id="f3eb6-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="f3eb6-115">gRPC</span></span>                                               | <span data-ttu-id="f3eb6-116">API HTTP avec JSON</span><span class="sxs-lookup"><span data-stu-id="f3eb6-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="f3eb6-117">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-118">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-119">'Identity'</span></span>
- <span data-ttu-id="f3eb6-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-121">'Razor'</span></span>
- <span data-ttu-id="f3eb6-122">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-123">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-124">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-125">'Identity'</span></span>
- <span data-ttu-id="f3eb6-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-127">'Razor'</span></span>
- <span data-ttu-id="f3eb6-128">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-129">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-130">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-131">'Identity'</span></span>
- <span data-ttu-id="f3eb6-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-133">'Razor'</span></span>
- <span data-ttu-id="f3eb6-134">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-135">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-136">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-137">'Identity'</span></span>
- <span data-ttu-id="f3eb6-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-139">'Razor'</span></span>
- <span data-ttu-id="f3eb6-140">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-141">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-142">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-143">'Identity'</span></span>
- <span data-ttu-id="f3eb6-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-145">'Razor'</span></span>
- <span data-ttu-id="f3eb6-146">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-147">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-148">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-149">'Identity'</span></span>
- <span data-ttu-id="f3eb6-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-151">'Razor'</span></span>
- <span data-ttu-id="f3eb6-152">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-152">'SignalR' uid:</span></span> 

<span data-ttu-id="f3eb6-153">-------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-154">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-155">'Identity'</span></span>
- <span data-ttu-id="f3eb6-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-157">'Razor'</span></span>
- <span data-ttu-id="f3eb6-158">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-159">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-160">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-161">'Identity'</span></span>
- <span data-ttu-id="f3eb6-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-163">'Razor'</span></span>
- <span data-ttu-id="f3eb6-164">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-165">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-166">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-167">'Identity'</span></span>
- <span data-ttu-id="f3eb6-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-169">'Razor'</span></span>
- <span data-ttu-id="f3eb6-170">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-171">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-172">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-173">'Identity'</span></span>
- <span data-ttu-id="f3eb6-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-175">'Razor'</span></span>
- <span data-ttu-id="f3eb6-176">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-177">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-178">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-179">'Identity'</span></span>
- <span data-ttu-id="f3eb6-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-181">'Razor'</span></span>
- <span data-ttu-id="f3eb6-182">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-183">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-184">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-185">'Identity'</span></span>
- <span data-ttu-id="f3eb6-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-187">'Razor'</span></span>
- <span data-ttu-id="f3eb6-188">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-189">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-190">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-191">'Identity'</span></span>
- <span data-ttu-id="f3eb6-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-193">'Razor'</span></span>
- <span data-ttu-id="f3eb6-194">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-195">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-196">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-197">'Identity'</span></span>
- <span data-ttu-id="f3eb6-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-199">'Razor'</span></span>
- <span data-ttu-id="f3eb6-200">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-201">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-202">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-203">'Identity'</span></span>
- <span data-ttu-id="f3eb6-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-205">'Razor'</span></span>
- <span data-ttu-id="f3eb6-206">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-207">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-208">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-209">'Identity'</span></span>
- <span data-ttu-id="f3eb6-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-211">'Razor'</span></span>
- <span data-ttu-id="f3eb6-212">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-213">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-214">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-215">'Identity'</span></span>
- <span data-ttu-id="f3eb6-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-217">'Razor'</span></span>
- <span data-ttu-id="f3eb6-218">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-219">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-220">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-221">'Identity'</span></span>
- <span data-ttu-id="f3eb6-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-223">'Razor'</span></span>
- <span data-ttu-id="f3eb6-224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-225">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-226">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-227">'Identity'</span></span>
- <span data-ttu-id="f3eb6-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-229">'Razor'</span></span>
- <span data-ttu-id="f3eb6-230">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-231">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-232">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-233">'Identity'</span></span>
- <span data-ttu-id="f3eb6-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-235">'Razor'</span></span>
- <span data-ttu-id="f3eb6-236">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-237">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-238">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-239">'Identity'</span></span>
- <span data-ttu-id="f3eb6-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-241">'Razor'</span></span>
- <span data-ttu-id="f3eb6-242">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-243">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-244">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-245">'Identity'</span></span>
- <span data-ttu-id="f3eb6-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-247">'Razor'</span></span>
- <span data-ttu-id="f3eb6-248">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-249">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-250">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-251">'Identity'</span></span>
- <span data-ttu-id="f3eb6-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-253">'Razor'</span></span>
- <span data-ttu-id="f3eb6-254">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-255">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-256">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-257">'Identity'</span></span>
- <span data-ttu-id="f3eb6-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-259">'Razor'</span></span>
- <span data-ttu-id="f3eb6-260">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-261">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-262">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-263">'Identity'</span></span>
- <span data-ttu-id="f3eb6-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-265">'Razor'</span></span>
- <span data-ttu-id="f3eb6-266">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-267">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-268">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-269">'Identity'</span></span>
- <span data-ttu-id="f3eb6-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-271">'Razor'</span></span>
- <span data-ttu-id="f3eb6-272">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-273">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-274">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-275">'Identity'</span></span>
- <span data-ttu-id="f3eb6-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-277">'Razor'</span></span>
- <span data-ttu-id="f3eb6-278">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-279">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-280">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-281">'Identity'</span></span>
- <span data-ttu-id="f3eb6-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-283">'Razor'</span></span>
- <span data-ttu-id="f3eb6-284">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-285">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-286">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-287">'Identity'</span></span>
- <span data-ttu-id="f3eb6-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-289">'Razor'</span></span>
- <span data-ttu-id="f3eb6-290">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-290">'SignalR' uid:</span></span> 

<span data-ttu-id="f3eb6-291">------------------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-292">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-293">'Identity'</span></span>
- <span data-ttu-id="f3eb6-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-295">'Razor'</span></span>
- <span data-ttu-id="f3eb6-296">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-297">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-298">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-299">'Identity'</span></span>
- <span data-ttu-id="f3eb6-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-301">'Razor'</span></span>
- <span data-ttu-id="f3eb6-302">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-303">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-304">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-305">'Identity'</span></span>
- <span data-ttu-id="f3eb6-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-307">'Razor'</span></span>
- <span data-ttu-id="f3eb6-308">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-309">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-310">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-311">'Identity'</span></span>
- <span data-ttu-id="f3eb6-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-313">'Razor'</span></span>
- <span data-ttu-id="f3eb6-314">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-315">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-316">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-317">'Identity'</span></span>
- <span data-ttu-id="f3eb6-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-319">'Razor'</span></span>
- <span data-ttu-id="f3eb6-320">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-321">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-322">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-323">'Identity'</span></span>
- <span data-ttu-id="f3eb6-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-325">'Razor'</span></span>
- <span data-ttu-id="f3eb6-326">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-327">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-328">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-329">'Identity'</span></span>
- <span data-ttu-id="f3eb6-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-331">'Razor'</span></span>
- <span data-ttu-id="f3eb6-332">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-333">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-334">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-335">'Identity'</span></span>
- <span data-ttu-id="f3eb6-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-337">'Razor'</span></span>
- <span data-ttu-id="f3eb6-338">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-339">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-340">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-341">'Identity'</span></span>
- <span data-ttu-id="f3eb6-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-343">'Razor'</span></span>
- <span data-ttu-id="f3eb6-344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-345">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-346">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-347">'Identity'</span></span>
- <span data-ttu-id="f3eb6-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-349">'Razor'</span></span>
- <span data-ttu-id="f3eb6-350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-351">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-352">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-353">'Identity'</span></span>
- <span data-ttu-id="f3eb6-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-355">'Razor'</span></span>
- <span data-ttu-id="f3eb6-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f3eb6-357">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3eb6-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-358">'Blazor'</span></span>
- <span data-ttu-id="f3eb6-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-359">'Identity'</span></span>
- <span data-ttu-id="f3eb6-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3eb6-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3eb6-361">'Razor'</span></span>
- <span data-ttu-id="f3eb6-362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-362">'SignalR' uid:</span></span> 

<span data-ttu-id="f3eb6-363">--------------- | | Contrat | Obligatoire (*. proto*) | Facultatif (OpenAPI) | | Protocole | HTTP/2 | HTTP | | Charge utile | [Protobuf (petit, binaire)](#performance) | JSON (grand, lisible par l’utilisateur) | | Prescriptiveness | [Spécification stricte](#strict-specification) | Compatibilité.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="f3eb6-364">Tout HTTP est valide.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-364">Any HTTP is valid.</span></span>     <span data-ttu-id="f3eb6-365">| | Diffusion en continu | [Client, serveur, bidirectionnel](#streaming) | Client, serveur | | Prise en charge du navigateur | [Non (requiert GRPC-Web)](#limited-browser-support) | Oui | | Sécurité | Transport (TLS) | Transport (TLS) | | Génération de code client | [Oui](#code-generation) | OpenAPI + outils tiers |</span><span class="sxs-lookup"><span data-stu-id="f3eb6-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="f3eb6-366">points forts gRPC</span><span class="sxs-lookup"><span data-stu-id="f3eb6-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="f3eb6-367">Performances</span><span class="sxs-lookup"><span data-stu-id="f3eb6-367">Performance</span></span>

<span data-ttu-id="f3eb6-368">les messages gRPC sont sérialisés à l’aide de [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), un format de message binaire efficace.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="f3eb6-369">Protobuf sérialise très rapidement sur le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="f3eb6-370">La sérialisation Protobuf génère des messages de petite taille, importants dans des scénarios de bande passante limitée, tels que les applications mobiles.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="f3eb6-371">gRPC est conçu pour HTTP/2, une révision majeure de HTTP qui offre des avantages significatifs en matière de performances par rapport à HTTP 1. x :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="f3eb6-372">Tramage et compression binaires.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-372">Binary framing and compression.</span></span> <span data-ttu-id="f3eb6-373">Le protocole HTTP/2 est compact et efficace à la fois pour l’envoi et la réception.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="f3eb6-374">Multiplexage de plusieurs appels HTTP/2 sur une seule connexion TCP.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="f3eb6-375">Le multiplexage élimine [le blocage en tête de ligne](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="f3eb6-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="f3eb6-376">HTTP/2 n’est pas exclusif à gRPC.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="f3eb6-377">De nombreux types de demandes, y compris les API HTTP avec JSON, peuvent utiliser le protocole HTTP/2 et tirer parti de ses améliorations en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="f3eb6-378">Génération de code</span><span class="sxs-lookup"><span data-stu-id="f3eb6-378">Code generation</span></span>

<span data-ttu-id="f3eb6-379">Toutes les infrastructures gRPC fournissent une prise en charge de première classe pour la génération de code.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="f3eb6-380">Un fichier de base pour le développement gRPC est le [fichier. proto](https://developers.google.com/protocol-buffers/docs/proto3), qui définit le contrat de services et de messages gRPC.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="f3eb6-381">À partir de ce fichier, gRPC frameworks code générera une classe de base de service, des messages et un client complet.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="f3eb6-382">En partageant le fichier *. proto* entre le serveur et le client, les messages et le code client peuvent être générés de bout en bout.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="f3eb6-383">La génération de code du client élimine la duplication des messages sur le client et le serveur, et crée un client fortement typé pour vous.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="f3eb6-384">Le fait de ne pas avoir à écrire un client permet d’économiser beaucoup de temps de développement dans les applications avec de nombreux services.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="f3eb6-385">Spécification stricte</span><span class="sxs-lookup"><span data-stu-id="f3eb6-385">Strict specification</span></span>

<span data-ttu-id="f3eb6-386">Il n’existe aucune spécification formelle pour l’API HTTP avec JSON.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="f3eb6-387">Les développeurs ont discuté du meilleur format des URL, des verbes HTTP et des codes de réponse.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="f3eb6-388">La [spécification gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) est normative sur le format qu’un service gRPC doit suivre.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="f3eb6-389">gRPC élimine le débat et économise le temps des développeurs, car gRPC est cohérent entre les plateformes et les implémentations.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="f3eb6-390">Diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="f3eb6-390">Streaming</span></span>

<span data-ttu-id="f3eb6-391">HTTP/2 fournit une base pour les flux de communication de longue durée et en temps réel.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="f3eb6-392">gRPC fournit une prise en charge de première classe pour la diffusion via HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="f3eb6-393">Un service gRPC prend en charge toutes les combinaisons de diffusion en continu :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="f3eb6-394">Unaire (aucune diffusion en continu)</span><span class="sxs-lookup"><span data-stu-id="f3eb6-394">Unary (no streaming)</span></span>
* <span data-ttu-id="f3eb6-395">Streaming de serveur à client</span><span class="sxs-lookup"><span data-stu-id="f3eb6-395">Server to client streaming</span></span>
* <span data-ttu-id="f3eb6-396">Diffusion en continu du client vers le serveur</span><span class="sxs-lookup"><span data-stu-id="f3eb6-396">Client to server streaming</span></span>
* <span data-ttu-id="f3eb6-397">Streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="f3eb6-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="f3eb6-398">Échéance/délais d’attente et annulation</span><span class="sxs-lookup"><span data-stu-id="f3eb6-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="f3eb6-399">gRPC permet aux clients de spécifier la durée pendant laquelle ils sont prêts à attendre la fin d’un appel de procédure distante (RPC).</span><span class="sxs-lookup"><span data-stu-id="f3eb6-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="f3eb6-400">L' [échéance](https://grpc.io/blog/deadlines) est envoyée au serveur et le serveur peut décider de l’action à entreprendre en cas de dépassement de l’échéance.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="f3eb6-401">Par exemple, le serveur peut annuler les demandes de gRPC/HTTP/de base de données en cours d’expiration.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="f3eb6-402">La propagation de l’échéance et de l’annulation via les appels gRPC enfants permet d’appliquer les limites d’utilisation des ressources.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="f3eb6-403">scénarios gRPC recommandés</span><span class="sxs-lookup"><span data-stu-id="f3eb6-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="f3eb6-404">gRPC est bien adapté aux scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="f3eb6-405">**Microservices**: gRPC est conçu pour la communication à faible latence et à haut débit.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="f3eb6-406">gRPC est parfait pour les microservices légers où l’efficacité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="f3eb6-407">**Communication en temps réel point à point**: gRPC offre une excellente prise en charge de la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="f3eb6-408">les services gRPC peuvent envoyer des messages en temps réel sans interrogation.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="f3eb6-409">**Environnements polyglotte**: les outils gRPC prennent en charge tous les langages de développement populaires, ce qui fait de gRPC un bon choix pour les environnements multilingues.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="f3eb6-410">**Environnements réseau restreints**: les messages gRPC sont sérialisés avec Protobuf, un format de message léger.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="f3eb6-411">Un message gRPC est toujours plus petit qu’un message JSON équivalent.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="f3eb6-412">faiblesses gRPC</span><span class="sxs-lookup"><span data-stu-id="f3eb6-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="f3eb6-413">Prise en charge limitée du navigateur</span><span class="sxs-lookup"><span data-stu-id="f3eb6-413">Limited browser support</span></span>

<span data-ttu-id="f3eb6-414">Il est impossible d’appeler directement un service gRPC à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="f3eb6-415">gRPC utilise fortement les fonctionnalités HTTP/2 et aucun navigateur ne fournit le niveau de contrôle requis sur les requêtes Web pour prendre en charge un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="f3eb6-416">Par exemple, les navigateurs n’autorisent pas un appelant à exiger que le protocole HTTP/2 soit utilisé ou à fournir l’accès aux frames HTTP/2 sous-jacents.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="f3eb6-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) est une technologie supplémentaire de l’équipe gRPC qui offre une prise en charge limitée des gRPC dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="f3eb6-418">gRPC-Web se compose de deux parties : un client JavaScript qui prend en charge tous les navigateurs modernes et un proxy gRPC-Web sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="f3eb6-419">Le client gRPC-Web appelle le proxy et le proxy transmet les demandes gRPC au serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="f3eb6-420">Les fonctionnalités de gRPC ne sont pas toutes prises en charge par gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="f3eb6-421">Le client et la diffusion bidirectionnelle ne sont pas pris en charge, et la prise en charge de la diffusion de serveur est limitée.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="f3eb6-422">.NET Core offre une prise en charge expérimentale de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="f3eb6-423"><xref:grpc/browser>Pour plus d’informations, consultez.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="f3eb6-424">Non lisible par l’homme</span><span class="sxs-lookup"><span data-stu-id="f3eb6-424">Not human readable</span></span>

<span data-ttu-id="f3eb6-425">Les demandes de l’API HTTP sont envoyées en tant que texte et peuvent être lues et créées par des humains.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="f3eb6-426">par défaut, les messages gRPC sont encodés avec Protobuf.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="f3eb6-427">Bien que Protobuf soit efficace pour l’envoi et la réception, son format binaire n’est pas lisible par l’homme.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="f3eb6-428">Protobuf requiert la description d’interface du message spécifiée dans le fichier *. proto* pour une désérialisation correcte.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="f3eb6-429">Des outils supplémentaires sont requis pour analyser les charges utiles Protobuf sur le réseau et pour composer les demandes manuellement.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="f3eb6-430">Des fonctionnalités telles que la [réflexion de serveur](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) et l’outil en [ligne de commande gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existent pour faciliter les messages binaires Protobuf.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="f3eb6-431">En outre, les messages Protobuf prennent en charge la [conversion vers et à partir de JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="f3eb6-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="f3eb6-432">La conversion JSON intégrée offre un moyen efficace de convertir des messages Protobuf vers et à partir d’une forme lisible par l’utilisateur lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="f3eb6-433">Autres scénarios d’infrastructure</span><span class="sxs-lookup"><span data-stu-id="f3eb6-433">Alternative framework scenarios</span></span>

<span data-ttu-id="f3eb6-434">D’autres infrastructures sont recommandées par rapport à gRPC dans les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="f3eb6-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="f3eb6-435">**API accessibles**par le navigateur : gRPC n’est pas entièrement pris en charge dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="f3eb6-436">gRPC-Web peut offrir la prise en charge des navigateurs, mais il présente des limitations et introduit un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="f3eb6-437">**Communication en temps réel de diffusion**: gRPC prend en charge la communication en temps réel via la diffusion en continu, mais le concept de diffusion d’un message à des connexions inscrites n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="f3eb6-438">Par exemple, dans un scénario de salle de conversation dans lequel de nouveaux messages de conversation doivent être envoyés à tous les clients dans la salle de conversation, chaque appel gRPC est requis pour diffuser individuellement de nouveaux messages de conversation au client.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="f3eb6-439">[SignalR](xref:signalr/introduction)est une infrastructure utile pour ce scénario.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="f3eb6-440">dispose du concept de connexions persistantes et de la prise en charge intégrée de la diffusion des messages.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="f3eb6-441">**Communication entre processus**: un processus doit héberger un serveur http/2 pour accepter les appels gRPC entrants.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="f3eb6-442">Pour Windows, les [canaux](/dotnet/standard/io/pipe-operations) de communication entre processus sont une méthode de communication rapide et légère.</span><span class="sxs-lookup"><span data-stu-id="f3eb6-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3eb6-443">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f3eb6-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
