---
title: Meilleures pratiques en matière de performances dans gRPC pour ASP.NET Core
author: jamesnk
description: Découvrez les meilleures pratiques pour créer des services gRPC à hautes performances.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876722"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="71516-103">Meilleures pratiques en matière de performances dans gRPC pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71516-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="71516-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="71516-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="71516-105">gRPC est conçu pour les services à hautes performances.</span><span class="sxs-lookup"><span data-stu-id="71516-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="71516-106">Ce document explique comment obtenir les meilleures performances possibles à partir de gRPC.</span><span class="sxs-lookup"><span data-stu-id="71516-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="71516-107">Réutiliser le canal</span><span class="sxs-lookup"><span data-stu-id="71516-107">Reuse channel</span></span>

<span data-ttu-id="71516-108">Un canal gRPC doit être réutilisé pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="71516-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="71516-109">La réutilisation d’un canal permet de multiplexer les appels par le biais d’une connexion HTTP/2 existante.</span><span class="sxs-lookup"><span data-stu-id="71516-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="71516-110">Si un canal est créé pour chaque appel gRPC, le temps nécessaire à l’exécution peut augmenter de manière significative.</span><span class="sxs-lookup"><span data-stu-id="71516-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="71516-111">Chaque appel nécessite plusieurs allers-retours sur le réseau entre le client et le serveur pour créer une connexion HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="71516-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="71516-112">Ouverture d’un socket</span><span class="sxs-lookup"><span data-stu-id="71516-112">Opening a socket</span></span>
2. <span data-ttu-id="71516-113">Établissement d’une connexion TCP</span><span class="sxs-lookup"><span data-stu-id="71516-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="71516-114">Négociation de TLS</span><span class="sxs-lookup"><span data-stu-id="71516-114">Negotiating TLS</span></span>
4. <span data-ttu-id="71516-115">Démarrage de la connexion HTTP/2</span><span class="sxs-lookup"><span data-stu-id="71516-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="71516-116">Exécution de l’appel gRPC</span><span class="sxs-lookup"><span data-stu-id="71516-116">Making the gRPC call</span></span>

<span data-ttu-id="71516-117">Les canaux peuvent être partagés et réutilisés en toute sécurité entre les appels gRPC :</span><span class="sxs-lookup"><span data-stu-id="71516-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="71516-118">les clients gRPC sont créés avec des canaux.</span><span class="sxs-lookup"><span data-stu-id="71516-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="71516-119">les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.</span><span class="sxs-lookup"><span data-stu-id="71516-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="71516-120">Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.</span><span class="sxs-lookup"><span data-stu-id="71516-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="71516-121">Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="71516-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="71516-122">Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.</span><span class="sxs-lookup"><span data-stu-id="71516-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="71516-123">Accès concurrentiel de la connexion</span><span class="sxs-lookup"><span data-stu-id="71516-123">Connection concurrency</span></span>

<span data-ttu-id="71516-124">Les connexions HTTP/2 ont généralement une limite du nombre [maximal de flux simultanés (requêtes http actives)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) sur une connexion à la fois.</span><span class="sxs-lookup"><span data-stu-id="71516-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="71516-125">Par défaut, la plupart des serveurs définissent cette limite sur 100 flux simultanés.</span><span class="sxs-lookup"><span data-stu-id="71516-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="71516-126">Un canal gRPC utilise une seule connexion HTTP/2, et les appels simultanés sont multiplexés sur cette connexion.</span><span class="sxs-lookup"><span data-stu-id="71516-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="71516-127">Lorsque le nombre d’appels actifs atteint la limite du flux de connexion, les appels supplémentaires sont mis en file d’attente dans le client.</span><span class="sxs-lookup"><span data-stu-id="71516-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="71516-128">Les appels en file d’attente attendent que les appels actifs se terminent avant d’être envoyés.</span><span class="sxs-lookup"><span data-stu-id="71516-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="71516-129">Les applications avec une charge élevée, ou des appels de diffusion en continu à long terme, peuvent voir des problèmes de performances dus à la mise en file d’attente des appels en raison de cette limite.</span><span class="sxs-lookup"><span data-stu-id="71516-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="71516-130">.NET 5 introduit la `SocketsHttpHandler.EnableMultipleHttp2Connections` propriété.</span><span class="sxs-lookup"><span data-stu-id="71516-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="71516-131">Quand la valeur `true` est, les connexions http/2 supplémentaires sont créées par un canal lorsque la limite de flux simultanée est atteinte.</span><span class="sxs-lookup"><span data-stu-id="71516-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="71516-132">Lorsqu’un `GrpcChannel` est créé, son interne `SocketsHttpHandler` est automatiquement configuré pour créer des connexions http/2 supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="71516-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="71516-133">Si une application configure son propre gestionnaire, envisagez `EnableMultipleHttp2Connections` de définir sur `true` :</span><span class="sxs-lookup"><span data-stu-id="71516-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="71516-134">Il existe deux solutions de contournement pour les applications .NET Core 3,1 :</span><span class="sxs-lookup"><span data-stu-id="71516-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="71516-135">Créez des canaux gRPC distincts pour les zones de l’application avec une charge élevée.</span><span class="sxs-lookup"><span data-stu-id="71516-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="71516-136">Par exemple, le `Logger` service gRPC peut avoir une charge élevée.</span><span class="sxs-lookup"><span data-stu-id="71516-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="71516-137">Utilisez un canal distinct pour créer le `LoggerClient` dans l’application.</span><span class="sxs-lookup"><span data-stu-id="71516-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="71516-138">Utilisez un pool de canaux gRPC, par exemple, créer une liste de canaux gRPC.</span><span class="sxs-lookup"><span data-stu-id="71516-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="71516-139">`Random` est utilisé pour choisir un canal dans la liste chaque fois qu’un canal gRPC est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="71516-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="71516-140">L’utilisation de `Random` distribue aléatoirement des appels sur plusieurs connexions.</span><span class="sxs-lookup"><span data-stu-id="71516-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71516-141">L’amélioration du nombre maximal de flux simultanés sur le serveur est une autre façon de résoudre ce problème.</span><span class="sxs-lookup"><span data-stu-id="71516-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="71516-142">Dans Kestrel, ce est configuré avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="71516-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="71516-143">L’amélioration de la limite maximale du flux simultané n’est pas recommandée.</span><span class="sxs-lookup"><span data-stu-id="71516-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="71516-144">Un trop grand nombre de flux sur une seule connexion HTTP/2 introduit de nouveaux problèmes de performances :</span><span class="sxs-lookup"><span data-stu-id="71516-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="71516-145">Conflit de threads entre les flux tentant d’écrire dans la connexion.</span><span class="sxs-lookup"><span data-stu-id="71516-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="71516-146">La perte de paquets de connexion entraîne le blocage de tous les appels au niveau de la couche TCP.</span><span class="sxs-lookup"><span data-stu-id="71516-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="71516-147">Conserver les pings actifs</span><span class="sxs-lookup"><span data-stu-id="71516-147">Keep alive pings</span></span>

