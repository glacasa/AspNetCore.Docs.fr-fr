---
title: Appliquer le protocole HTTPs en ASP.NET Core
author: rick-anderson
description: Découvrez comment exiger le protocole HTTPs/TLS dans une application Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/enforcing-ssl
ms.openlocfilehash: aac52ac760de31c4ba6e7215b95cdbb558d6640b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777369"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="68a76-103">Appliquer le protocole HTTPs en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68a76-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="68a76-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68a76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68a76-105">Ce document montre comment :</span><span class="sxs-lookup"><span data-stu-id="68a76-105">This document shows how to:</span></span>

* <span data-ttu-id="68a76-106">Exiger le protocole HTTPs pour toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="68a76-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="68a76-107">Redirige toutes les requêtes HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="68a76-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="68a76-108">Aucune API ne peut empêcher un client d’envoyer des données sensibles à la première demande.</span><span class="sxs-lookup"><span data-stu-id="68a76-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="68a76-109">Projets d’API</span><span class="sxs-lookup"><span data-stu-id="68a76-109">API projects</span></span>
>
> <span data-ttu-id="68a76-110">N' **not** utilisez pas [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) sur les API Web qui reçoivent des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="68a76-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="68a76-111">`RequireHttpsAttribute`utilise des codes d’état HTTP pour rediriger les navigateurs de HTTP vers HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="68a76-112">Les clients d’API peuvent ne pas comprendre ou respecter les redirections du protocole HTTP vers HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="68a76-113">Ces clients peuvent envoyer des informations via HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="68a76-114">Les API Web doivent être les suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="68a76-115">Ne pas écouter sur HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="68a76-116">Fermez la connexion avec le code d’état 400 (requête incorrecte) et ne traitez pas la requête.</span><span class="sxs-lookup"><span data-stu-id="68a76-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="68a76-117">Projets HSTS et API</span><span class="sxs-lookup"><span data-stu-id="68a76-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="68a76-118">Les projets d’API par défaut n’incluent pas [HSTS](#hsts) , car HSTS est généralement une instruction de navigateur uniquement.</span><span class="sxs-lookup"><span data-stu-id="68a76-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="68a76-119">Les autres appelants, tels que les applications de bureau ou de téléphone, ne respectent **pas** l’instruction.</span><span class="sxs-lookup"><span data-stu-id="68a76-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="68a76-120">Même dans les navigateurs, un seul appel authentifié à une API sur HTTP présente des risques sur les réseaux non sécurisés.</span><span class="sxs-lookup"><span data-stu-id="68a76-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="68a76-121">L’approche sécurisée consiste à configurer des projets d’API pour écouter et répondre uniquement au protocole HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="68a76-122">Projets d’API</span><span class="sxs-lookup"><span data-stu-id="68a76-122">API projects</span></span>
>
> <span data-ttu-id="68a76-123">N' **not** utilisez pas [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) sur les API Web qui reçoivent des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="68a76-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="68a76-124">`RequireHttpsAttribute`utilise des codes d’état HTTP pour rediriger les navigateurs de HTTP vers HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="68a76-125">Les clients d’API peuvent ne pas comprendre ou respecter les redirections du protocole HTTP vers HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="68a76-126">Ces clients peuvent envoyer des informations via HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="68a76-127">Les API Web doivent être les suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="68a76-128">Ne pas écouter sur HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="68a76-129">Fermez la connexion avec le code d’état 400 (requête incorrecte) et ne traitez pas la requête.</span><span class="sxs-lookup"><span data-stu-id="68a76-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="68a76-130">Exiger HTTPS</span><span class="sxs-lookup"><span data-stu-id="68a76-130">Require HTTPS</span></span>

