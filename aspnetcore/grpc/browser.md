---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core à appeler à partir d’applications de navigateur à l’aide de gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106596"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="37392-103">Utiliser gRPC dans les applications de navigateur</span><span class="sxs-lookup"><span data-stu-id="37392-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="37392-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="37392-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="37392-105">Il n’est pas possible d’appeler un service gRPC HTTP/2 à partir d’une application basée sur un navigateur.</span><span class="sxs-lookup"><span data-stu-id="37392-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="37392-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet au navigateur JavaScript et Blazor aux applications d’appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="37392-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="37392-107">Cet article explique comment utiliser gRPC-Web dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="37392-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="37392-108">gRPC-Web dans ASP.NET Core vs. Envoy</span><span class="sxs-lookup"><span data-stu-id="37392-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="37392-109">Il existe deux façons d’ajouter gRPC-Web à une application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="37392-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="37392-110">Prenez en charge gRPC-Web en parallèle de gRPC HTTP/2 dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="37392-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="37392-111">Cette option utilise l’intergiciel (middleware) fourni par le `Grpc.AspNetCore.Web` Package.</span><span class="sxs-lookup"><span data-stu-id="37392-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="37392-112">Utilisez la prise en charge gRPC-Web [du proxy Envoy](https://www.envoyproxy.io/) pour traduire GRPC-Web en gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="37392-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="37392-113">L’appel traduit est ensuite transféré vers l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="37392-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="37392-114">Chaque approche présente des avantages et des inconvénients.</span><span class="sxs-lookup"><span data-stu-id="37392-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="37392-115">Si vous utilisez déjà Envoy en tant que proxy dans l’environnement de votre application, il peut être judicieux de l’utiliser également pour fournir la prise en charge de gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="37392-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="37392-116">Si vous souhaitez une solution simple pour gRPC-Web qui nécessite uniquement ASP.NET Core, `Grpc.AspNetCore.Web` est un bon choix.</span><span class="sxs-lookup"><span data-stu-id="37392-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="37392-117">Configurer gRPC-Web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37392-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="37392-118">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="37392-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="37392-119">gRPC-Web ne nécessite aucune modification des services.</span><span class="sxs-lookup"><span data-stu-id="37392-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="37392-120">La seule modification concerne la configuration du démarrage.</span><span class="sxs-lookup"><span data-stu-id="37392-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="37392-121">Pour activer gRPC-Web avec un service ASP.NET Core gRPC :</span><span class="sxs-lookup"><span data-stu-id="37392-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="37392-122">Ajoutez une référence au package [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="37392-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="37392-123">Configurez l’application pour utiliser gRPC-Web en ajoutant `UseGrpcWeb` et `EnableGrpcWeb` à *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="37392-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="37392-124">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="37392-124">The preceding code:</span></span>

* <span data-ttu-id="37392-125">Ajoute l’intergiciel (middleware) Web gRPC, `UseGrpcWeb` , après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="37392-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="37392-126">Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge gRPC-Web avec `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="37392-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="37392-127">L’intergiciel gRPC-Web peut également être configuré de sorte que tous les services prennent en charge gRPC-Web par défaut et non `EnableGrpcWeb` requis.</span><span class="sxs-lookup"><span data-stu-id="37392-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="37392-128">Spécifiez à `new GrpcWebOptions { DefaultEnabled = true }` quel moment l’intergiciel est ajouté.</span><span class="sxs-lookup"><span data-stu-id="37392-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="37392-129">Il existe un problème connu qui provoque l’échec de gRPC-Web lorsqu’il est [hébergé par http. sys](xref:fundamentals/servers/httpsys) dans .net Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="37392-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="37392-130">Une solution de contournement pour obtenir gRPC-Web Working sur http. sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="37392-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="37392-131">gRPC-Web et CORS</span><span class="sxs-lookup"><span data-stu-id="37392-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="37392-132">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="37392-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="37392-133">Cette restriction s’applique à la création d’appels gRPC-Web avec des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="37392-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="37392-134">Par exemple, une application de navigateur servie par `https://www.contoso.com` est bloquée de l’appel de gRPC-services Web hébergés sur `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="37392-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="37392-135">Le partage des ressources Cross-Origin (CORS) peut être utilisé pour assouplir cette restriction.</span><span class="sxs-lookup"><span data-stu-id="37392-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="37392-136">Pour permettre à votre application de navigateur d’effectuer des appels gRPC-Web Cross-Origin, configurez [cors dans ASP.net Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="37392-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="37392-137">Utilisez la prise en charge de CORS intégrée et exposez des en-têtes spécifiques à gRPC avec <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="37392-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="37392-138">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="37392-138">The preceding code:</span></span>

* <span data-ttu-id="37392-139">Appelle `AddCors` pour ajouter des services cors et configure une stratégie cors qui expose des en-têtes spécifiques à gRPC.</span><span class="sxs-lookup"><span data-stu-id="37392-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="37392-140">Appelle `UseCors` pour ajouter l’intergiciel (middleware) cors après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="37392-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="37392-141">Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge cors avec `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="37392-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="37392-142">Appeler gRPC-Web à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="37392-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="37392-143">Les applications de navigateur peuvent utiliser gRPC-Web pour appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="37392-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="37392-144">Il existe certaines exigences et limitations lors de l’appel de services gRPC avec gRPC-Web à partir du navigateur :</span><span class="sxs-lookup"><span data-stu-id="37392-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="37392-145">Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="37392-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="37392-146">Les appels de diffusion en continu et bidirectionnelle du client ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="37392-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="37392-147">La diffusion en continu du serveur est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="37392-147">Server streaming is supported.</span></span>
* <span data-ttu-id="37392-148">L’appel de services gRPC sur un autre domaine requiert la configuration de [cors](xref:security/cors) sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="37392-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="37392-149">JavaScript gRPC-client Web</span><span class="sxs-lookup"><span data-stu-id="37392-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="37392-150">Il existe un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="37392-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="37392-151">Pour obtenir des instructions sur l’utilisation de gRPC-Web à partir de JavaScript, consultez [écrire du code JavaScript client avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="37392-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="37392-152">Configurer gRPC-Web avec le client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="37392-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="37392-153">Le client .NET gRPC peut être configuré pour effectuer des appels gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="37392-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="37392-154">Cela est utile pour les applications [ Blazor webassembly](xref:blazor/index#blazor-webassembly) , qui sont hébergées dans le navigateur et qui ont les mêmes limitations http du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37392-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="37392-155">L’appel de gRPC-Web avec un client .NET est [identique à http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="37392-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="37392-156">La seule modification est la manière dont le canal est créé.</span><span class="sxs-lookup"><span data-stu-id="37392-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="37392-157">Pour utiliser gRPC-Web :</span><span class="sxs-lookup"><span data-stu-id="37392-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="37392-158">Ajoutez une référence au package [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="37392-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="37392-159">Assurez-vous que la référence au package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) est 2.29.0 ou supérieure.</span><span class="sxs-lookup"><span data-stu-id="37392-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="37392-160">Configurez le canal pour utiliser le `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="37392-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="37392-161">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="37392-161">The preceding code:</span></span>

* <span data-ttu-id="37392-162">Configure un canal pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="37392-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="37392-163">Crée un client et effectue un appel à l’aide du canal.</span><span class="sxs-lookup"><span data-stu-id="37392-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="37392-164">`GrpcWebHandler`possède les options de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="37392-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="37392-165">**InnerHandler**: sous-jacent <xref:System.Net.Http.HttpMessageHandler> qui effectue la requête http gRPC, par exemple, `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="37392-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="37392-166">**GrpcWebMode**: type énumération qui spécifie si la requête http gRPC `Content-Type` est `application/grpc-web` ou `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="37392-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="37392-167">`GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans encodage.</span><span class="sxs-lookup"><span data-stu-id="37392-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="37392-168">Valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="37392-168">Default value.</span></span>
    * <span data-ttu-id="37392-169">`GrpcWebMode.GrpcWebText`configure le contenu pour qu’il soit encodé en base64.</span><span class="sxs-lookup"><span data-stu-id="37392-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="37392-170">Requis pour les appels de diffusion en continu du serveur dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="37392-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="37392-171">**HttpVersion**: protocole http `Version` utilisé pour définir [HttpRequestMessage. version](xref:System.Net.Http.HttpRequestMessage.Version) sur la requête http gRPC sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="37392-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="37392-172">gRPC-Web n’a pas besoin d’une version spécifique et ne remplace pas la valeur par défaut, sauf indication contraire.</span><span class="sxs-lookup"><span data-stu-id="37392-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37392-173">Les clients gRPC générés ont des méthodes synchrones et asynchrones pour appeler des méthodes unaires.</span><span class="sxs-lookup"><span data-stu-id="37392-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="37392-174">Par exemple, `SayHello` est Sync et `SayHelloAsync` est Async.</span><span class="sxs-lookup"><span data-stu-id="37392-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="37392-175">L’appel d’une méthode Sync dans une Blazor application Webassembly entraîne la non-réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="37392-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="37392-176">Les méthodes Async doivent toujours être utilisées dans Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="37392-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37392-177">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37392-177">Additional resources</span></span>

* [<span data-ttu-id="37392-178">gRPC pour le projet GitHub des clients Web</span><span class="sxs-lookup"><span data-stu-id="37392-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