<span data-ttu-id="71516-148">Les commandes ping Keep Alive peuvent être utilisées pour maintenir les connexions HTTP/2 actives pendant les périodes d’inactivité.</span><span class="sxs-lookup"><span data-stu-id="71516-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="71516-149">Le fait de disposer d’une connexion HTTP/2 existante quand une application reprend l’activité permet aux appels gRPC initiaux de se faire rapidement, sans délai provoqué par la réinitialisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="71516-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="71516-150">Les pings Keep Alive sont configurés sur <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="71516-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="71516-151">Le code précédent configure un canal qui envoie une commande ping Keep Alive au serveur toutes les 60 secondes pendant les périodes d’inactivité.</span><span class="sxs-lookup"><span data-stu-id="71516-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="71516-152">La commande ping garantit que le serveur et les proxys en cours d’utilisation ne ferment pas la connexion en raison d’une inactivité.</span><span class="sxs-lookup"><span data-stu-id="71516-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="71516-153">Diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="71516-153">Streaming</span></span>

<span data-ttu-id="71516-154">la diffusion en continu bidirectionnelle gRPC peut être utilisée pour remplacer les appels gRPC unaires dans des scénarios à hautes performances.</span><span class="sxs-lookup"><span data-stu-id="71516-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="71516-155">Une fois qu’un flux bidirectionnel a démarré, la diffusion en continu des messages est plus rapide que l’envoi de messages avec plusieurs appels unaires gRPC.</span><span class="sxs-lookup"><span data-stu-id="71516-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="71516-156">Les messages diffusés en continu sont envoyés en tant que données sur une requête HTTP/2 existante et éliminent la surcharge liée à la création d’une nouvelle requête HTTP/2 pour chaque appel unaire.</span><span class="sxs-lookup"><span data-stu-id="71516-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="71516-157">Exemple de service :</span><span class="sxs-lookup"><span data-stu-id="71516-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="71516-158">Exemple de client :</span><span class="sxs-lookup"><span data-stu-id="71516-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="71516-159">Le remplacement des appels unaires par la diffusion bidirectionnelle pour des raisons de performances est une technique avancée qui n’est pas approprié dans de nombreuses situations.</span><span class="sxs-lookup"><span data-stu-id="71516-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="71516-160">L’utilisation des appels en diffusion en continu est un bon choix lorsque :</span><span class="sxs-lookup"><span data-stu-id="71516-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="71516-161">Un débit élevé ou une latence faible est requis.</span><span class="sxs-lookup"><span data-stu-id="71516-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="71516-162">gRPC et HTTP/2 sont identifiés comme un goulot d’étranglement au niveau des performances.</span><span class="sxs-lookup"><span data-stu-id="71516-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="71516-163">Un Worker du client envoie ou reçoit des messages standard avec un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="71516-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="71516-164">Tenez compte de la complexité et des limitations supplémentaires liées à l’utilisation des appels de diffusion en continu au lieu de unaire :</span><span class="sxs-lookup"><span data-stu-id="71516-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="71516-165">Un flux peut être interrompu par un service ou une erreur de connexion.</span><span class="sxs-lookup"><span data-stu-id="71516-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="71516-166">Une logique est nécessaire pour redémarrer le flux en cas d’erreur.</span><span class="sxs-lookup"><span data-stu-id="71516-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="71516-167">`RequestStream.WriteAsync` n’est pas sécurisé pour le Multi-Threading.</span><span class="sxs-lookup"><span data-stu-id="71516-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="71516-168">Un seul message à la fois peut être écrit dans un flux.</span><span class="sxs-lookup"><span data-stu-id="71516-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="71516-169">L’envoi de messages à partir de plusieurs threads sur un seul flux nécessite une file d’attente de producteur/consommateur telle que le <xref:System.Threading.Channels.Channel%601> marshalling des messages.</span><span class="sxs-lookup"><span data-stu-id="71516-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="71516-170">Une méthode de diffusion en continu gRPC est limitée à la réception d’un type de message et à l’envoi d’un type de message.</span><span class="sxs-lookup"><span data-stu-id="71516-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="71516-171">Par exemple, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` reçoit `RequestMessage` et envoie `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="71516-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="71516-172">La prise en charge par Protobuf des messages inconnus ou conditionnels utilisant `Any` et peut contourner `oneof` cette limitation.</span><span class="sxs-lookup"><span data-stu-id="71516-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
