---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core pour être callable à partir d’applications de navigateur utilisant gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977143"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="fad49-103">Utiliser gRPC dans les applications de navigateur</span><span class="sxs-lookup"><span data-stu-id="fad49-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="fad49-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fad49-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fad49-105">**gRPC-Web support in .NET est expérimental**</span><span class="sxs-lookup"><span data-stu-id="fad49-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="fad49-106">gRPC-Web pour .NET est un projet expérimental, pas un produit engagé.</span><span class="sxs-lookup"><span data-stu-id="fad49-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="fad49-107">Nous voulons :</span><span class="sxs-lookup"><span data-stu-id="fad49-107">We want to:</span></span>
>
> * <span data-ttu-id="fad49-108">Testez que notre approche de la mise en œuvre de gRPC-Web fonctionne.</span><span class="sxs-lookup"><span data-stu-id="fad49-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="fad49-109">Obtenez des commentaires sur si cette approche est utile pour les développeurs .NET par rapport à la façon traditionnelle de mettre en place gRPC-Web via un proxy.</span><span class="sxs-lookup"><span data-stu-id="fad49-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="fad49-110">S’il [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) vous plaît laisser des commentaires pour s’assurer que nous construisons quelque chose que les développeurs aiment et sont productifs avec.</span><span class="sxs-lookup"><span data-stu-id="fad49-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="fad49-111">Il n’est pas possible d’appeler un service HTTP/2 gRPC à partir d’une application basée sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="fad49-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="fad49-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet aux applications JavaScript et Blazor d’appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="fad49-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="fad49-113">Cet article explique comment utiliser gRPC-Web dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fad49-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="fad49-114">Configurer gRPC-Web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fad49-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="fad49-115">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web aux côtés de HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="fad49-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="fad49-116">gRPC-Web n’exige aucune modification des services.</span><span class="sxs-lookup"><span data-stu-id="fad49-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="fad49-117">La seule modification est la configuration de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fad49-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="fad49-118">Pour activer gRPC-Web avec un service ASP.NET Core gRPC :</span><span class="sxs-lookup"><span data-stu-id="fad49-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="fad49-119">Ajoutez une référence au forfait [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="fad49-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="fad49-120">Configurer l’application pour utiliser gRPC-Web en ajoutant `AddGrpcWeb` et `UseGrpcWeb` en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fad49-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="fad49-121">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fad49-121">The preceding code:</span></span>

* <span data-ttu-id="fad49-122">Ajoute le gRPC-Web `UseGrpcWeb`middleware, , après routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="fad49-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="fad49-123">Spécifie la `endpoints.MapGrpcService<GreeterService>()` méthode prend `EnableGrpcWeb`en charge gRPC-Web avec .</span><span class="sxs-lookup"><span data-stu-id="fad49-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="fad49-124">Alternativement, configurez tous les services pour `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` prendre en charge gRPC-Web en ajoutant à ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="fad49-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="fad49-125">Il ya un problème connu qui provoque gRPC-Web à l’échec [lorsqu’il est hébergé par Http.sys](xref:fundamentals/servers/httpsys) dans .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="fad49-125">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="fad49-126">Une solution de contournement pour obtenir gRPC-Web de travail sur Http.sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="fad49-126">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="fad49-127">gRPC-Web et CORS</span><span class="sxs-lookup"><span data-stu-id="fad49-127">gRPC-Web and CORS</span></span>

