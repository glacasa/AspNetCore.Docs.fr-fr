---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core pour être callable à partir d’applications de navigateur utilisant gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440764"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="8987a-103">Utiliser gRPC dans les applications de navigateur</span><span class="sxs-lookup"><span data-stu-id="8987a-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="8987a-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8987a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8987a-105">**gRPC-Web support in .NET est expérimental**</span><span class="sxs-lookup"><span data-stu-id="8987a-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="8987a-106">gRPC-Web pour .NET est un projet expérimental, pas un produit engagé.</span><span class="sxs-lookup"><span data-stu-id="8987a-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="8987a-107">Nous voulons :</span><span class="sxs-lookup"><span data-stu-id="8987a-107">We want to:</span></span>
>
> * <span data-ttu-id="8987a-108">Testez que notre approche de la mise en œuvre de gRPC-Web fonctionne.</span><span class="sxs-lookup"><span data-stu-id="8987a-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="8987a-109">Obtenez des commentaires sur si cette approche est utile pour les développeurs .NET par rapport à la façon traditionnelle de mettre en place gRPC-Web via un proxy.</span><span class="sxs-lookup"><span data-stu-id="8987a-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="8987a-110">S’il [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) vous plaît laisser des commentaires pour s’assurer que nous construisons quelque chose que les développeurs aiment et sont productifs avec.</span><span class="sxs-lookup"><span data-stu-id="8987a-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="8987a-111">Il n’est pas possible d’appeler un service HTTP/2 gRPC à partir d’une application basée sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="8987a-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="8987a-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet aux applications JavaScript et Blazor d’appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="8987a-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="8987a-113">Cet article explique comment utiliser gRPC-Web dans .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8987a-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="8987a-114">gRPC-Web dans ASP.NET Core vs Envoyé</span><span class="sxs-lookup"><span data-stu-id="8987a-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="8987a-115">Il ya deux choix pour la façon d’ajouter gRPC-Web à une application ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8987a-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="8987a-116">Prendre en charge gRPC-Web aux côtés de gRPC HTTP/2 dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8987a-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="8987a-117">Cette option utilise middleware `Grpc.AspNetCore.Web` fourni par l’emballage.</span><span class="sxs-lookup"><span data-stu-id="8987a-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="8987a-118">Utilisez le support gRPC-Web [de la procuration Envoyé](https://www.envoyproxy.io/) pour traduire gRPC-Web en gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8987a-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="8987a-119">L’appel traduit est ensuite transmis à l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8987a-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="8987a-120">Il y a des avantages et des inconvénients à chaque approche.</span><span class="sxs-lookup"><span data-stu-id="8987a-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="8987a-121">Si vous utilisez déjà Envoy comme proxy dans l’environnement de votre application, il pourrait être judicieux de l’utiliser également pour fournir un support gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="8987a-122">Si vous voulez une solution simple pour gRPC-Web `Grpc.AspNetCore.Web` qui ne nécessite que ASP.NET Core, est un bon choix.</span><span class="sxs-lookup"><span data-stu-id="8987a-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="8987a-123">Configurer gRPC-Web dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8987a-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="8987a-124">les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web aux côtés de HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="8987a-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="8987a-125">gRPC-Web n’exige aucune modification des services.</span><span class="sxs-lookup"><span data-stu-id="8987a-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="8987a-126">La seule modification est la configuration de démarrage.</span><span class="sxs-lookup"><span data-stu-id="8987a-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="8987a-127">Pour activer gRPC-Web avec un service ASP.NET Core gRPC :</span><span class="sxs-lookup"><span data-stu-id="8987a-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="8987a-128">Ajoutez une référence au forfait [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="8987a-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="8987a-129">Configurer l’application pour utiliser gRPC-Web en ajoutant `AddGrpcWeb` et `UseGrpcWeb` en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8987a-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="8987a-130">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="8987a-130">The preceding code:</span></span>

* <span data-ttu-id="8987a-131">Ajoute le gRPC-Web `UseGrpcWeb`middleware, , après routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="8987a-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="8987a-132">Spécifie la `endpoints.MapGrpcService<GreeterService>()` méthode prend `EnableGrpcWeb`en charge gRPC-Web avec .</span><span class="sxs-lookup"><span data-stu-id="8987a-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="8987a-133">Alternativement, configurez tous les services pour `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` prendre en charge gRPC-Web en ajoutant à ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="8987a-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="8987a-134">Il ya un problème connu qui provoque gRPC-Web à l’échec [lorsqu’il est hébergé par Http.sys](xref:fundamentals/servers/httpsys) dans .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="8987a-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="8987a-135">Une solution de contournement pour obtenir gRPC-Web de travail sur Http.sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="8987a-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="8987a-136">gRPC-Web et CORS</span><span class="sxs-lookup"><span data-stu-id="8987a-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="8987a-137">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8987a-138">Cette restriction s’applique à la prise d’appels gRPC-Web avec des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="8987a-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="8987a-139">Par exemple, une application `https://www.contoso.com` de navigateur desservie par est empêchée `https://services.contoso.com`d’appeler les services gRPC-Web hébergés sur .</span><span class="sxs-lookup"><span data-stu-id="8987a-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="8987a-140">Le partage des ressources d’origine croisée (CORS) peut être utilisé pour assouplir cette restriction.</span><span class="sxs-lookup"><span data-stu-id="8987a-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="8987a-141">Pour permettre à votre application de navigateur de passer des appels gRPC-Web d’origine croisée, configurez [CORS dans ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="8987a-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="8987a-142">Utilisez le support CORS intégré, et exposez les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>en-têtes spécifiques à gRPC avec .</span><span class="sxs-lookup"><span data-stu-id="8987a-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="8987a-143">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="8987a-143">The preceding code:</span></span>

* <span data-ttu-id="8987a-144">Appels `AddCors` à ajouter des services CORS et configure une stratégie CORS qui expose les en-têtes spécifiques à gRPC.</span><span class="sxs-lookup"><span data-stu-id="8987a-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="8987a-145">Appels `UseCors` pour ajouter le middleware CORS après le routage et avant les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="8987a-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="8987a-146">Specifie `endpoints.MapGrpcService<GreeterService>()` la méthode prend `RequiresCors`en charge CORS avec .</span><span class="sxs-lookup"><span data-stu-id="8987a-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="8987a-147">Appelez gRPC-Web à partir du navigateur</span><span class="sxs-lookup"><span data-stu-id="8987a-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="8987a-148">Les applications de navigateur peuvent utiliser gRPC-Web pour appeler les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="8987a-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="8987a-149">Il ya quelques exigences et limitations lors de l’appel des services gRPC avec gRPC-Web à partir du navigateur:</span><span class="sxs-lookup"><span data-stu-id="8987a-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="8987a-150">Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="8987a-151">Le streaming client et les appels bidirectionnels en streaming ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="8987a-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="8987a-152">Le streaming serveur est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="8987a-152">Server streaming is supported.</span></span>
* <span data-ttu-id="8987a-153">L’appel des services gRPC sur un domaine différent nécessite [corS](xref:security/cors) d’être configuré sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="8987a-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="8987a-154">JavaScript gRPC-Web client</span><span class="sxs-lookup"><span data-stu-id="8987a-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="8987a-155">Il y a un client JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="8987a-156">Pour obtenir des instructions sur la façon d’utiliser gRPC-Web à partir de JavaScript, voir [écrire le code client JavaScript avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="8987a-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="8987a-157">Configurer gRPC-Web avec le client .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="8987a-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="8987a-158">Le client .NET gRPC peut être configuré pour passer des appels gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="8987a-159">Ceci est utile pour les applications [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) qui sont hébergées dans le navigateur et ont les mêmes limites HTTP de code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8987a-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="8987a-160">Appeler gRPC-Web avec un client .NET est [le même que HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="8987a-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="8987a-161">La seule modification est la façon dont le canal est créé.</span><span class="sxs-lookup"><span data-stu-id="8987a-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="8987a-162">Pour utiliser gRPC-Web :</span><span class="sxs-lookup"><span data-stu-id="8987a-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="8987a-163">Ajoutez une référence au forfait [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="8987a-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="8987a-164">Assurez-vous que la référence au forfait [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) est de 2.27.0 ou plus.</span><span class="sxs-lookup"><span data-stu-id="8987a-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="8987a-165">Configurer le canal `GrpcWebHandler`pour utiliser le :</span><span class="sxs-lookup"><span data-stu-id="8987a-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="8987a-166">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="8987a-166">The preceding code:</span></span>

* <span data-ttu-id="8987a-167">Configure un canal pour utiliser gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8987a-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="8987a-168">Crée un client et passe un appel à l’aide du canal.</span><span class="sxs-lookup"><span data-stu-id="8987a-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="8987a-169">Le `GrpcWebHandler` a les options de configuration suivantes lors de la création:</span><span class="sxs-lookup"><span data-stu-id="8987a-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="8987a-170">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Le sous-jacent qui fait la `HttpClientHandler`demande gRPC HTTP, par exemple, .</span><span class="sxs-lookup"><span data-stu-id="8987a-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="8987a-171">**Mode**: Un type d’énumération qui précise si `Content-Type` `application/grpc-web` la `application/grpc-web-text`demande http://http de gRPC est ou .</span><span class="sxs-lookup"><span data-stu-id="8987a-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="8987a-172">`GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans codage.</span><span class="sxs-lookup"><span data-stu-id="8987a-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="8987a-173">Valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="8987a-173">Default value.</span></span>
    * <span data-ttu-id="8987a-174">`GrpcWebMode.GrpcWebText`configure le contenu à coder de base64.</span><span class="sxs-lookup"><span data-stu-id="8987a-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="8987a-175">Nécessaire pour les appels de streaming serveur dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="8987a-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="8987a-176">**HttpVersion**: `Version` Protocole HTTP utilisé pour définir [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) sur la demande httpRPC HTTP sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="8987a-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="8987a-177">gRPC-Web ne nécessite pas de version spécifique et ne remplace pas la valeur par défaut à moins d’être spécifiée.</span><span class="sxs-lookup"><span data-stu-id="8987a-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8987a-178">Les clients générés de gRPC ont des méthodes de synchronisation et d’async pour appeler des méthodes nonary.</span><span class="sxs-lookup"><span data-stu-id="8987a-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="8987a-179">Par exemple, `SayHello` est `SayHelloAsync` synchronisé et est async.</span><span class="sxs-lookup"><span data-stu-id="8987a-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="8987a-180">L’appel d’une méthode de synchronisation dans une application WebAssembly De Blazor entraînera une réponse imprésive de l’application.</span><span class="sxs-lookup"><span data-stu-id="8987a-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="8987a-181">Les méthodes Async doivent toujours être utilisées dans Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8987a-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8987a-182">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8987a-182">Additional resources</span></span>

* [<span data-ttu-id="8987a-183">gRPC pour le projet GitHub de clients Web</span><span class="sxs-lookup"><span data-stu-id="8987a-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
