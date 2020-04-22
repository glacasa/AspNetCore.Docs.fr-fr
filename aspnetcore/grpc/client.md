---
title: Appeler les services gRPC avec le client .NET
author: jamesnk
description: Apprenez à appeler les services gRPC avec le client .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
uid: grpc/client
ms.openlocfilehash: aefa52a5c4c66178c5978aebd4cd9b00559c7f54
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791552"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="a6ef5-103">Appeler les services gRPC avec le client .NET</span><span class="sxs-lookup"><span data-stu-id="a6ef5-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="a6ef5-104">Une bibliothèque client .NET gRPC est disponible dans le forfait [NuGet Grpc.Net.Client.](https://www.nuget.org/packages/Grpc.Net.Client)</span><span class="sxs-lookup"><span data-stu-id="a6ef5-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="a6ef5-105">Ce document explique comment :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-105">This document explains how to:</span></span>

* <span data-ttu-id="a6ef5-106">Configurer un client gRPC pour appeler les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="a6ef5-107">Effectuez des appels gRPC vers des méthodes de streaming nonary, serveur, streaming client et bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="a6ef5-108">Configurer le client gRPC</span><span class="sxs-lookup"><span data-stu-id="a6ef5-108">Configure gRPC client</span></span>

<span data-ttu-id="a6ef5-109">les clients gRPC sont des types de clients concrets qui sont [générés à partir de \* \*\* fichiers .proto](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="a6ef5-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="a6ef5-110">Le client en béton gRPC a des méthodes \* \*\* qui se traduisent par le service gRPC dans le fichier .proto.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="a6ef5-111">Un client gRPC est créé à partir d’un canal.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="a6ef5-112">Commencez par `GrpcChannel.ForAddress` utiliser pour créer un canal, puis utilisez le canal pour créer un client gRPC:</span><span class="sxs-lookup"><span data-stu-id="a6ef5-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="a6ef5-113">Un canal représente une connexion à longue durée de vie à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="a6ef5-114">Lorsqu’un canal est créé, il est configuré avec des options liées à l’appel d’un service.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="a6ef5-115">Par exemple, `HttpClient` l’utilisé pour faire des appels, le maximum envoyer `GrpcChannelOptions` et recevoir `GrpcChannel.ForAddress`la taille du message, et la connexion peut être spécifiée et utilisée avec .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="a6ef5-116">Pour une liste complète des options, voir [options de configuration client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a6ef5-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="a6ef5-117">Performance et utilisation des canaux et des clients :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="a6ef5-118">La création d’un canal peut être une opération coûteuse.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="a6ef5-119">La réutilation d’un canal pour les appels gRPC offre des avantages de performance.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="a6ef5-120">les clients gRPC sont créés avec des canaux.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="a6ef5-121">les clients de gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="a6ef5-122">Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="a6ef5-123">Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="a6ef5-124">Les clients créés à partir du canal peuvent passer plusieurs appels simultanés.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="a6ef5-125">`GrpcChannel.ForAddress`n’est pas la seule option pour créer un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="a6ef5-126">Si vous appelez les services gRPC à partir d’une application ASP.NET Core, envisagez [l’intégration de l’usine client gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="a6ef5-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="a6ef5-127">l’intégration gRPC avec `HttpClientFactory` offre une alternative centralisée à la création de clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a6ef5-128">Une configuration supplémentaire est nécessaire pour [appeler des services gRPC non sécurisés avec le client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="a6ef5-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="a6ef5-129">Appeler gRPC sur HTTP/2 avec `Grpc.Net.Client` n’est actuellement pas pris en charge sur Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="a6ef5-130">Nous travaillons à améliorer le support HTTP/2 dans une future version de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="a6ef5-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) et [gRPC-Web](xref:grpc/browser) sont des alternatives viables qui fonctionnent aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="a6ef5-132">Passer des appels gRPC</span><span class="sxs-lookup"><span data-stu-id="a6ef5-132">Make gRPC calls</span></span>

<span data-ttu-id="a6ef5-133">Un appel gRPC est lancé en appelant une méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="a6ef5-134">Le client gRPC s’occupera de la sérialisation des messages et de l’adresse de l’appel gRPC au bon service.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="a6ef5-135">gRPC a différents types de méthodes.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-135">gRPC has different types of methods.</span></span> <span data-ttu-id="a6ef5-136">La façon dont le client est utilisé pour faire un appel gRPC dépend du type de méthode appelée.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="a6ef5-137">Les types de méthode gRPC sont les :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-137">The gRPC method types are:</span></span>

* <span data-ttu-id="a6ef5-138">Unaire</span><span class="sxs-lookup"><span data-stu-id="a6ef5-138">Unary</span></span>
* <span data-ttu-id="a6ef5-139">Streaming serveur</span><span class="sxs-lookup"><span data-stu-id="a6ef5-139">Server streaming</span></span>
* <span data-ttu-id="a6ef5-140">Streaming client</span><span class="sxs-lookup"><span data-stu-id="a6ef5-140">Client streaming</span></span>
* <span data-ttu-id="a6ef5-141">Streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="a6ef5-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="a6ef5-142">Appel unary</span><span class="sxs-lookup"><span data-stu-id="a6ef5-142">Unary call</span></span>

<span data-ttu-id="a6ef5-143">Un appel non anaire commence par l’envoi d’un message de demande par le client.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="a6ef5-144">Un message de réponse est retourné lorsque le service se termine.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="a6ef5-145">Chaque méthode de service \* \*\* unary dans le fichier .proto se traduira par deux méthodes .NET sur le type de client gRPC en béton pour appeler la méthode: une méthode asynchrone et une méthode de blocage.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="a6ef5-146">Par exemple, `GreeterClient` il y a `SayHello`deux façons d’appeler :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="a6ef5-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` appelle le service asynchronement.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="a6ef5-148">On peut attendre.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-148">Can be awaited.</span></span>
* <span data-ttu-id="a6ef5-149">`GreeterClient.SayHello`- `Greeter.SayHello` le service d’appels et les blocs jusqu’à ce qu’ils soient complets.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="a6ef5-150">N’utilisez pas dans le code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="a6ef5-151">Appel de streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="a6ef5-151">Server streaming call</span></span>

<span data-ttu-id="a6ef5-152">Un appel de streaming serveur commence avec le client envoyant un message de demande.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="a6ef5-153">`ResponseStream.MoveNext()`lit les messages diffusés à partir du service.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="a6ef5-154">L’appel de streaming `ResponseStream.MoveNext()` `false`serveur est terminé lors de la déclaration .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="a6ef5-155">Lors de l’utilisation de `await foreach` C 8 ou plus tard, la syntaxe peut être utilisée pour lire les messages.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="a6ef5-156">La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de réponse :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="a6ef5-157">Appel en streaming client</span><span class="sxs-lookup"><span data-stu-id="a6ef5-157">Client streaming call</span></span>

<span data-ttu-id="a6ef5-158">Un appel en streaming client commence *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a6ef5-159">Le client peut choisir `RequestStream.WriteAsync`d’envoyer des messages avec .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a6ef5-160">Lorsque le client a `RequestStream.CompleteAsync` fini d’envoyer des messages, doit être appelé pour aviser le service.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="a6ef5-161">L’appel est terminé lorsque le service renvoie un message de réponse.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="a6ef5-162">Appel en streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="a6ef5-162">Bi-directional streaming call</span></span>

<span data-ttu-id="a6ef5-163">Un appel en streaming bidirectionnel commence *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a6ef5-164">Le client peut choisir `RequestStream.WriteAsync`d’envoyer des messages avec .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a6ef5-165">Les messages diffusés à `ResponseStream.MoveNext()` partir `ResponseStream.ReadAllAsync()`du service sont accessibles avec ou .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="a6ef5-166">L’appel en streaming bidirectionnel est terminé lorsque le n’a `ResponseStream` plus de messages.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="a6ef5-167">Lors d’un appel en continu bidirectionnel, le client et le service peuvent s’envoyer des messages les uns aux autres à tout moment.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="a6ef5-168">La meilleure logique client pour interagir avec un appel bidirectionnel varie selon la logique de service.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="a6ef5-169">Accédez aux remorques gRPC</span><span class="sxs-lookup"><span data-stu-id="a6ef5-169">Access gRPC trailers</span></span>

<span data-ttu-id="a6ef5-170">les appels gRPC peuvent retourner les remorques gRPC.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="a6ef5-171">les remorques gRPC sont utilisées pour fournir des métadonnées nom/valeur au sujet d’un appel.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="a6ef5-172">Les bandes-annonces offrent des fonctionnalités similaires aux en-têtes HTTP, mais sont reçues à la fin de l’appel.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="a6ef5-173">les remorques gRPC `GetTrailers()`sont accessibles à l’aide de , qui renvoie une collection de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="a6ef5-174">Les remorques sont retournées une fois la réponse terminée, vous devez donc attendre tous les messages de réponse avant d’accéder aux remorques.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="a6ef5-175">Les appels en streaming `ResponseAsync` non `GetTrailers()`et clients doivent attendre avant d’appeler :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="a6ef5-176">Les appels en streaming serveur et bidirectionnel doivent se terminer en attendant le flux de réponse avant d’appeler `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="a6ef5-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="a6ef5-177">les remorques gRPC `RpcException`sont également accessibles à partir de .</span><span class="sxs-lookup"><span data-stu-id="a6ef5-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="a6ef5-178">Un service peut retourner des remorques avec un statut de gRPC non-OK.</span><span class="sxs-lookup"><span data-stu-id="a6ef5-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="a6ef5-179">Dans cette situation, les remorques sont récupérées à partir de l’exception lancée par le client gRPC :</span><span class="sxs-lookup"><span data-stu-id="a6ef5-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="a6ef5-180">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a6ef5-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
