---
title: Appeler les services gRPC avec le client .NET
author: jamesnk
description: Découvrez comment appeler des services gRPC avec le client .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: c554ce9702a9f2b2efeabbfdf0d1319ca4161a1c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774728"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="b8428-103">Appeler les services gRPC avec le client .NET</span><span class="sxs-lookup"><span data-stu-id="b8428-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="b8428-104">Une bibliothèque cliente .NET gRPC est disponible dans le package NuGet [gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="b8428-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="b8428-105">Ce document explique comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8428-105">This document explains how to:</span></span>

* <span data-ttu-id="b8428-106">Configurez un client gRPC pour appeler les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8428-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="b8428-107">Effectuez des appels gRPC à des méthodes unaire, de streaming de serveur, de diffusion de client et de diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="b8428-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="b8428-108">Configurer le client gRPC</span><span class="sxs-lookup"><span data-stu-id="b8428-108">Configure gRPC client</span></span>

<span data-ttu-id="b8428-109">les clients gRPC sont des types de client concrets qui sont [générés à partir de \* \*fichiers. proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="b8428-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="b8428-110">Le client gRPC concret possède des méthodes qui traduisent le service gRPC dans le \* \*fichier. proto\* .</span><span class="sxs-lookup"><span data-stu-id="b8428-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="b8428-111">Un client gRPC est créé à partir d’un canal.</span><span class="sxs-lookup"><span data-stu-id="b8428-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="b8428-112">Commencez par utiliser `GrpcChannel.ForAddress` pour créer un canal, puis utilisez le canal pour créer un client gRPC :</span><span class="sxs-lookup"><span data-stu-id="b8428-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="b8428-113">Un canal représente une connexion à long terme à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8428-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="b8428-114">Lorsqu’un canal est créé, il est configuré avec les options relatives à l’appel d’un service.</span><span class="sxs-lookup"><span data-stu-id="b8428-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="b8428-115">Par exemple, le `HttpClient` utilisé pour effectuer des appels, la taille maximale des messages d’envoi et de réception, et la `GrpcChannelOptions` journalisation peuvent `GrpcChannel.ForAddress`être spécifiés sur et utilisés avec.</span><span class="sxs-lookup"><span data-stu-id="b8428-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="b8428-116">Pour obtenir la liste complète des options, consultez [options de configuration du client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="b8428-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="b8428-117">Performances et utilisation des canaux et des clients :</span><span class="sxs-lookup"><span data-stu-id="b8428-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="b8428-118">La création d’un canal peut être une opération coûteuse.</span><span class="sxs-lookup"><span data-stu-id="b8428-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="b8428-119">La réutilisation d’un canal pour les appels gRPC offre des avantages en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="b8428-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="b8428-120">les clients gRPC sont créés avec des canaux.</span><span class="sxs-lookup"><span data-stu-id="b8428-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="b8428-121">les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.</span><span class="sxs-lookup"><span data-stu-id="b8428-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="b8428-122">Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.</span><span class="sxs-lookup"><span data-stu-id="b8428-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="b8428-123">Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="b8428-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="b8428-124">Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.</span><span class="sxs-lookup"><span data-stu-id="b8428-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="b8428-125">`GrpcChannel.ForAddress`n’est pas la seule option pour créer un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8428-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="b8428-126">Si vous appelez gRPC services à partir d’une application ASP.NET Core, envisagez l’intégration de la [fabrique de clients gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="b8428-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="b8428-127">l’intégration de `HttpClientFactory` gRPC avec offre une alternative centralisée à la création de clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8428-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="b8428-128">Une configuration supplémentaire est requise pour [appeler des services gRPC non sécurisés avec le client .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="b8428-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="b8428-129">L’appel de gRPC sur HTTP/ `Grpc.Net.Client` 2 avec n’est pas pris en charge actuellement sur Xamarin.</span><span class="sxs-lookup"><span data-stu-id="b8428-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="b8428-130">Nous nous efforçons d’améliorer la prise en charge de HTTP/2 dans une prochaine version de Xamarin.</span><span class="sxs-lookup"><span data-stu-id="b8428-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="b8428-131">[GRPC. Core](https://www.nuget.org/packages/Grpc.Core) et [GRPC-Web](xref:grpc/browser) sont des alternatives viables qui fonctionnent aujourd’hui.</span><span class="sxs-lookup"><span data-stu-id="b8428-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="b8428-132">Effectuer des appels gRPC</span><span class="sxs-lookup"><span data-stu-id="b8428-132">Make gRPC calls</span></span>

<span data-ttu-id="b8428-133">Un appel gRPC est initié par l’appel d’une méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="b8428-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="b8428-134">Le client gRPC gère la sérialisation des messages et traite l’appel gRPC au service approprié.</span><span class="sxs-lookup"><span data-stu-id="b8428-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="b8428-135">gRPC a différents types de méthodes.</span><span class="sxs-lookup"><span data-stu-id="b8428-135">gRPC has different types of methods.</span></span> <span data-ttu-id="b8428-136">La façon dont le client est utilisé pour effectuer un appel gRPC dépend du type de méthode appelé.</span><span class="sxs-lookup"><span data-stu-id="b8428-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="b8428-137">Les types de méthode gRPC sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b8428-137">The gRPC method types are:</span></span>

* <span data-ttu-id="b8428-138">Unaire</span><span class="sxs-lookup"><span data-stu-id="b8428-138">Unary</span></span>
* <span data-ttu-id="b8428-139">Streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="b8428-139">Server streaming</span></span>
* <span data-ttu-id="b8428-140">Diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="b8428-140">Client streaming</span></span>
* <span data-ttu-id="b8428-141">Streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="b8428-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="b8428-142">Appel unaire</span><span class="sxs-lookup"><span data-stu-id="b8428-142">Unary call</span></span>

<span data-ttu-id="b8428-143">Un appel unaire commence par le client envoyant un message de demande.</span><span class="sxs-lookup"><span data-stu-id="b8428-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="b8428-144">Un message de réponse est retourné lorsque le service se termine.</span><span class="sxs-lookup"><span data-stu-id="b8428-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="b8428-145">Chaque méthode de service unaire du fichier \* \*. proto\* se traduira par deux méthodes .net sur le type de client gRPC concret pour l’appel de la méthode : une méthode asynchrone et une méthode de blocage.</span><span class="sxs-lookup"><span data-stu-id="b8428-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="b8428-146">Par exemple, `GreeterClient` il existe deux façons d’appeler `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="b8428-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="b8428-147">`GreeterClient.SayHelloAsync`-appelle `Greeter.SayHello` le service de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b8428-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="b8428-148">Peut être attendu.</span><span class="sxs-lookup"><span data-stu-id="b8428-148">Can be awaited.</span></span>
* <span data-ttu-id="b8428-149">`GreeterClient.SayHello`-appelle `Greeter.SayHello` le service et bloque jusqu’à la fin.</span><span class="sxs-lookup"><span data-stu-id="b8428-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="b8428-150">N’utilisez pas dans le code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b8428-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="b8428-151">Appel de streaming de serveur</span><span class="sxs-lookup"><span data-stu-id="b8428-151">Server streaming call</span></span>

<span data-ttu-id="b8428-152">Un appel de streaming de serveur commence par le client qui envoie un message de demande.</span><span class="sxs-lookup"><span data-stu-id="b8428-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="b8428-153">`ResponseStream.MoveNext()`lit les messages diffusés en continu à partir du service.</span><span class="sxs-lookup"><span data-stu-id="b8428-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="b8428-154">L’appel de streaming de serveur est `ResponseStream.MoveNext()` terminé `false`lorsque retourne.</span><span class="sxs-lookup"><span data-stu-id="b8428-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="b8428-155">Si vous utilisez C# 8 ou une version `await foreach` ultérieure, la syntaxe peut être utilisée pour lire les messages.</span><span class="sxs-lookup"><span data-stu-id="b8428-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="b8428-156">La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de réponse :</span><span class="sxs-lookup"><span data-stu-id="b8428-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="b8428-157">Appel de diffusion en continu du client</span><span class="sxs-lookup"><span data-stu-id="b8428-157">Client streaming call</span></span>

<span data-ttu-id="b8428-158">Un appel de diffusion en continu du client démarre *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="b8428-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="b8428-159">Le client peut choisir d’envoyer des messages `RequestStream.WriteAsync`avec.</span><span class="sxs-lookup"><span data-stu-id="b8428-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="b8428-160">Lorsque le client a terminé l’envoi des `RequestStream.CompleteAsync` messages, doit être appelé pour notifier le service.</span><span class="sxs-lookup"><span data-stu-id="b8428-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="b8428-161">L’appel est terminé lorsque le service renvoie un message de réponse.</span><span class="sxs-lookup"><span data-stu-id="b8428-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="b8428-162">Appel de streaming bidirectionnel</span><span class="sxs-lookup"><span data-stu-id="b8428-162">Bi-directional streaming call</span></span>

<span data-ttu-id="b8428-163">Un appel de streaming bidirectionnel démarre *sans* que le client envoie un message.</span><span class="sxs-lookup"><span data-stu-id="b8428-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="b8428-164">Le client peut choisir d’envoyer des messages `RequestStream.WriteAsync`avec.</span><span class="sxs-lookup"><span data-stu-id="b8428-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="b8428-165">Les messages transmis en continu à partir du service `ResponseStream.MoveNext()` sont `ResponseStream.ReadAllAsync()`accessibles avec ou.</span><span class="sxs-lookup"><span data-stu-id="b8428-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="b8428-166">L’appel de streaming bidirectionnel est terminé lorsque le n' `ResponseStream` a plus de messages.</span><span class="sxs-lookup"><span data-stu-id="b8428-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="b8428-167">Pendant un appel de streaming bidirectionnel, le client et le service peuvent envoyer des messages entre eux à tout moment.</span><span class="sxs-lookup"><span data-stu-id="b8428-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="b8428-168">La meilleure logique cliente pour interagir avec un appel bidirectionnel varie en fonction de la logique du service.</span><span class="sxs-lookup"><span data-stu-id="b8428-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="b8428-169">Accéder aux codes de fin gRPC</span><span class="sxs-lookup"><span data-stu-id="b8428-169">Access gRPC trailers</span></span>

<span data-ttu-id="b8428-170">les appels gRPC peuvent retourner des codes de fin gRPC.</span><span class="sxs-lookup"><span data-stu-id="b8428-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="b8428-171">les codes de fin gRPC sont utilisés pour fournir les métadonnées nom/valeur relatives à un appel.</span><span class="sxs-lookup"><span data-stu-id="b8428-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="b8428-172">Les codes de fin offrent des fonctionnalités similaires aux en-têtes HTTP, mais sont reçus à la fin de l’appel.</span><span class="sxs-lookup"><span data-stu-id="b8428-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="b8428-173">les codes de fin gRPC `GetTrailers()`sont accessibles à l’aide de, qui retourne une collection de métadonnées.</span><span class="sxs-lookup"><span data-stu-id="b8428-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="b8428-174">Les codes de fin sont retournés une fois la réponse terminée. par conséquent, vous devez attendre tous les messages de réponse avant d’accéder aux codes de fin.</span><span class="sxs-lookup"><span data-stu-id="b8428-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="b8428-175">Les appels unaires et de streaming `ResponseAsync` client doivent `GetTrailers()`attendre avant d’appeler :</span><span class="sxs-lookup"><span data-stu-id="b8428-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="b8428-176">Les appels de diffusion en continu bidirectionnelle et serveur doivent finir par attendre le `GetTrailers()`flux de réponse avant d’appeler :</span><span class="sxs-lookup"><span data-stu-id="b8428-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="b8428-177">les codes de fin gRPC sont `RpcException`également accessibles à partir de.</span><span class="sxs-lookup"><span data-stu-id="b8428-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="b8428-178">Un service peut retourner des codes de fin avec un État gRPC non OK.</span><span class="sxs-lookup"><span data-stu-id="b8428-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="b8428-179">Dans ce cas, les codes de fin sont récupérés à partir de l’exception levée par le client gRPC :</span><span class="sxs-lookup"><span data-stu-id="b8428-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b8428-180">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8428-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
