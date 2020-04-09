---
title: Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge
author: rick-anderson
description: Découvrez la configuration des applications hébergées derrière des serveurs proxy et des équilibreurs de charge, qui masquent souvent des informations de requête importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659382"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="2c619-103">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="2c619-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="2c619-104">Par [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="2c619-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c619-105">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="2c619-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="2c619-106">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="2c619-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="2c619-107">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="2c619-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="2c619-108">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="2c619-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="2c619-109">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="2c619-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="2c619-110">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="2c619-110">Forwarded headers</span></span>

<span data-ttu-id="2c619-111">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c619-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="2c619-112">En-tête</span><span class="sxs-lookup"><span data-stu-id="2c619-112">Header</span></span> | <span data-ttu-id="2c619-113">Description</span><span class="sxs-lookup"><span data-stu-id="2c619-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2c619-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="2c619-114">X-Forwarded-For</span></span> | <span data-ttu-id="2c619-115">Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="2c619-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="2c619-116">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="2c619-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="2c619-117">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="2c619-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="2c619-118">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="2c619-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="2c619-119">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="2c619-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="2c619-120">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="2c619-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="2c619-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="2c619-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="2c619-122">Valeur du schéma d’origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c619-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="2c619-123">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="2c619-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="2c619-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="2c619-124">X-Forwarded-Host</span></span> | <span data-ttu-id="2c619-125">Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2c619-125">The original value of the Host header field.</span></span> <span data-ttu-id="2c619-126">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2c619-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="2c619-127">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="2c619-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="2c619-128">L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="2c619-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="2c619-129">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2c619-129">The middleware updates:</span></span>

* <span data-ttu-id="2c619-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="2c619-131">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="2c619-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="2c619-132">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="2c619-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="2c619-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="2c619-135">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="2c619-136">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="2c619-136">The default settings are:</span></span>

* <span data-ttu-id="2c619-137">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2c619-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="2c619-138">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="2c619-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="2c619-139">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="2c619-140">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="2c619-141">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="2c619-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="2c619-142">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="2c619-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="2c619-143">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="2c619-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="2c619-144">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="2c619-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="2c619-145">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c619-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="2c619-146">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="2c619-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="2c619-147">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="2c619-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="2c619-148">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2c619-149">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="2c619-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2c619-150">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="2c619-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="2c619-151">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="2c619-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="2c619-152">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="2c619-153">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2c619-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2c619-154">Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :</span><span class="sxs-lookup"><span data-stu-id="2c619-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="2c619-155">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="2c619-156">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="2c619-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="2c619-157">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="2c619-157">Nginx configuration</span></span>

<span data-ttu-id="2c619-158">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="2c619-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="2c619-159">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="2c619-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="2c619-160">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="2c619-160">Apache configuration</span></span>

<span data-ttu-id="2c619-161">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="2c619-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="2c619-162">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="2c619-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="2c619-163">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="2c619-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="2c619-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="2c619-165">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="2c619-165">The following example changes the default values:</span></span>

* <span data-ttu-id="2c619-166">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="2c619-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="2c619-167">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="2c619-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="2c619-168">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="2c619-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="2c619-169">Option</span><span class="sxs-lookup"><span data-stu-id="2c619-169">Option</span></span> | <span data-ttu-id="2c619-170">Description</span><span class="sxs-lookup"><span data-stu-id="2c619-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="2c619-171">Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="2c619-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="2c619-172">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="2c619-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="2c619-173">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="2c619-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="2c619-174">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="2c619-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="2c619-175">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="2c619-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="2c619-176">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="2c619-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="2c619-177">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2c619-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="2c619-178">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="2c619-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="2c619-179">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="2c619-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="2c619-180">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="2c619-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="2c619-181">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="2c619-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="2c619-182">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="2c619-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="2c619-183">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="2c619-184">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-185">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="2c619-186">Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="2c619-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="2c619-187">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="2c619-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="2c619-188">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="2c619-189">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="2c619-190">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="2c619-191">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-192">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="2c619-193">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="2c619-194">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-195">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="2c619-196">Limite le nombre d’entrées dans les en-têtes qui sont traités.</span><span class="sxs-lookup"><span data-stu-id="2c619-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="2c619-197">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="2c619-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="2c619-198">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="2c619-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="2c619-199">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="2c619-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2c619-200">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="2c619-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="2c619-201">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="2c619-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="2c619-202">Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="2c619-203">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="2c619-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="2c619-204">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2c619-205">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-206">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2c619-207">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="2c619-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2c619-208">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2c619-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="2c619-209">Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="2c619-210">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="2c619-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="2c619-211">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2c619-212">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-213">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2c619-214">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="2c619-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2c619-215">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2c619-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="2c619-216">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="2c619-217">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="2c619-218">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="2c619-219">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="2c619-220">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="2c619-221">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="2c619-222">Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="2c619-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="2c619-223">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="2c619-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="2c619-224">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="2c619-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="2c619-225">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="2c619-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="2c619-226">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="2c619-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="2c619-227">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="2c619-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="2c619-228">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="2c619-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="2c619-229">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="2c619-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="2c619-230">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="2c619-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="2c619-231">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="2c619-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="2c619-232">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="2c619-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="2c619-233">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2c619-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="2c619-234">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="2c619-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="2c619-235">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="2c619-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2c619-236">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="2c619-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="2c619-237">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="2c619-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="2c619-238">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="2c619-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="2c619-239">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="2c619-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="2c619-240">Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6</span><span class="sxs-lookup"><span data-stu-id="2c619-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="2c619-241">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="2c619-242">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-243">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="2c619-244">Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.</span><span class="sxs-lookup"><span data-stu-id="2c619-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="2c619-245">Adresse IPv4 : `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="2c619-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="2c619-246">Adresse IPv6 convertie : `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="2c619-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="2c619-247">Longueur de préfixe converti : 104</span><span class="sxs-lookup"><span data-stu-id="2c619-247">Converted prefix length: 104</span></span>

<span data-ttu-id="2c619-248">Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="2c619-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="2c619-249">Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="2c619-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="2c619-250">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="2c619-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="2c619-251">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2c619-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="2c619-252">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="2c619-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="2c619-253">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="2c619-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="2c619-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="2c619-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="2c619-255">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="2c619-256">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2c619-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="2c619-257">Transfert de certificat</span><span class="sxs-lookup"><span data-stu-id="2c619-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="2c619-258">Azure</span><span class="sxs-lookup"><span data-stu-id="2c619-258">Azure</span></span>

<span data-ttu-id="2c619-259">Pour configurer Azure App Service pour l’endage des certificats, voir [l’authentification mutuelle Configure TLS pour Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="2c619-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="2c619-260">Les directives suivantes portent sur la configuration de l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c619-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="2c619-261">Dans `Startup.Configure`, ajouter le code `app.UseAuthentication();`suivant avant l’appel à :</span><span class="sxs-lookup"><span data-stu-id="2c619-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="2c619-262">Configurer Certificate Forwarding Middleware pour spécifier le nom d’en-tête qu’Azure utilise.</span><span class="sxs-lookup"><span data-stu-id="2c619-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="2c619-263">Dans `Startup.ConfigureServices`, ajouter le code suivant pour configurer l’en-tête à partir duquel le middleware construit un certificat:</span><span class="sxs-lookup"><span data-stu-id="2c619-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="2c619-264">Autres procurations web</span><span class="sxs-lookup"><span data-stu-id="2c619-264">Other web proxies</span></span>

<span data-ttu-id="2c619-265">Si un proxy est utilisé qui n’est pas IIS ou Azure App Service’s Application Request Routing (ARR), configurez le proxy pour transmettre le certificat qu’il a reçu dans un en-tête HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c619-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="2c619-266">Dans `Startup.Configure`, ajouter le code `app.UseAuthentication();`suivant avant l’appel à :</span><span class="sxs-lookup"><span data-stu-id="2c619-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="2c619-267">Configurer le certificat En transmettant Middleware pour spécifier le nom d’en-tête.</span><span class="sxs-lookup"><span data-stu-id="2c619-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="2c619-268">Dans `Startup.ConfigureServices`, ajouter le code suivant pour configurer l’en-tête à partir duquel le middleware construit un certificat:</span><span class="sxs-lookup"><span data-stu-id="2c619-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="2c619-269">Si le proxy n’est pas base64-encodage du certificat (comme c’est le cas avec Nginx), définissez l’option. `HeaderConverter`</span><span class="sxs-lookup"><span data-stu-id="2c619-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="2c619-270">Prenons l’exemple suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c619-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="2c619-271">Dépanner</span><span class="sxs-lookup"><span data-stu-id="2c619-271">Troubleshoot</span></span>

<span data-ttu-id="2c619-272">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2c619-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2c619-273">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="2c619-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="2c619-274">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="2c619-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="2c619-275">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2c619-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="2c619-276">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="2c619-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="2c619-277">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="2c619-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="2c619-278">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="2c619-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="2c619-279">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="2c619-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="2c619-280">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2c619-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2c619-281">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="2c619-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="2c619-282">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="2c619-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2c619-283">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="2c619-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="2c619-284">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="2c619-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="2c619-285">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="2c619-286">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="2c619-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="2c619-287">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="2c619-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="2c619-288">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2c619-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2c619-289">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="2c619-290">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="2c619-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="2c619-291">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="2c619-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c619-292">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2c619-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="2c619-293">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c619-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c619-294">Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache.</span><span class="sxs-lookup"><span data-stu-id="2c619-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="2c619-295">Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :</span><span class="sxs-lookup"><span data-stu-id="2c619-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="2c619-296">Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="2c619-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="2c619-297">Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.</span><span class="sxs-lookup"><span data-stu-id="2c619-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="2c619-298">Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.</span><span class="sxs-lookup"><span data-stu-id="2c619-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="2c619-299">En-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="2c619-299">Forwarded headers</span></span>

<span data-ttu-id="2c619-300">Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c619-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="2c619-301">En-tête</span><span class="sxs-lookup"><span data-stu-id="2c619-301">Header</span></span> | <span data-ttu-id="2c619-302">Description</span><span class="sxs-lookup"><span data-stu-id="2c619-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2c619-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="2c619-303">X-Forwarded-For</span></span> | <span data-ttu-id="2c619-304">Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys.</span><span class="sxs-lookup"><span data-stu-id="2c619-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="2c619-305">Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port).</span><span class="sxs-lookup"><span data-stu-id="2c619-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="2c619-306">Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="2c619-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="2c619-307">Viennent ensuite les identificateurs des proxy suivants.</span><span class="sxs-lookup"><span data-stu-id="2c619-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="2c619-308">Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres.</span><span class="sxs-lookup"><span data-stu-id="2c619-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="2c619-309">L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport.</span><span class="sxs-lookup"><span data-stu-id="2c619-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="2c619-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="2c619-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="2c619-311">Valeur du schéma d’origine (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c619-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="2c619-312">La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys.</span><span class="sxs-lookup"><span data-stu-id="2c619-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="2c619-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="2c619-313">X-Forwarded-Host</span></span> | <span data-ttu-id="2c619-314">Valeur d’origine du champ d’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2c619-314">The original value of the Host header field.</span></span> <span data-ttu-id="2c619-315">En règle générale, les proxys ne modifient pas l’en-tête de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2c619-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="2c619-316">Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues.</span><span class="sxs-lookup"><span data-stu-id="2c619-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="2c619-317">L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="2c619-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="2c619-318">Le middleware met à jour les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2c619-318">The middleware updates:</span></span>

* <span data-ttu-id="2c619-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="2c619-320">Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="2c619-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="2c619-321">Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="2c619-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="2c619-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Défini à l’aide de la valeur d’en-tête `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="2c619-324">Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="2c619-325">Les paramètres par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="2c619-325">The default settings are:</span></span>

* <span data-ttu-id="2c619-326">Il y a *un seul proxy* entre l’application et la source des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2c619-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="2c619-327">Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.</span><span class="sxs-lookup"><span data-stu-id="2c619-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="2c619-328">Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="2c619-329">Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="2c619-330">Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application.</span><span class="sxs-lookup"><span data-stu-id="2c619-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="2c619-331">Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance.</span><span class="sxs-lookup"><span data-stu-id="2c619-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="2c619-332">Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="2c619-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="2c619-333">Module ASP.NET Core et IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="2c619-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="2c619-334">Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c619-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="2c619-335">Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="2c619-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="2c619-336">Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique.</span><span class="sxs-lookup"><span data-stu-id="2c619-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="2c619-337">Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2c619-338">Autres scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="2c619-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2c619-339">En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="2c619-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="2c619-340">L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="2c619-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="2c619-341">Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="2c619-342">Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2c619-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2c619-343">Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :</span><span class="sxs-lookup"><span data-stu-id="2c619-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="2c619-344">Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="2c619-345">La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.</span><span class="sxs-lookup"><span data-stu-id="2c619-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="2c619-346">Configuration de Nginx</span><span class="sxs-lookup"><span data-stu-id="2c619-346">Nginx configuration</span></span>

<span data-ttu-id="2c619-347">Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="2c619-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="2c619-348">Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).</span><span class="sxs-lookup"><span data-stu-id="2c619-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="2c619-349">Configuration d’Apache</span><span class="sxs-lookup"><span data-stu-id="2c619-349">Apache configuration</span></span>

<span data-ttu-id="2c619-350">`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="2c619-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="2c619-351">Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="2c619-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="2c619-352">Options du middleware des en-têtes transférés</span><span class="sxs-lookup"><span data-stu-id="2c619-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="2c619-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="2c619-354">L’exemple suivant change les valeurs par défaut :</span><span class="sxs-lookup"><span data-stu-id="2c619-354">The following example changes the default values:</span></span>

* <span data-ttu-id="2c619-355">Limitez le nombre d’entrées dans les en-têtes transférés à `2`.</span><span class="sxs-lookup"><span data-stu-id="2c619-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="2c619-356">Ajoutez l’adresse de proxy connue `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="2c619-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="2c619-357">Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="2c619-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="2c619-358">Option</span><span class="sxs-lookup"><span data-stu-id="2c619-358">Option</span></span> | <span data-ttu-id="2c619-359">Description</span><span class="sxs-lookup"><span data-stu-id="2c619-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="2c619-360">Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.</span><span class="sxs-lookup"><span data-stu-id="2c619-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="2c619-361">Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="2c619-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="2c619-362">Les numéros de port doivent être exclus.</span><span class="sxs-lookup"><span data-stu-id="2c619-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="2c619-363">Si la liste est vide, tous les hôtes sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="2c619-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="2c619-364">Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</span><span class="sxs-lookup"><span data-stu-id="2c619-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="2c619-365">Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine.</span><span class="sxs-lookup"><span data-stu-id="2c619-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="2c619-366">Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2c619-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="2c619-367">Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</span><span class="sxs-lookup"><span data-stu-id="2c619-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="2c619-368">Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="2c619-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="2c619-369">Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="2c619-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="2c619-370">La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</span><span class="sxs-lookup"><span data-stu-id="2c619-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="2c619-371">La valeur par défaut est un `IList<string>` vide.</span><span class="sxs-lookup"><span data-stu-id="2c619-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="2c619-372">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="2c619-373">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-374">Par défaut, il s’agit de `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="2c619-375">Identifie les redirecteurs à traiter.</span><span class="sxs-lookup"><span data-stu-id="2c619-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="2c619-376">Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="2c619-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="2c619-377">En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="2c619-378">La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2c619-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="2c619-379">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="2c619-380">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-381">Par défaut, il s’agit de `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="2c619-382">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="2c619-383">Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.</span><span class="sxs-lookup"><span data-stu-id="2c619-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2c619-384">Par défaut, il s’agit de `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="2c619-385">Limite le nombre d’entrées dans les en-têtes qui sont traités.</span><span class="sxs-lookup"><span data-stu-id="2c619-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="2c619-386">Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="2c619-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="2c619-387">Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="2c619-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="2c619-388">Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="2c619-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2c619-389">Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="2c619-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="2c619-390">Par défaut, il s’agit de `1`.</span><span class="sxs-lookup"><span data-stu-id="2c619-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="2c619-391">Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="2c619-392">Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).</span><span class="sxs-lookup"><span data-stu-id="2c619-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="2c619-393">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2c619-394">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-395">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2c619-396">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="2c619-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2c619-397">La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2c619-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="2c619-398">Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="2c619-399">Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.</span><span class="sxs-lookup"><span data-stu-id="2c619-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="2c619-400">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2c619-401">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-402">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2c619-403">Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="2c619-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2c619-404">La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2c619-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="2c619-405">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="2c619-406">Par défaut, il s’agit de `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="2c619-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="2c619-407">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="2c619-408">Par défaut, il s’agit de `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="2c619-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="2c619-409">Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2c619-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="2c619-410">Par défaut, il s’agit de `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2c619-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="2c619-411">Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="2c619-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="2c619-412">La valeur par défaut dans ASP.NET Core 1.x est `true`.</span><span class="sxs-lookup"><span data-stu-id="2c619-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="2c619-413">La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`.</span><span class="sxs-lookup"><span data-stu-id="2c619-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="2c619-414">Scénarios et cas d’usage</span><span class="sxs-lookup"><span data-stu-id="2c619-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="2c619-415">Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées</span><span class="sxs-lookup"><span data-stu-id="2c619-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="2c619-416">Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application.</span><span class="sxs-lookup"><span data-stu-id="2c619-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="2c619-417">Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :</span><span class="sxs-lookup"><span data-stu-id="2c619-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="2c619-418">Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.</span><span class="sxs-lookup"><span data-stu-id="2c619-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="2c619-419">Traiter la base du chemin et les proxys qui changent le chemin de la requête</span><span class="sxs-lookup"><span data-stu-id="2c619-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="2c619-420">Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="2c619-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="2c619-421">Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="2c619-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="2c619-422">Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2c619-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="2c619-423">Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="2c619-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="2c619-424">Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="2c619-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2c619-425">Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :</span><span class="sxs-lookup"><span data-stu-id="2c619-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="2c619-426">Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="2c619-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="2c619-427">Configuration d’un proxy qui utilise des noms d’en-têtes différents</span><span class="sxs-lookup"><span data-stu-id="2c619-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="2c619-428">Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :</span><span class="sxs-lookup"><span data-stu-id="2c619-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="2c619-429">Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6</span><span class="sxs-lookup"><span data-stu-id="2c619-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="2c619-430">Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="2c619-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="2c619-431">Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2c619-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2c619-432">Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2c619-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="2c619-433">Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.</span><span class="sxs-lookup"><span data-stu-id="2c619-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="2c619-434">Adresse IPv4 : `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="2c619-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="2c619-435">Adresse IPv6 convertie : `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="2c619-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="2c619-436">Longueur de préfixe converti : 104</span><span class="sxs-lookup"><span data-stu-id="2c619-436">Converted prefix length: 104</span></span>

<span data-ttu-id="2c619-437">Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="2c619-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="2c619-438">Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="2c619-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="2c619-439">Transférer le schéma pour les serveurs proxy inverses Linux et non IIS</span><span class="sxs-lookup"><span data-stu-id="2c619-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="2c619-440">Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2c619-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="2c619-441">Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié.</span><span class="sxs-lookup"><span data-stu-id="2c619-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="2c619-442">OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes.</span><span class="sxs-lookup"><span data-stu-id="2c619-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="2c619-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).</span><span class="sxs-lookup"><span data-stu-id="2c619-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="2c619-444">Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="2c619-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="2c619-445">Dans `Startup.ConfigureServices`, utilisez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2c619-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="2c619-446">Dépanner</span><span class="sxs-lookup"><span data-stu-id="2c619-446">Troubleshoot</span></span>

