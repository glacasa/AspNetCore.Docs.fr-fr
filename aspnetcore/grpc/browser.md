---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core à appeler à partir d’applications de navigateur à l’aide de gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774741"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="a5d84-103">Utiliser gRPC dans les applications de navigateur</span><span class="sxs-lookup"><span data-stu-id="a5d84-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="a5d84-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a5d84-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5d84-105">**gRPC-la prise en charge de Web dans .NET est expérimentale**</span><span class="sxs-lookup"><span data-stu-id="a5d84-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="a5d84-106">gRPC-Web pour .NET est un projet expérimental, et non un produit validé.</span><span class="sxs-lookup"><span data-stu-id="a5d84-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="a5d84-107">Nous voulons :</span><span class="sxs-lookup"><span data-stu-id="a5d84-107">We want to:</span></span>
>
> * <span data-ttu-id="a5d84-108">Testez que notre approche de l’implémentation de gRPC-Web fonctionne.</span><span class="sxs-lookup"><span data-stu-id="a5d84-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="a5d84-109">Recevez des commentaires sur si cette approche est utile pour les développeurs .NET par rapport à la méthode traditionnelle de configuration de gRPC-Web via un proxy.</span><span class="sxs-lookup"><span data-stu-id="a5d84-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="a5d84-110">N’hésitez pas [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) à nous faire part de vos commentaires.</span><span class="sxs-lookup"><span data-stu-id="a5d84-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="a5d84-111">Il n’est pas possible d’appeler un service gRPC HTTP/2 à partir d’une application basée sur un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a5d84-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="a5d84-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet au navigateur JavaScript Blazor et aux applications d’appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="a5d84-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="a5d84-113">Cet article explique comment utiliser gRPC-Web dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5d84-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="a5d84-114">gRPC-Web dans ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="a5d84-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="a5d84-115">Il existe deux façons d’ajouter gRPC-Web à une application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="a5d84-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="a5d84-116">Prenez en charge gRPC-Web en parallèle de gRPC HTTP/2 dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5d84-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="a5d84-117">Cette option utilise l’intergiciel (middleware) `Grpc.AspNetCore.Web` fourni par le package.</span><span class="sxs-lookup"><span data-stu-id="a5d84-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="a5d84-118">Utilisez la prise en charge gRPC-Web [du proxy Envoy](https://www.envoyproxy.io/) pour traduire GRPC-Web en gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="a5d84-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="a5d84-119">L’appel traduit est ensuite transféré vers l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5d84-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="a5d84-120">Chaque approche présente des avantages et des inconvénients.</span><span class="sxs-lookup"><span data-stu-id="a5d84-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="a5d84-121">Si vous utilisez déjà Envoy en tant que proxy dans l’environnement de votre application, il peut être judicieux de l’utiliser également pour fournir la prise en charge de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="a5d84-122">Si vous souhaitez une solution simple pour gRPC-Web qui nécessite uniquement ASP.NET Core, `Grpc.AspNetCore.Web` est un bon choix.</span><span class="sxs-lookup"><span data-stu-id="a5d84-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="a5d84-123">Configurer gRPC-Web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5d84-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="a5d84-124">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="a5d84-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="a5d84-125">gRPC-Web ne nécessite aucune modification des services.</span><span class="sxs-lookup"><span data-stu-id="a5d84-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="a5d84-126">La seule modification concerne la configuration du démarrage.</span><span class="sxs-lookup"><span data-stu-id="a5d84-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="a5d84-127">Pour activer gRPC-Web avec un service ASP.NET Core gRPC :</span><span class="sxs-lookup"><span data-stu-id="a5d84-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="a5d84-128">Ajoutez une référence au package [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="a5d84-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="a5d84-129">Configurez l’application pour utiliser gRPC-Web `AddGrpcWeb` en `UseGrpcWeb` ajoutant et à *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5d84-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="a5d84-130">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a5d84-130">The preceding code:</span></span>

* <span data-ttu-id="a5d84-131">Ajoute l’intergiciel (middleware) Web gRPC `UseGrpcWeb`,, après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="a5d84-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="a5d84-132">Spécifie `endpoints.MapGrpcService<GreeterService>()` que la méthode prend en charge `EnableGrpcWeb`gRPC-Web avec.</span><span class="sxs-lookup"><span data-stu-id="a5d84-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="a5d84-133">Vous pouvez également configurer tous les services pour prendre en charge gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` Web en ajoutant à ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="a5d84-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="a5d84-134">Il existe un problème connu qui provoque l’échec de gRPC-Web lorsqu’il est [hébergé par http. sys](xref:fundamentals/servers/httpsys) dans .net Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="a5d84-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="a5d84-135">Une solution de contournement pour obtenir gRPC-Web Working sur http. sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="a5d84-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="a5d84-136">gRPC-Web et CORS</span><span class="sxs-lookup"><span data-stu-id="a5d84-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="a5d84-137">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a5d84-138">Cette restriction s’applique à la création d’appels gRPC-Web avec des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="a5d84-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="a5d84-139">Par exemple, une application de navigateur servie par est bloquée de l' `https://www.contoso.com` appel de gRPC `https://services.contoso.com`-services Web hébergés sur.</span><span class="sxs-lookup"><span data-stu-id="a5d84-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="a5d84-140">Le partage des ressources Cross-Origin (CORS) peut être utilisé pour assouplir cette restriction.</span><span class="sxs-lookup"><span data-stu-id="a5d84-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="a5d84-141">Pour permettre à votre application de navigateur d’effectuer des appels gRPC-Web Cross-Origin, configurez [cors dans ASP.net Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="a5d84-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="a5d84-142">Utilisez la prise en charge de CORS intégrée et exposez des en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>spécifiques à gRPC avec.</span><span class="sxs-lookup"><span data-stu-id="a5d84-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="a5d84-143">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a5d84-143">The preceding code:</span></span>

* <span data-ttu-id="a5d84-144">Appelle `AddCors` pour ajouter des services cors et configure une stratégie cors qui expose des en-têtes spécifiques à gRPC.</span><span class="sxs-lookup"><span data-stu-id="a5d84-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="a5d84-145">Appelle `UseCors` pour ajouter l’intergiciel (middleware) cors après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="a5d84-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="a5d84-146">Spécifie `endpoints.MapGrpcService<GreeterService>()` que la méthode prend `RequiresCors`en charge cors avec.</span><span class="sxs-lookup"><span data-stu-id="a5d84-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="a5d84-147">Appeler gRPC-Web à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="a5d84-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="a5d84-148">Les applications de navigateur peuvent utiliser gRPC-Web pour appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="a5d84-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="a5d84-149">Il existe certaines exigences et limitations lors de l’appel de services gRPC avec gRPC-Web à partir du navigateur :</span><span class="sxs-lookup"><span data-stu-id="a5d84-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="a5d84-150">Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="a5d84-151">Les appels de diffusion en continu et bidirectionnelle du client ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a5d84-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="a5d84-152">La diffusion en continu du serveur est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="a5d84-152">Server streaming is supported.</span></span>
* <span data-ttu-id="a5d84-153">L’appel de services gRPC sur un autre domaine requiert la configuration de [cors](xref:security/cors) sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a5d84-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="a5d84-154">JavaScript gRPC-client Web</span><span class="sxs-lookup"><span data-stu-id="a5d84-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="a5d84-155">Il existe un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="a5d84-156">Pour obtenir des instructions sur l’utilisation de gRPC-Web à partir de JavaScript, consultez [écrire du code JavaScript client avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="a5d84-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="a5d84-157">Configurer gRPC-Web avec le client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="a5d84-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="a5d84-158">Le client .NET gRPC peut être configuré pour effectuer des appels gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="a5d84-159">Cela est utile pour [ Blazor les applications webassembly](xref:blazor/index#blazor-webassembly) , qui sont hébergées dans le navigateur et qui ont les mêmes limitations http du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a5d84-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="a5d84-160">L’appel de gRPC-Web avec un client .NET est [identique à http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="a5d84-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="a5d84-161">La seule modification est la manière dont le canal est créé.</span><span class="sxs-lookup"><span data-stu-id="a5d84-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="a5d84-162">Pour utiliser gRPC-Web :</span><span class="sxs-lookup"><span data-stu-id="a5d84-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="a5d84-163">Ajoutez une référence au package [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="a5d84-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="a5d84-164">Assurez-vous que la référence au package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) est 2.27.0 ou supérieure.</span><span class="sxs-lookup"><span data-stu-id="a5d84-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="a5d84-165">Configurez le canal pour `GrpcWebHandler`utiliser le :</span><span class="sxs-lookup"><span data-stu-id="a5d84-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="a5d84-166">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="a5d84-166">The preceding code:</span></span>

* <span data-ttu-id="a5d84-167">Configure un canal pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a5d84-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="a5d84-168">Crée un client et effectue un appel à l’aide du canal.</span><span class="sxs-lookup"><span data-stu-id="a5d84-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="a5d84-169">A `GrpcWebHandler` les options de configuration suivantes lors de la création :</span><span class="sxs-lookup"><span data-stu-id="a5d84-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="a5d84-170">**InnerHandler**: sous- <xref:System.Net.Http.HttpMessageHandler> jacent qui effectue la requête http gRPC, par exemple `HttpClientHandler`,.</span><span class="sxs-lookup"><span data-stu-id="a5d84-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="a5d84-171">**Mode**: type énumération qui spécifie si la requête `Content-Type` http gRPC `application/grpc-web` est `application/grpc-web-text`ou.</span><span class="sxs-lookup"><span data-stu-id="a5d84-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="a5d84-172">`GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans encodage.</span><span class="sxs-lookup"><span data-stu-id="a5d84-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="a5d84-173">Valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="a5d84-173">Default value.</span></span>
    * <span data-ttu-id="a5d84-174">`GrpcWebMode.GrpcWebText`configure le contenu pour qu’il soit encodé en base64.</span><span class="sxs-lookup"><span data-stu-id="a5d84-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="a5d84-175">Requis pour les appels de diffusion en continu du serveur dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="a5d84-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="a5d84-176">**HttpVersion**: protocole `Version` http utilisé pour définir [HttpRequestMessage. version](xref:System.Net.Http.HttpRequestMessage.Version) sur la requête http gRPC sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="a5d84-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="a5d84-177">gRPC-Web n’a pas besoin d’une version spécifique et ne remplace pas la valeur par défaut, sauf indication contraire.</span><span class="sxs-lookup"><span data-stu-id="a5d84-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5d84-178">Les clients gRPC générés ont des méthodes synchrones et asynchrones pour appeler des méthodes unaires.</span><span class="sxs-lookup"><span data-stu-id="a5d84-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="a5d84-179">Par exemple, `SayHello` est Sync et `SayHelloAsync` est Async.</span><span class="sxs-lookup"><span data-stu-id="a5d84-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="a5d84-180">L’appel d’une méthode Sync Blazor dans une application webassembly entraîne la non-réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="a5d84-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="a5d84-181">Les méthodes Async doivent toujours être utilisées Blazor dans webassembly.</span><span class="sxs-lookup"><span data-stu-id="a5d84-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5d84-182">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a5d84-182">Additional resources</span></span>

* [<span data-ttu-id="a5d84-183">gRPC pour le projet GitHub des clients Web</span><span class="sxs-lookup"><span data-stu-id="a5d84-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