<span data-ttu-id="fad49-128">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="fad49-128">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="fad49-129">Cette restriction s’applique à la prise d’appels gRPC-Web avec des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="fad49-129">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="fad49-130">Par exemple, une application `https://www.contoso.com` de navigateur desservie par est empêchée `https://services.contoso.com`d’appeler les services gRPC-Web hébergés sur .</span><span class="sxs-lookup"><span data-stu-id="fad49-130">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="fad49-131">Le partage des ressources d’origine croisée (CORS) peut être utilisé pour assouplir cette restriction.</span><span class="sxs-lookup"><span data-stu-id="fad49-131">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="fad49-132">Pour permettre à votre application de navigateur de passer des appels gRPC-Web d’origine croisée, configurez [CORS dans ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="fad49-132">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="fad49-133">Utilisez le support CORS intégré, et exposez les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>en-têtes spécifiques à gRPC avec .</span><span class="sxs-lookup"><span data-stu-id="fad49-133">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="fad49-134">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fad49-134">The preceding code:</span></span>

* <span data-ttu-id="fad49-135">Appels `AddCors` à ajouter des services CORS et configure une stratégie CORS qui expose les en-têtes spécifiques à gRPC.</span><span class="sxs-lookup"><span data-stu-id="fad49-135">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="fad49-136">Appels `UseCors` pour ajouter le middleware CORS après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="fad49-136">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="fad49-137">Specifie `endpoints.MapGrpcService<GreeterService>()` la méthode prend `RequiresCors`en charge CORS avec .</span><span class="sxs-lookup"><span data-stu-id="fad49-137">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="fad49-138">Appelez gRPC-Web à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="fad49-138">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="fad49-139">Les applications de navigateur peuvent utiliser gRPC-Web pour appeler les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="fad49-139">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="fad49-140">Il ya quelques exigences et limitations lors de l’appel des services gRPC avec gRPC-Web à partir du navigateur:</span><span class="sxs-lookup"><span data-stu-id="fad49-140">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="fad49-141">Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fad49-141">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="fad49-142">Le streaming client et les appels bidirectionnels en streaming ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fad49-142">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="fad49-143">Le streaming serveur est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fad49-143">Server streaming is supported.</span></span>
* <span data-ttu-id="fad49-144">L’appel des services gRPC sur un domaine différent nécessite [corS](xref:security/cors) d’être configuré sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fad49-144">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="fad49-145">JavaScript gRPC-Web client</span><span class="sxs-lookup"><span data-stu-id="fad49-145">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="fad49-146">Il y a un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fad49-146">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="fad49-147">Pour obtenir des instructions sur la façon d’utiliser gRPC-Web à partir de JavaScript, voir [écrire le code client JavaScript avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="fad49-147">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="fad49-148">Configurer gRPC-Web avec le client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="fad49-148">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="fad49-149">Le client .NET gRPC peut être configuré pour passer des appels gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fad49-149">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="fad49-150">Ceci est utile pour les applications [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) qui sont hébergées dans le navigateur et ont les mêmes limites HTTP de code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fad49-150">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="fad49-151">Appeler gRPC-Web avec un client .NET est [le même que HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="fad49-151">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="fad49-152">La seule modification est la façon dont le canal est créé.</span><span class="sxs-lookup"><span data-stu-id="fad49-152">The only modification is how the channel is created.</span></span>

<span data-ttu-id="fad49-153">Pour utiliser gRPC-Web :</span><span class="sxs-lookup"><span data-stu-id="fad49-153">To use gRPC-Web:</span></span>

* <span data-ttu-id="fad49-154">Ajoutez une référence au forfait [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="fad49-154">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="fad49-155">Assurez-vous que la référence au forfait [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) est de 2.27.0 ou plus.</span><span class="sxs-lookup"><span data-stu-id="fad49-155">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="fad49-156">Configurer le canal `GrpcWebHandler`pour utiliser le :</span><span class="sxs-lookup"><span data-stu-id="fad49-156">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="fad49-157">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fad49-157">The preceding code:</span></span>

* <span data-ttu-id="fad49-158">Configure un canal pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fad49-158">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="fad49-159">Crée un client et passe un appel à l’aide du canal.</span><span class="sxs-lookup"><span data-stu-id="fad49-159">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="fad49-160">Le `GrpcWebHandler` a les options de configuration suivantes lors de la création:</span><span class="sxs-lookup"><span data-stu-id="fad49-160">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="fad49-161">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Le sous-jacent qui fait la `HttpClientHandler`demande gRPC HTTP, par exemple, .</span><span class="sxs-lookup"><span data-stu-id="fad49-161">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="fad49-162">**Mode**: Un type d’énumération qui précise si `Content-Type` la `application/grpc-web` `application/grpc-web-text`demande de demande http://http de gRPC est ou .</span><span class="sxs-lookup"><span data-stu-id="fad49-162">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="fad49-163">`GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans codage.</span><span class="sxs-lookup"><span data-stu-id="fad49-163">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="fad49-164">Valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="fad49-164">Default value.</span></span>
    * <span data-ttu-id="fad49-165">`GrpcWebMode.GrpcWebText`configure le contenu à coder de base64.</span><span class="sxs-lookup"><span data-stu-id="fad49-165">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="fad49-166">Nécessaire pour les appels de streaming serveur dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="fad49-166">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="fad49-167">**HttpVersion**: `Version` Protocole HTTP utilisé pour définir [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) sur la demande httpRPC HTTP sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="fad49-167">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="fad49-168">gRPC-Web ne nécessite pas de version spécifique et ne remplace pas la valeur par défaut à moins d’être spécifiée.</span><span class="sxs-lookup"><span data-stu-id="fad49-168">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fad49-169">Les clients générés de gRPC ont des méthodes de synchronisation et d’async pour appeler des méthodes nonary.</span><span class="sxs-lookup"><span data-stu-id="fad49-169">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="fad49-170">Par exemple, `SayHello` est `SayHelloAsync` synchronisé et est async.</span><span class="sxs-lookup"><span data-stu-id="fad49-170">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="fad49-171">L’appel d’une méthode de synchronisation dans une application WebAssembly De Blazor entraînera une réponse imprésive de l’application.</span><span class="sxs-lookup"><span data-stu-id="fad49-171">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="fad49-172">Les méthodes Async doivent toujours être utilisées dans Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="fad49-172">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fad49-173">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fad49-173">Additional resources</span></span>

* [<span data-ttu-id="fad49-174">gRPC pour le projet GitHub de clients Web</span><span class="sxs-lookup"><span data-stu-id="fad49-174">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
