---
<span data-ttu-id="56d58-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-102">'Blazor'</span></span>
- <span data-ttu-id="56d58-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-103">'Identity'</span></span>
- <span data-ttu-id="56d58-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-105">'Razor'</span></span>
- <span data-ttu-id="56d58-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="56d58-107">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="56d58-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="56d58-108">Par [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="56d58-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56d58-109">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="56d58-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="56d58-110">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="56d58-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="56d58-111">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="56d58-112">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="56d58-113">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="56d58-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="56d58-114">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="56d58-114">Forwarded headers</span></span>

<span data-ttu-id="56d58-115">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="56d58-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="56d58-116">En-tête</span><span class="sxs-lookup"><span data-stu-id="56d58-116">Header</span></span> | <span data-ttu-id="56d58-117">Description</span><span class="sxs-lookup"><span data-stu-id="56d58-117">Description</span></span> |
| ---
<span data-ttu-id="56d58-118">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-119">'Blazor'</span></span>
- <span data-ttu-id="56d58-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-120">'Identity'</span></span>
- <span data-ttu-id="56d58-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-122">'Razor'</span></span>
- <span data-ttu-id="56d58-123">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-123">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-124">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-125">'Blazor'</span></span>
- <span data-ttu-id="56d58-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-126">'Identity'</span></span>
- <span data-ttu-id="56d58-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-128">'Razor'</span></span>
- <span data-ttu-id="56d58-129">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-130">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-131">'Blazor'</span></span>
- <span data-ttu-id="56d58-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-132">'Identity'</span></span>
- <span data-ttu-id="56d58-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-134">'Razor'</span></span>
- <span data-ttu-id="56d58-135">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-136">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-137">'Blazor'</span></span>
- <span data-ttu-id="56d58-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-138">'Identity'</span></span>
- <span data-ttu-id="56d58-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-140">'Razor'</span></span>
- <span data-ttu-id="56d58-141">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-141">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-142">------ | | X-Forwarded-pour | Contient des informations sur le client qui a initié la demande et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="56d58-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="56d58-143">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="56d58-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="56d58-144">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="56d58-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="56d58-145">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="56d58-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="56d58-146">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="56d58-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="56d58-147">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="56d58-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="56d58-148">| | X-Forwarded-proto | Valeur du schéma d’origine (HTTP/HTTPs).</span><span class="sxs-lookup"><span data-stu-id="56d58-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="56d58-149">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="56d58-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="56d58-150">| | X-Forwarded-Host | Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56d58-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="56d58-151">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56d58-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="56d58-152">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="56d58-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="56d58-153">L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="56d58-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="56d58-154">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="56d58-154">The middleware updates:</span></span>

* <span data-ttu-id="56d58-155">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="56d58-156">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="56d58-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="56d58-157">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="56d58-158">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="56d58-159">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="56d58-160">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="56d58-161">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="56d58-161">The default settings are:</span></span>

* <span data-ttu-id="56d58-162">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="56d58-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="56d58-163">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="56d58-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="56d58-164">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="56d58-165">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="56d58-166">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="56d58-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="56d58-167">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="56d58-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="56d58-168">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="56d58-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="56d58-169">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="56d58-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="56d58-170">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d58-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="56d58-171">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="56d58-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="56d58-172">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="56d58-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="56d58-173">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="56d58-174">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="56d58-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="56d58-175">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="56d58-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="56d58-176">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="56d58-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="56d58-177">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="56d58-178">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d58-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56d58-179">Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :</span><span class="sxs-lookup"><span data-stu-id="56d58-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="56d58-180">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="56d58-181">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="56d58-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="56d58-182">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="56d58-182">Nginx configuration</span></span>

<span data-ttu-id="56d58-183">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="56d58-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="56d58-184">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="56d58-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="56d58-185">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="56d58-185">Apache configuration</span></span>

<span data-ttu-id="56d58-186">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="56d58-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="56d58-187">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="56d58-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="56d58-188">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="56d58-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="56d58-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="56d58-190">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="56d58-190">The following example changes the default values:</span></span>

* <span data-ttu-id="56d58-191">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="56d58-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="56d58-192">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="56d58-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="56d58-193">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="56d58-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="56d58-194">Option</span><span class="sxs-lookup"><span data-stu-id="56d58-194">Option</span></span> | <span data-ttu-id="56d58-195">Description</span><span class="sxs-lookup"><span data-stu-id="56d58-195">Description</span></span> |
| ---
<span data-ttu-id="56d58-196">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-197">'Blazor'</span></span>
- <span data-ttu-id="56d58-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-198">'Identity'</span></span>
- <span data-ttu-id="56d58-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-200">'Razor'</span></span>
- <span data-ttu-id="56d58-201">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-201">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-202">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-203">'Blazor'</span></span>
- <span data-ttu-id="56d58-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-204">'Identity'</span></span>
- <span data-ttu-id="56d58-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-206">'Razor'</span></span>
- <span data-ttu-id="56d58-207">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-208">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-209">'Blazor'</span></span>
- <span data-ttu-id="56d58-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-210">'Identity'</span></span>
- <span data-ttu-id="56d58-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-212">'Razor'</span></span>
- <span data-ttu-id="56d58-213">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-214">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-215">'Blazor'</span></span>
- <span data-ttu-id="56d58-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-216">'Identity'</span></span>
- <span data-ttu-id="56d58-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-218">'Razor'</span></span>
- <span data-ttu-id="56d58-219">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-219">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restreint les hôtes par l' `X-Forwarded-Host` en-tête aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="56d58-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="56d58-221">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="56d58-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="56d58-222">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="56d58-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="56d58-223">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="56d58-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="56d58-224">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="56d58-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="56d58-225">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="56d58-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="56d58-226">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="56d58-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="56d58-227">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="56d58-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="56d58-228">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="56d58-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="56d58-229">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="56d58-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="56d58-230">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="56d58-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="56d58-231">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="56d58-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="56d58-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="56d58-233">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-234">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="56d58-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="56d58-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="56d58-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="56d58-236">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="56d58-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="56d58-237">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="56d58-238">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="56d58-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="56d58-240">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-241">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="56d58-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="56d58-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="56d58-243">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-244">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="56d58-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limite le nombre d’entrées dans les en-têtes traités.</span><span class="sxs-lookup"><span data-stu-id="56d58-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="56d58-246">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="56d58-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="56d58-247">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="56d58-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="56d58-248">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="56d58-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="56d58-249">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="56d58-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="56d58-250">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="56d58-250">The default is `1`.</span></span> <span data-ttu-id="56d58-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Plages d’adresses des réseaux connus à partir desquels accepter les en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="56d58-252">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="56d58-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="56d58-253">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="56d58-254">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-255">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="56d58-256">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="56d58-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="56d58-257">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="56d58-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="56d58-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresses des proxies connus à partir desquels accepter les en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="56d58-259">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="56d58-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="56d58-260">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="56d58-261">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-262">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="56d58-263">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="56d58-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="56d58-264">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="56d58-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="56d58-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="56d58-266">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="56d58-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="56d58-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="56d58-268">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="56d58-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="56d58-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="56d58-270">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="56d58-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Exiger que le nombre de valeurs d’en-tête soit synchronisé entre le [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="56d58-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="56d58-272">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="56d58-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="56d58-273">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="56d58-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="56d58-274">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="56d58-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="56d58-275">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="56d58-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="56d58-276">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="56d58-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="56d58-277">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="56d58-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="56d58-278">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="56d58-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="56d58-279">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="56d58-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="56d58-280">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="56d58-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="56d58-281">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="56d58-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="56d58-282">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="56d58-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="56d58-283">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="56d58-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="56d58-284">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="56d58-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="56d58-285">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="56d58-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="56d58-286">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="56d58-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="56d58-287">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="56d58-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="56d58-288">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="56d58-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="56d58-289">Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6</span><span class="sxs-lookup"><span data-stu-id="56d58-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="56d58-290">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="56d58-291">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-292">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="56d58-293">Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.</span><span class="sxs-lookup"><span data-stu-id="56d58-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="56d58-294">Adresse IPv4 : `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="56d58-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="56d58-295">Adresse IPv6 convertie : `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="56d58-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="56d58-296">Longueur de préfixe converti : 104</span><span class="sxs-lookup"><span data-stu-id="56d58-296">Converted prefix length: 104</span></span>

<span data-ttu-id="56d58-297">Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="56d58-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="56d58-298">Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="56d58-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="56d58-299">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="56d58-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="56d58-300">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="56d58-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="56d58-301">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="56d58-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="56d58-302">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="56d58-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="56d58-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="56d58-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="56d58-304">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="56d58-305">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56d58-305">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="56d58-306">Transfert de certificat</span><span class="sxs-lookup"><span data-stu-id="56d58-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="56d58-307">Azure</span><span class="sxs-lookup"><span data-stu-id="56d58-307">Azure</span></span>

<span data-ttu-id="56d58-308">Pour configurer Azure App Service pour le transfert de certificats, consultez [configurer l’authentification mutuelle TLS pour les Azure App service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="56d58-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="56d58-309">Les instructions suivantes concernent la configuration de l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d58-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="56d58-310">Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="56d58-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="56d58-311">Configurez l’intergiciel (middleware) de transfert de certificats pour spécifier le nom d’en-tête utilisé par Azure.</span><span class="sxs-lookup"><span data-stu-id="56d58-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="56d58-312">Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :</span><span class="sxs-lookup"><span data-stu-id="56d58-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="56d58-313">Autres proxys Web</span><span class="sxs-lookup"><span data-stu-id="56d58-313">Other web proxies</span></span>

<span data-ttu-id="56d58-314">Si vous utilisez un proxy qui n’est pas IIS ou le Application Request Routing d’Azure App Service (ARR), configurez le proxy pour transférer le certificat qu’il a reçu dans un en-tête HTTP.</span><span class="sxs-lookup"><span data-stu-id="56d58-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="56d58-315">Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="56d58-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="56d58-316">Configurez l’intergiciel (middleware) de transfert de certificat pour spécifier le nom d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="56d58-317">Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :</span><span class="sxs-lookup"><span data-stu-id="56d58-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="56d58-318">Si le proxy n’encodage pas en base64 du certificat (comme c’est le cas avec Nginx), définissez l' `HeaderConverter` option.</span><span class="sxs-lookup"><span data-stu-id="56d58-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="56d58-319">Prenons l’exemple suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="56d58-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="56d58-320">Dépanner</span><span class="sxs-lookup"><span data-stu-id="56d58-320">Troubleshoot</span></span>

<span data-ttu-id="56d58-321">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="56d58-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="56d58-322">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="56d58-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="56d58-323">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="56d58-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="56d58-324">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="56d58-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="56d58-325">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="56d58-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="56d58-326">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="56d58-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="56d58-327">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="56d58-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="56d58-328">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="56d58-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="56d58-329">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="56d58-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="56d58-330">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="56d58-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="56d58-331">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="56d58-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="56d58-332">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="56d58-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="56d58-333">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="56d58-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="56d58-334">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="56d58-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="56d58-335">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="56d58-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="56d58-336">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="56d58-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="56d58-337">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d58-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56d58-338">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="56d58-339">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="56d58-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="56d58-340">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="56d58-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56d58-341">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56d58-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="56d58-342">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d58-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56d58-343">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="56d58-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="56d58-344">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="56d58-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="56d58-345">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="56d58-346">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="56d58-347">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="56d58-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="56d58-348">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="56d58-348">Forwarded headers</span></span>

<span data-ttu-id="56d58-349">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="56d58-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="56d58-350">En-tête</span><span class="sxs-lookup"><span data-stu-id="56d58-350">Header</span></span> | <span data-ttu-id="56d58-351">Description</span><span class="sxs-lookup"><span data-stu-id="56d58-351">Description</span></span> |
| ---
<span data-ttu-id="56d58-352">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-353">'Blazor'</span></span>
- <span data-ttu-id="56d58-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-354">'Identity'</span></span>
- <span data-ttu-id="56d58-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-356">'Razor'</span></span>
- <span data-ttu-id="56d58-357">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-357">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-358">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-359">'Blazor'</span></span>
- <span data-ttu-id="56d58-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-360">'Identity'</span></span>
- <span data-ttu-id="56d58-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-362">'Razor'</span></span>
- <span data-ttu-id="56d58-363">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-364">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-365">'Blazor'</span></span>
- <span data-ttu-id="56d58-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-366">'Identity'</span></span>
- <span data-ttu-id="56d58-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-368">'Razor'</span></span>
- <span data-ttu-id="56d58-369">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-370">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-371">'Blazor'</span></span>
- <span data-ttu-id="56d58-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-372">'Identity'</span></span>
- <span data-ttu-id="56d58-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-374">'Razor'</span></span>
- <span data-ttu-id="56d58-375">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-375">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-376">------ | | X-Forwarded-pour | Contient des informations sur le client qui a initié la demande et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="56d58-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="56d58-377">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="56d58-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="56d58-378">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="56d58-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="56d58-379">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="56d58-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="56d58-380">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="56d58-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="56d58-381">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="56d58-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="56d58-382">| | X-Forwarded-proto | Valeur du schéma d’origine (HTTP/HTTPs).</span><span class="sxs-lookup"><span data-stu-id="56d58-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="56d58-383">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="56d58-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="56d58-384">| | X-Forwarded-Host | Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56d58-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="56d58-385">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="56d58-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="56d58-386">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="56d58-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="56d58-387">L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="56d58-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="56d58-388">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="56d58-388">The middleware updates:</span></span>

* <span data-ttu-id="56d58-389">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="56d58-390">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="56d58-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="56d58-391">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="56d58-392">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="56d58-393">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="56d58-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="56d58-394">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="56d58-395">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="56d58-395">The default settings are:</span></span>

* <span data-ttu-id="56d58-396">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="56d58-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="56d58-397">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="56d58-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="56d58-398">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="56d58-399">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="56d58-400">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="56d58-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="56d58-401">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="56d58-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="56d58-402">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="56d58-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="56d58-403">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="56d58-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="56d58-404">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d58-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="56d58-405">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="56d58-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="56d58-406">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="56d58-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="56d58-407">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="56d58-408">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="56d58-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="56d58-409">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="56d58-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="56d58-410">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="56d58-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="56d58-411">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="56d58-412">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d58-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56d58-413">Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :</span><span class="sxs-lookup"><span data-stu-id="56d58-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="56d58-414">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="56d58-415">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="56d58-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="56d58-416">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="56d58-416">Nginx configuration</span></span>

<span data-ttu-id="56d58-417">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="56d58-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="56d58-418">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="56d58-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="56d58-419">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="56d58-419">Apache configuration</span></span>

<span data-ttu-id="56d58-420">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="56d58-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="56d58-421">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="56d58-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="56d58-422">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="56d58-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="56d58-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="56d58-424">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="56d58-424">The following example changes the default values:</span></span>

* <span data-ttu-id="56d58-425">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="56d58-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="56d58-426">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="56d58-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="56d58-427">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="56d58-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="56d58-428">Option</span><span class="sxs-lookup"><span data-stu-id="56d58-428">Option</span></span> | <span data-ttu-id="56d58-429">Description</span><span class="sxs-lookup"><span data-stu-id="56d58-429">Description</span></span> |
| ---
<span data-ttu-id="56d58-430">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-431">'Blazor'</span></span>
- <span data-ttu-id="56d58-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-432">'Identity'</span></span>
- <span data-ttu-id="56d58-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-434">'Razor'</span></span>
- <span data-ttu-id="56d58-435">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-435">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-436">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-437">'Blazor'</span></span>
- <span data-ttu-id="56d58-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-438">'Identity'</span></span>
- <span data-ttu-id="56d58-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-440">'Razor'</span></span>
- <span data-ttu-id="56d58-441">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-442">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-443">'Blazor'</span></span>
- <span data-ttu-id="56d58-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-444">'Identity'</span></span>
- <span data-ttu-id="56d58-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-446">'Razor'</span></span>
- <span data-ttu-id="56d58-447">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="56d58-448">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="56d58-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56d58-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56d58-449">'Blazor'</span></span>
- <span data-ttu-id="56d58-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56d58-450">'Identity'</span></span>
- <span data-ttu-id="56d58-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56d58-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="56d58-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56d58-452">'Razor'</span></span>
- <span data-ttu-id="56d58-453">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="56d58-453">'SignalR' uid:</span></span> 

<span data-ttu-id="56d58-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restreint les hôtes par l' `X-Forwarded-Host` en-tête aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="56d58-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="56d58-455">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="56d58-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="56d58-456">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="56d58-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="56d58-457">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="56d58-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="56d58-458">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="56d58-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="56d58-459">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="56d58-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="56d58-460">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="56d58-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="56d58-461">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="56d58-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="56d58-462">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="56d58-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="56d58-463">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="56d58-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="56d58-464">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="56d58-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="56d58-465">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="56d58-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="56d58-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="56d58-467">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-468">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="56d58-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="56d58-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="56d58-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="56d58-470">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="56d58-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="56d58-471">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="56d58-472">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="56d58-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="56d58-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="56d58-474">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-475">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="56d58-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="56d58-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="56d58-477">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="56d58-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="56d58-478">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="56d58-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limite le nombre d’entrées dans les en-têtes traités.</span><span class="sxs-lookup"><span data-stu-id="56d58-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="56d58-480">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="56d58-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="56d58-481">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="56d58-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="56d58-482">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="56d58-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="56d58-483">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="56d58-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="56d58-484">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="56d58-484">The default is `1`.</span></span> <span data-ttu-id="56d58-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Plages d’adresses des réseaux connus à partir desquels accepter les en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="56d58-486">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="56d58-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="56d58-487">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="56d58-488">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-489">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="56d58-490">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="56d58-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="56d58-491">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="56d58-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="56d58-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresses des proxies connus à partir desquels accepter les en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="56d58-493">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="56d58-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="56d58-494">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="56d58-495">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-496">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="56d58-497">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="56d58-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="56d58-498">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="56d58-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="56d58-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="56d58-500">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="56d58-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="56d58-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="56d58-502">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="56d58-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="56d58-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété plutôt que celui spécifié par [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="56d58-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="56d58-504">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="56d58-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="56d58-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Exiger que le nombre de valeurs d’en-tête soit synchronisé entre le [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="56d58-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="56d58-506">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="56d58-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="56d58-507">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="56d58-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="56d58-508">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="56d58-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="56d58-509">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="56d58-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="56d58-510">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="56d58-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="56d58-511">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="56d58-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="56d58-512">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="56d58-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="56d58-513">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="56d58-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="56d58-514">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="56d58-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="56d58-515">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="56d58-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="56d58-516">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="56d58-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="56d58-517">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="56d58-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="56d58-518">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="56d58-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="56d58-519">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="56d58-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="56d58-520">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="56d58-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="56d58-521">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="56d58-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="56d58-522">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="56d58-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="56d58-523">Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6</span><span class="sxs-lookup"><span data-stu-id="56d58-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="56d58-524">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="56d58-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="56d58-525">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="56d58-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="56d58-526">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="56d58-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="56d58-527">Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.</span><span class="sxs-lookup"><span data-stu-id="56d58-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="56d58-528">Adresse IPv4 : `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="56d58-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="56d58-529">Adresse IPv6 convertie : `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="56d58-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="56d58-530">Longueur de préfixe converti : 104</span><span class="sxs-lookup"><span data-stu-id="56d58-530">Converted prefix length: 104</span></span>

<span data-ttu-id="56d58-531">Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="56d58-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="56d58-532">Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="56d58-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="56d58-533">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="56d58-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="56d58-534">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="56d58-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="56d58-535">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="56d58-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="56d58-536">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="56d58-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="56d58-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="56d58-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="56d58-538">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="56d58-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="56d58-539">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56d58-539">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="56d58-540">Dépanner</span><span class="sxs-lookup"><span data-stu-id="56d58-540">Troubleshoot</span></span>

<span data-ttu-id="56d58-541">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="56d58-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="56d58-542">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="56d58-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="56d58-543">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="56d58-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="56d58-544">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="56d58-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="56d58-545">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="56d58-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="56d58-546">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="56d58-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="56d58-547">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="56d58-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="56d58-548">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="56d58-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="56d58-549">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="56d58-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="56d58-550">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="56d58-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="56d58-551">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="56d58-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="56d58-552">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="56d58-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="56d58-553">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="56d58-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="56d58-554">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="56d58-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="56d58-555">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="56d58-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="56d58-556">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="56d58-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="56d58-557">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d58-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56d58-558">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="56d58-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="56d58-559">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="56d58-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="56d58-560">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="56d58-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56d58-561">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56d58-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="56d58-562">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d58-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
