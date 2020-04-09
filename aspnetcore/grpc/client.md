---
title: Appelez les services gRPC avec le client .NET
author: jamesnk
description: Apprenez à appeler les services gRPC avec le client .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667173"
---
# <a name="call-grpc-services-with-the-net-client"></a>Appelez les services gRPC avec le client .NET

Une bibliothèque client .NET gRPC est disponible dans le forfait [NuGet Grpc.Net.Client.](https://www.nuget.org/packages/Grpc.Net.Client) Ce document explique comment :

* Configurer un client gRPC pour appeler les services gRPC.
* Effectuez des appels gRPC vers des méthodes de streaming nonary, serveur, streaming client et bidirectionnelle.

## <a name="configure-grpc-client"></a>Configurer le client gRPC

les clients gRPC sont des types de clients concrets qui sont [générés à partir de * \** fichiers .proto](xref:grpc/basics#generated-c-assets). Le client en béton gRPC a des méthodes * \** qui se traduisent par le service gRPC dans le fichier .proto.

Un client gRPC est créé à partir d’un canal. Commencez par `GrpcChannel.ForAddress` utiliser pour créer un canal, puis utilisez le canal pour créer un client gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Un canal représente une connexion à longue durée de vie à un service gRPC. Lorsqu’un canal est créé, il est configuré avec des options liées à l’appel d’un service. Par exemple, `HttpClient` l’utilisé pour faire des appels, le maximum envoyer `GrpcChannelOptions` et recevoir `GrpcChannel.ForAddress`la taille du message, et la connexion peut être spécifiée et utilisée avec . Pour une liste complète des options, voir [options de configuration client](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Performance et utilisation des canaux et des clients :

* La création d’un canal peut être une opération coûteuse. La réutilation d’un canal pour les appels gRPC offre des avantages de performance.
* les clients gRPC sont créés avec des canaux. les clients de gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.
* Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.
* Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.
* Les clients créés à partir du canal peuvent passer plusieurs appels simultanés.

`GrpcChannel.ForAddress`n’est pas la seule option pour créer un client gRPC. Si vous appelez les services gRPC à partir d’une application ASP.NET Core, envisagez [l’intégration de l’usine client gRPC](xref:grpc/clientfactory). l’intégration gRPC avec `HttpClientFactory` offre une alternative centralisée à la création de clients gRPC.

> [!NOTE]
> Une configuration supplémentaire est nécessaire pour [appeler des services gRPC non sécurisés avec le client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> Appeler gRPC sur HTTP/2 avec `Grpc.Net.Client` n’est actuellement pas pris en charge sur Xamarin. Nous travaillons à améliorer le support HTTP/2 dans une future version de Xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) et [gRPC-Web](xref:grpc/browser) sont des alternatives viables qui fonctionnent aujourd’hui.

## <a name="make-grpc-calls"></a>Passer des appels gRPC

Un appel gRPC est lancé en appelant une méthode sur le client. Le client gRPC s’occupera de la sérialisation des messages et de l’adresse de l’appel gRPC au bon service.

gRPC a différents types de méthodes. La façon dont vous utilisez le client pour passer un appel gRPC dépend du type de méthode que vous appelez. Les types de méthode gRPC sont les :

* Unaire
* Streaming serveur
* Streaming client
* Streaming bidirectionnel

### <a name="unary-call"></a>Appel unary

Un appel non anaire commence par l’envoi d’un message de demande par le client. Un message de réponse est retourné lorsque le service se termine.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Chaque méthode de service * \** unary dans le fichier .proto se traduira par deux méthodes .NET sur le type de client gRPC en béton pour appeler la méthode: une méthode asynchrone et une méthode de blocage. Par exemple, `GreeterClient` il y a `SayHello`deux façons d’appeler :

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` appelle le service asynchronement. On peut attendre.
* `GreeterClient.SayHello`- `Greeter.SayHello` le service d’appels et les blocs jusqu’à ce qu’ils soient complets. N’utilisez pas dans le code asynchrone.

### <a name="server-streaming-call"></a>Appel de streaming de serveur

Un appel de streaming serveur commence avec le client envoyant un message de demande. `ResponseStream.MoveNext()`lit les messages diffusés à partir du service. L’appel de streaming `ResponseStream.MoveNext()` `false`serveur est terminé lors de la déclaration .

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

Si vous utilisez C 8 ou `await foreach` plus tard, la syntaxe peut être utilisée pour lire les messages. La `IAsyncStreamReader<T>.ReadAllAsync()` méthode d’extension lit tous les messages du flux de réponse :

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>Appel en streaming client

Un appel en streaming client commence *sans* que le client envoie un message. Le client peut choisir `RequestStream.WriteAsync`d’envoyer des messages avec . Lorsque le client a `RequestStream.CompleteAsync` fini d’envoyer des messages doit être appelé pour aviser le service. L’appel est terminé lorsque le service renvoie un message de réponse.

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>Appel en streaming bidirectionnel

Un appel en streaming bidirectionnel commence *sans* que le client envoie un message. Le client peut choisir `RequestStream.WriteAsync`d’envoyer des messages avec . Les messages diffusés à `ResponseStream.MoveNext()` partir `ResponseStream.ReadAllAsync()`du service sont accessibles avec ou . L’appel en streaming bidirectionnel est terminé lorsque le n’a `ResponseStream` plus de messages.

```csharp
using (var call = client.Echo())
{
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
}
```

Lors d’un appel en continu bidirectionnel, le client et le service peuvent s’envoyer des messages les uns aux autres à tout moment. La meilleure logique client pour interagir avec un appel bidirectionnel varie selon la logique de service.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