<span data-ttu-id="68a76-131">Nous vous recommandons d’utiliser les applications Web de production ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="68a76-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="68a76-132">Intergiciel (middleware) de redirection HTTPs (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pour rediriger les requêtes HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="68a76-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="68a76-133">Intergiciel (middleware) HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) pour envoyer des en-têtes HSTS (protocole de sécurité de transport strict) aux clients.</span><span class="sxs-lookup"><span data-stu-id="68a76-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="68a76-134">Les applications déployées dans une configuration de proxy inverse autorisent le proxy à gérer la sécurité de la connexion (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="68a76-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="68a76-135">Si le proxy gère également la redirection HTTPs, il n’est pas nécessaire d’utiliser l’intergiciel (middleware) de redirection HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="68a76-136">Si le serveur proxy gère également l’écriture d’en-têtes HSTS (par exemple, la [prise en charge native de HSTS dans IIS 10,0 (1709) ou version ultérieure](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), l’application ne nécessite pas d’intergiciel HSTS.</span><span class="sxs-lookup"><span data-stu-id="68a76-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="68a76-137">Pour plus d’informations, consultez [opt-out de https/HSTS lors de la création du projet](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="68a76-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="68a76-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="68a76-138">UseHttpsRedirection</span></span>

<span data-ttu-id="68a76-139">Le code suivant appelle `UseHttpsRedirection` dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="68a76-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="68a76-140">Code mis en surbrillance précédent :</span><span class="sxs-lookup"><span data-stu-id="68a76-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="68a76-141">Utilise la valeur par défaut [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="68a76-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="68a76-142">Utilise la valeur par défaut [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), sauf si `ASPNETCORE_HTTPS_PORT` elle est remplacée par la variable d’environnement ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="68a76-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="68a76-143">Nous vous recommandons d’utiliser des redirections temporaires plutôt que des redirections permanentes.</span><span class="sxs-lookup"><span data-stu-id="68a76-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="68a76-144">La mise en cache des liens peut provoquer un comportement instable dans les environnements de développement.</span><span class="sxs-lookup"><span data-stu-id="68a76-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="68a76-145">Si vous préférez envoyer un code d’état de redirection permanent lorsque l’application se trouve dans un environnement de non-développement, consultez la section [configurer des redirections permanentes en production](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="68a76-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="68a76-146">Nous vous recommandons d’utiliser [HSTS](#http-strict-transport-security-protocol-hsts) pour signaler aux clients que seules les demandes de ressources sécurisées doivent être envoyées à l’application (uniquement en production).</span><span class="sxs-lookup"><span data-stu-id="68a76-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="68a76-147">Configuration du port</span><span class="sxs-lookup"><span data-stu-id="68a76-147">Port configuration</span></span>

<span data-ttu-id="68a76-148">Un port doit être disponible pour que l’intergiciel redirige une requête non sécurisée vers HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="68a76-149">Si aucun port n’est disponible :</span><span class="sxs-lookup"><span data-stu-id="68a76-149">If no port is available:</span></span>

* <span data-ttu-id="68a76-150">La redirection vers HTTPs ne se produit pas.</span><span class="sxs-lookup"><span data-stu-id="68a76-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="68a76-151">L’intergiciel enregistre l’avertissement « échec de la détermination du port HTTPS pour la redirection ».</span><span class="sxs-lookup"><span data-stu-id="68a76-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="68a76-152">Spécifiez le port HTTPs à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="68a76-153">Définissez [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="68a76-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="68a76-154">Définissez le `https_port` [paramètre d’ordinateur hôte](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="68a76-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="68a76-155">Dans la configuration de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="68a76-155">In host configuration.</span></span>
  * <span data-ttu-id="68a76-156">En définissant `ASPNETCORE_HTTPS_PORT` la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="68a76-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="68a76-157">En ajoutant une entrée de niveau supérieur dans *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="68a76-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="68a76-158">Indiquez un port avec le schéma sécurisé à l’aide de la [variable d’environnement ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="68a76-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="68a76-159">La variable d’environnement configure le serveur.</span><span class="sxs-lookup"><span data-stu-id="68a76-159">The environment variable configures the server.</span></span> <span data-ttu-id="68a76-160">L’intergiciel Découvre indirectement le port HTTPs via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="68a76-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="68a76-161">Cette approche ne fonctionne pas dans les déploiements de proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="68a76-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="68a76-162">Définissez le `https_port` [paramètre d’ordinateur hôte](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="68a76-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="68a76-163">Dans la configuration de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="68a76-163">In host configuration.</span></span>
  * <span data-ttu-id="68a76-164">En définissant `ASPNETCORE_HTTPS_PORT` la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="68a76-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="68a76-165">En ajoutant une entrée de niveau supérieur dans *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="68a76-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="68a76-166">Indiquez un port avec le schéma sécurisé à l’aide de la [variable d’environnement ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="68a76-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="68a76-167">La variable d’environnement configure le serveur.</span><span class="sxs-lookup"><span data-stu-id="68a76-167">The environment variable configures the server.</span></span> <span data-ttu-id="68a76-168">L’intergiciel Découvre indirectement le port HTTPs via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="68a76-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="68a76-169">Cette approche ne fonctionne pas dans les déploiements de proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="68a76-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="68a76-170">Dans développement, définissez une URL HTTPs dans *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="68a76-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="68a76-171">Activez le protocole HTTPs lorsque IIS Express est utilisé.</span><span class="sxs-lookup"><span data-stu-id="68a76-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="68a76-172">Configurez un point de terminaison d’URL HTTPs pour un déploiement de périphérie public d’un serveur [Kestrel](xref:fundamentals/servers/kestrel) ou [http. sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="68a76-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="68a76-173">**Un seul port HTTPS** est utilisé par l’application.</span><span class="sxs-lookup"><span data-stu-id="68a76-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="68a76-174">L’intergiciel Découvre le port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="68a76-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="68a76-175">Lorsqu’une application est exécutée dans une configuration de proxy <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> inverse, n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="68a76-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="68a76-176">Définissez le port à l’aide de l’une des autres approches décrites dans cette section.</span><span class="sxs-lookup"><span data-stu-id="68a76-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="68a76-177">Déploiements de périphérie</span><span class="sxs-lookup"><span data-stu-id="68a76-177">Edge deployments</span></span> 

<span data-ttu-id="68a76-178">Quand Kestrel ou HTTP. sys est utilisé en tant que serveur Edge public, Kestrel ou HTTP. sys doivent être configurés pour écouter les deux :</span><span class="sxs-lookup"><span data-stu-id="68a76-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="68a76-179">Le port sécurisé où le client est redirigé (en général, 443 en production et 5001 en développement).</span><span class="sxs-lookup"><span data-stu-id="68a76-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="68a76-180">Le port non sécurisé (en général, 80 en production et 5000 en développement).</span><span class="sxs-lookup"><span data-stu-id="68a76-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="68a76-181">Le port non sécurisé doit être accessible par le client afin que l’application puisse recevoir une demande non sécurisée et rediriger le client vers le port sécurisé.</span><span class="sxs-lookup"><span data-stu-id="68a76-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="68a76-182">Pour plus d’informations, consultez Configuration du point <xref:fundamentals/servers/httpsys>de [terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou.</span><span class="sxs-lookup"><span data-stu-id="68a76-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="68a76-183">Scénarios de déploiement</span><span class="sxs-lookup"><span data-stu-id="68a76-183">Deployment scenarios</span></span>

<span data-ttu-id="68a76-184">Tout pare-feu entre le client et le serveur doit également disposer de ports de communication ouverts pour le trafic.</span><span class="sxs-lookup"><span data-stu-id="68a76-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="68a76-185">Si les demandes sont transférées dans une configuration de proxy inverse, utilisez l' [intergiciel d’en-tête transféré](xref:host-and-deploy/proxy-load-balancer) avant d’appeler l’intergiciel (middleware) de REdirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="68a76-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="68a76-186">L’intergiciel d’en-têtes transférés met à `Request.Scheme`jour le, `X-Forwarded-Proto` à l’aide de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="68a76-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="68a76-187">L’intergiciel permet de rediriger les URI et d’autres stratégies de sécurité pour fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="68a76-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="68a76-188">Lorsque l’intergiciel d’en-têtes transférés n’est pas utilisé, l’application principale peut ne pas recevoir le schéma correct et se terminer dans une boucle de redirection.</span><span class="sxs-lookup"><span data-stu-id="68a76-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="68a76-189">Un message d’erreur de l’utilisateur final commun est qu’un trop grand nombre de redirections se sont produites.</span><span class="sxs-lookup"><span data-stu-id="68a76-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="68a76-190">Lors du déploiement sur Azure App Service, suivez les instructions du [Didacticiel : lier un certificat SSL personnalisé existant à Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="68a76-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="68a76-191">Options</span><span class="sxs-lookup"><span data-stu-id="68a76-191">Options</span></span>

<span data-ttu-id="68a76-192">Le code en surbrillance suivant appelle [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) pour configurer les options de l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="68a76-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="68a76-193">L' `AddHttpsRedirection` appel de est nécessaire uniquement pour modifier les `HttpsPort` valeurs `RedirectStatusCode`de ou.</span><span class="sxs-lookup"><span data-stu-id="68a76-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="68a76-194">Code mis en surbrillance précédent :</span><span class="sxs-lookup"><span data-stu-id="68a76-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="68a76-195">Définit [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) sur <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, qui est la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="68a76-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="68a76-196">Utilisez les champs de la <xref:Microsoft.AspNetCore.Http.StatusCodes> classe pour les assignations à `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="68a76-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="68a76-197">Définit le port HTTPs sur 5001.</span><span class="sxs-lookup"><span data-stu-id="68a76-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="68a76-198">Configurer des redirections permanentes en production</span><span class="sxs-lookup"><span data-stu-id="68a76-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="68a76-199">Par défaut, l’intergiciel envoie un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) avec toutes les redirections.</span><span class="sxs-lookup"><span data-stu-id="68a76-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="68a76-200">Si vous préférez envoyer un code d’état de redirection permanent lorsque l’application se trouve dans un environnement de non-développement, encapsulez la configuration des options d’intergiciel dans une vérification conditionnelle pour un environnement de non-développement.</span><span class="sxs-lookup"><span data-stu-id="68a76-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68a76-201">Lors de la configuration des services dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="68a76-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="68a76-202">Lors de la configuration des services dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="68a76-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="68a76-203">Approche alternative de l’intergiciel (middleware) de redirection HTTPs</span><span class="sxs-lookup"><span data-stu-id="68a76-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="68a76-204">Une alternative à l’utilisation de l’intergiciel () de redirection HTTPs (`UseHttpsRedirection`) consiste à utiliser l’intergiciel`AddRedirectToHttps`() de réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="68a76-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="68a76-205">`AddRedirectToHttps`peut également définir le code et le port d’état lors de l’exécution de la redirection.</span><span class="sxs-lookup"><span data-stu-id="68a76-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="68a76-206">Pour plus d’informations, consultez intergiciel (middleware) de [réécriture d’URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="68a76-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="68a76-207">Lorsque vous redirigez vers le protocole HTTPs sans avoir besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser l’intergiciel`UseHttpsRedirection`() de REdirection HTTPS () décrit dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="68a76-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="68a76-208">Protocole HTTP strict transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="68a76-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="68a76-209">Par [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [http strict transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) est une amélioration de sécurité qui est spécifiée par une application Web via l’utilisation d’un en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="68a76-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="68a76-210">Quand un [navigateur prenant en charge HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) reçoit cet en-tête :</span><span class="sxs-lookup"><span data-stu-id="68a76-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="68a76-211">Le navigateur stocke la configuration pour le domaine qui empêche l’envoi de toute communication sur HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="68a76-212">Le navigateur force toutes les communications sur HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="68a76-213">Le navigateur empêche l’utilisateur d’utiliser des certificats non approuvés ou non valides.</span><span class="sxs-lookup"><span data-stu-id="68a76-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="68a76-214">Le navigateur désactive les invites qui permettent à un utilisateur d’approuver temporairement ce type de certificat.</span><span class="sxs-lookup"><span data-stu-id="68a76-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="68a76-215">Étant donné que HSTS est appliqué par le client, il présente certaines limitations :</span><span class="sxs-lookup"><span data-stu-id="68a76-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="68a76-216">Le client doit prendre en charge HSTS.</span><span class="sxs-lookup"><span data-stu-id="68a76-216">The client must support HSTS.</span></span>
* <span data-ttu-id="68a76-217">HSTS nécessite au moins une demande HTTPs réussie pour établir la stratégie HSTS.</span><span class="sxs-lookup"><span data-stu-id="68a76-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="68a76-218">L’application doit vérifier chaque requête HTTP et rediriger ou rejeter la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="68a76-219">ASP.NET Core 2,1 et versions ultérieures implémentent HSTS `UseHsts` avec la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="68a76-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="68a76-220">Le code suivant appelle `UseHsts` lorsque l’application n’est pas en [mode de développement](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="68a76-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="68a76-221">`UseHsts`n’est pas recommandé dans le développement, car les paramètres HSTS sont très facilement mis en cache par les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="68a76-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="68a76-222">Par défaut, `UseHsts` exclut l’adresse de bouclage locale.</span><span class="sxs-lookup"><span data-stu-id="68a76-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="68a76-223">Pour les environnements de production qui implémentent le protocole HTTPs pour la première fois, définissez [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) initiale sur une petite valeur à <xref:System.TimeSpan> l’aide de l’une des méthodes.</span><span class="sxs-lookup"><span data-stu-id="68a76-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="68a76-224">Définissez la valeur des heures sur un seul jour si vous devez rétablir l’infrastructure HTTPs sur HTTP.</span><span class="sxs-lookup"><span data-stu-id="68a76-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="68a76-225">Une fois que vous êtes certain de la durabilité de la configuration HTTPs, augmentez la valeur de HSTS `max-age` . une valeur couramment utilisée est d’un an.</span><span class="sxs-lookup"><span data-stu-id="68a76-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="68a76-226">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="68a76-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="68a76-227">Définit le paramètre de préchargement de l' `Strict-Transport-Security` en-tête.</span><span class="sxs-lookup"><span data-stu-id="68a76-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="68a76-228">Le préchargement ne fait pas partie de la [spécification RFC HSTS](https://tools.ietf.org/html/rfc6797), mais est pris en charge par les navigateurs Web pour précharger des sites HSTS sur une nouvelle installation.</span><span class="sxs-lookup"><span data-stu-id="68a76-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="68a76-229">Pour plus d’informations, consultez [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="68a76-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="68a76-230">Active [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), qui applique la stratégie HSTS pour héberger des sous-domaines.</span><span class="sxs-lookup"><span data-stu-id="68a76-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="68a76-231">Définit explicitement le `max-age` paramètre de l' `Strict-Transport-Security` en-tête à 60 jours.</span><span class="sxs-lookup"><span data-stu-id="68a76-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="68a76-232">S’il n’est pas défini, la valeur par défaut est 30 jours.</span><span class="sxs-lookup"><span data-stu-id="68a76-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="68a76-233">Pour plus d’informations, consultez la [directive max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="68a76-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="68a76-234">Ajoute `example.com` à la liste des hôtes à exclure.</span><span class="sxs-lookup"><span data-stu-id="68a76-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="68a76-235">`UseHsts`exclut les hôtes de bouclage suivants :</span><span class="sxs-lookup"><span data-stu-id="68a76-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="68a76-236">`localhost`: Adresse de bouclage IPv4.</span><span class="sxs-lookup"><span data-stu-id="68a76-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="68a76-237">`127.0.0.1`: Adresse de bouclage IPv4.</span><span class="sxs-lookup"><span data-stu-id="68a76-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="68a76-238">`[::1]`: Adresse de bouclage IPv6.</span><span class="sxs-lookup"><span data-stu-id="68a76-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="68a76-239">Désactiver HTTPs/HSTS lors de la création du projet</span><span class="sxs-lookup"><span data-stu-id="68a76-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="68a76-240">Dans certains scénarios de service backend où la sécurité de connexion est gérée au niveau du périmètre public du réseau, la configuration de la sécurité des connexions sur chaque nœud n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="68a76-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="68a76-241">Les applications Web générées à partir des modèles dans Visual Studio ou à partir de la commande [dotnet New](/dotnet/core/tools/dotnet-new) activent la [redirection https](#require-https) et [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="68a76-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="68a76-242">Pour les déploiements qui n’ont pas besoin de ces scénarios, vous pouvez désactiver HTTPs/HSTS quand l’application est créée à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="68a76-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="68a76-243">Pour désactiver HTTPs/HSTS :</span><span class="sxs-lookup"><span data-stu-id="68a76-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="68a76-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68a76-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="68a76-245">Désactivez la case à cocher **configurer pour HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="68a76-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Nouvelle boîte de dialogue d’application Web ASP.NET Core avec la case à cocher configurer pour HTTPs désélectionnée.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Nouvelle boîte de dialogue d’application Web ASP.NET Core avec la case à cocher configurer pour HTTPs désélectionnée.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="68a76-248">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="68a76-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="68a76-249">Utilisez l'option `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="68a76-249">Use the `--no-https` option.</span></span> <span data-ttu-id="68a76-250">Par exemple</span><span class="sxs-lookup"><span data-stu-id="68a76-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="68a76-251">Approuver le certificat de développement HTTPs ASP.NET Core sur Windows et macOS</span><span class="sxs-lookup"><span data-stu-id="68a76-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="68a76-252">Le kit SDK .NET Core comprend un certificat de développement HTTPs.</span><span class="sxs-lookup"><span data-stu-id="68a76-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="68a76-253">Le certificat est installé dans le cadre de la première exécution.</span><span class="sxs-lookup"><span data-stu-id="68a76-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="68a76-254">Par exemple, `dotnet --info` produit une variation de la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="68a76-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="68a76-255">L’installation du kit SDK .NET Core installe le certificat de développement ASP.NET Core HTTPS dans le magasin de certificats de l’utilisateur local.</span><span class="sxs-lookup"><span data-stu-id="68a76-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="68a76-256">Le certificat a été installé, mais il n’est pas approuvé.</span><span class="sxs-lookup"><span data-stu-id="68a76-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="68a76-257">Pour approuver le certificat, effectuez l’étape unique pour exécuter l’outil dotnet `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="68a76-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="68a76-258">La commande suivante fournit de l’aide sur l’outil `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="68a76-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="68a76-259">Comment configurer un certificat de développeur pour l’arrimeur</span><span class="sxs-lookup"><span data-stu-id="68a76-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="68a76-260">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="68a76-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="68a76-261">Approuver le certificat HTTPs à partir du sous-système Windows pour Linux</span><span class="sxs-lookup"><span data-stu-id="68a76-261">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="68a76-262">Le sous-système Windows pour Linux (WSL) génère un certificat auto-signé HTTPs. Pour configurer le magasin de certificats Windows de façon à ce qu’il approuve le certificat WSL :</span><span class="sxs-lookup"><span data-stu-id="68a76-262">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="68a76-263">Exécutez la commande suivante pour exporter le certificat généré par WSL :`dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="68a76-263">Run the following command to export the WSL-generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="68a76-264">Dans une fenêtre WSL, exécutez la commande suivante :`ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="68a76-264">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="68a76-265">La commande précédente définit les variables d’environnement pour que Linux utilise le certificat de confiance Windows.</span><span class="sxs-lookup"><span data-stu-id="68a76-265">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="68a76-266">Résoudre les problèmes de certificat</span><span class="sxs-lookup"><span data-stu-id="68a76-266">Troubleshoot certificate problems</span></span>

<span data-ttu-id="68a76-267">Cette section fournit de l’aide lorsque le certificat de développement HTTPs ASP.NET Core a été [installé et approuvé](#trust), mais que vous avez encore des avertissements de navigateur indiquant que le certificat n’est pas approuvé.</span><span class="sxs-lookup"><span data-stu-id="68a76-267">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="68a76-268">Le certificat de développement HTTPs ASP.NET Core est utilisé par [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="68a76-268">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="68a76-269">Toutes les plateformes-certificat non approuvé</span><span class="sxs-lookup"><span data-stu-id="68a76-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="68a76-270">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-270">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="68a76-271">Fermez toutes les instances de navigateur ouvertes.</span><span class="sxs-lookup"><span data-stu-id="68a76-271">Close any browser instances open.</span></span> <span data-ttu-id="68a76-272">Ouvrez une nouvelle fenêtre de navigateur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="68a76-272">Open a new browser window to app.</span></span> <span data-ttu-id="68a76-273">L’approbation de certificat est mise en cache par les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="68a76-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="68a76-274">Les commandes précédentes résolvent la plupart des problèmes d’approbation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="68a76-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="68a76-275">Si le navigateur n’approuve toujours pas le certificat, suivez les suggestions spécifiques à la plateforme qui suivent.</span><span class="sxs-lookup"><span data-stu-id="68a76-275">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="68a76-276">Dockr-certificat non approuvé</span><span class="sxs-lookup"><span data-stu-id="68a76-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="68a76-277">Supprimez le dossier *C:\Users\{User} \AppData\Roaming\ASP.NET\Https* .</span><span class="sxs-lookup"><span data-stu-id="68a76-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="68a76-278">Nettoyez la solution.</span><span class="sxs-lookup"><span data-stu-id="68a76-278">Clean the solution.</span></span> <span data-ttu-id="68a76-279">Supprimez les dossiers *bin* et *obj*.</span><span class="sxs-lookup"><span data-stu-id="68a76-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="68a76-280">Redémarrez l’outil de développement.</span><span class="sxs-lookup"><span data-stu-id="68a76-280">Restart the development tool.</span></span> <span data-ttu-id="68a76-281">Par exemple, Visual Studio, Visual Studio Code ou Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="68a76-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="68a76-282">Windows-certificat non approuvé</span><span class="sxs-lookup"><span data-stu-id="68a76-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="68a76-283">Vérifiez les certificats dans le magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="68a76-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="68a76-284">Il doit y avoir `localhost` un certificat avec `ASP.NET Core HTTPS development certificate` le nom convivial à `Current User > Personal > Certificates` la fois sous et`Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="68a76-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="68a76-285">Supprimez tous les certificats détectés des autorités de certification racines personnelles et de confiance.</span><span class="sxs-lookup"><span data-stu-id="68a76-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="68a76-286">Ne supprimez **pas** le certificat IIS Express localhost.</span><span class="sxs-lookup"><span data-stu-id="68a76-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="68a76-287">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="68a76-288">Fermez toutes les instances de navigateur ouvertes.</span><span class="sxs-lookup"><span data-stu-id="68a76-288">Close any browser instances open.</span></span> <span data-ttu-id="68a76-289">Ouvrez une nouvelle fenêtre de navigateur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="68a76-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="68a76-290">OS X-certificat non approuvé</span><span class="sxs-lookup"><span data-stu-id="68a76-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="68a76-291">Ouvrez le trousseau d’accès.</span><span class="sxs-lookup"><span data-stu-id="68a76-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="68a76-292">Sélectionnez le trousseau système.</span><span class="sxs-lookup"><span data-stu-id="68a76-292">Select the System keychain.</span></span>
* <span data-ttu-id="68a76-293">Vérifiez la présence d’un certificat localhost.</span><span class="sxs-lookup"><span data-stu-id="68a76-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="68a76-294">Vérifiez qu’il contient un `+` symbole sur l’icône pour indiquer qu’il est approuvé pour tous les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="68a76-294">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="68a76-295">Supprimez le certificat du trousseau système.</span><span class="sxs-lookup"><span data-stu-id="68a76-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="68a76-296">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="68a76-296">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="68a76-297">Fermez toutes les instances de navigateur ouvertes.</span><span class="sxs-lookup"><span data-stu-id="68a76-297">Close any browser instances open.</span></span> <span data-ttu-id="68a76-298">Ouvrez une nouvelle fenêtre de navigateur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="68a76-298">Open a new browser window to app.</span></span>

<span data-ttu-id="68a76-299">Consultez [erreur HTTPS à l’aide de IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) pour résoudre les problèmes liés aux certificats dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68a76-299">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="68a76-300">IIS Express certificat SSL utilisé avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68a76-300">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="68a76-301">Pour résoudre les problèmes liés au certificat de IIS Express, sélectionnez **réparer** dans le programme d’installation de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68a76-301">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="68a76-302">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="68a76-302">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="68a76-303">Informations supplémentaires</span><span class="sxs-lookup"><span data-stu-id="68a76-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="68a76-304">ASP.NET Core d’hôte sur Linux avec Apache : configuration HTTPs</span><span class="sxs-lookup"><span data-stu-id="68a76-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="68a76-305">ASP.NET Core d’hôte sur Linux avec Nginx : configuration HTTPs</span><span class="sxs-lookup"><span data-stu-id="68a76-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="68a76-306">Configuration du protocole SSL sur IIS</span><span class="sxs-lookup"><span data-stu-id="68a76-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="68a76-307">Prise en charge du navigateur OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="68a76-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