<span data-ttu-id="2c619-447">Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2c619-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2c619-448">Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur.</span><span class="sxs-lookup"><span data-stu-id="2c619-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="2c619-449">Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="2c619-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="2c619-450">Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2c619-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="2c619-451">Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ;</span><span class="sxs-lookup"><span data-stu-id="2c619-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="2c619-452">ainsi, le site fonctionnera normalement pendant le débogage.</span><span class="sxs-lookup"><span data-stu-id="2c619-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="2c619-453">Pour écrire dans les journaux plutôt que dans le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="2c619-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="2c619-454">Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="2c619-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="2c619-455">Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2c619-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2c619-456">Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="2c619-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="2c619-457">S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche.</span><span class="sxs-lookup"><span data-stu-id="2c619-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2c619-458">La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.</span><span class="sxs-lookup"><span data-stu-id="2c619-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="2c619-459">L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités.</span><span class="sxs-lookup"><span data-stu-id="2c619-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="2c619-460">L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés.</span><span class="sxs-lookup"><span data-stu-id="2c619-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="2c619-461">Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :</span><span class="sxs-lookup"><span data-stu-id="2c619-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="2c619-462">Dans l’exemple précédent, 10.0.0.100 est un serveur proxy.</span><span class="sxs-lookup"><span data-stu-id="2c619-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="2c619-463">Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2c619-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2c619-464">Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2c619-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="2c619-465">Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes.</span><span class="sxs-lookup"><span data-stu-id="2c619-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="2c619-466">Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.</span><span class="sxs-lookup"><span data-stu-id="2c619-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c619-467">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2c619-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="2c619-468">Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c619-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
