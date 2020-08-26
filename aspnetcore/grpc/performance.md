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
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>Meilleures pratiques en matière de performances dans gRPC pour ASP.NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

gRPC est conçu pour les services à hautes performances. Ce document explique comment obtenir les meilleures performances possibles à partir de gRPC.

## <a name="reuse-channel"></a>Réutiliser le canal

Un canal gRPC doit être réutilisé pour effectuer des appels gRPC. La réutilisation d’un canal permet de multiplexer les appels par le biais d’une connexion HTTP/2 existante.

Si un canal est créé pour chaque appel gRPC, le temps nécessaire à l’exécution peut augmenter de manière significative. Chaque appel nécessite plusieurs allers-retours sur le réseau entre le client et le serveur pour créer une connexion HTTP/2 :

1. Ouverture d’un socket
2. Établissement d’une connexion TCP
3. Négociation de TLS
4. Démarrage de la connexion HTTP/2
5. Exécution de l’appel gRPC

Les canaux peuvent être partagés et réutilisés en toute sécurité entre les appels gRPC :

* les clients gRPC sont créés avec des canaux. les clients gRPC sont des objets légers et n’ont pas besoin d’être mis en cache ou réutilisés.
* Plusieurs clients gRPC peuvent être créés à partir d’un canal, y compris différents types de clients.
* Un canal et des clients créés à partir du canal peuvent être utilisés en toute sécurité par plusieurs threads.
* Les clients créés à partir du canal peuvent effectuer plusieurs appels simultanés.

## <a name="connection-concurrency"></a>Accès concurrentiel de la connexion

Les connexions HTTP/2 ont généralement une limite du nombre [maximal de flux simultanés (requêtes http actives)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) sur une connexion à la fois. Par défaut, la plupart des serveurs définissent cette limite sur 100 flux simultanés.

Un canal gRPC utilise une seule connexion HTTP/2, et les appels simultanés sont multiplexés sur cette connexion. Lorsque le nombre d’appels actifs atteint la limite du flux de connexion, les appels supplémentaires sont mis en file d’attente dans le client. Les appels en file d’attente attendent que les appels actifs se terminent avant d’être envoyés. Les applications avec une charge élevée, ou des appels de diffusion en continu à long terme, peuvent voir des problèmes de performances dus à la mise en file d’attente des appels en raison de cette limite.

::: moniker range=">= aspnetcore-5.0"

.NET 5 introduit la `SocketsHttpHandler.EnableMultipleHttp2Connections` propriété. Quand la valeur `true` est, les connexions http/2 supplémentaires sont créées par un canal lorsque la limite de flux simultanée est atteinte. Lorsqu’un `GrpcChannel` est créé, son interne `SocketsHttpHandler` est automatiquement configuré pour créer des connexions http/2 supplémentaires. Si une application configure son propre gestionnaire, envisagez `EnableMultipleHttp2Connections` de définir sur `true` :

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

Il existe deux solutions de contournement pour les applications .NET Core 3,1 :

* Créez des canaux gRPC distincts pour les zones de l’application avec une charge élevée. Par exemple, le `Logger` service gRPC peut avoir une charge élevée. Utilisez un canal distinct pour créer le `LoggerClient` dans l’application.
* Utilisez un pool de canaux gRPC, par exemple, créer une liste de canaux gRPC. `Random` est utilisé pour choisir un canal dans la liste chaque fois qu’un canal gRPC est nécessaire. L’utilisation de `Random` distribue aléatoirement des appels sur plusieurs connexions.

> [!IMPORTANT]
> L’amélioration du nombre maximal de flux simultanés sur le serveur est une autre façon de résoudre ce problème. Dans Kestrel, ce est configuré avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> L’amélioration de la limite maximale du flux simultané n’est pas recommandée. Un trop grand nombre de flux sur une seule connexion HTTP/2 introduit de nouveaux problèmes de performances :
>
> * Conflit de threads entre les flux tentant d’écrire dans la connexion.
> * La perte de paquets de connexion entraîne le blocage de tous les appels au niveau de la couche TCP.

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>Conserver les pings actifs

Les commandes ping Keep Alive peuvent être utilisées pour maintenir les connexions HTTP/2 actives pendant les périodes d’inactivité. Le fait de disposer d’une connexion HTTP/2 existante quand une application reprend l’activité permet aux appels gRPC initiaux de se faire rapidement, sans délai provoqué par la réinitialisation de la connexion.

Les pings Keep Alive sont configurés sur <xref:System.Net.Http.SocketsHttpHandler> :

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

Le code précédent configure un canal qui envoie une commande ping Keep Alive au serveur toutes les 60 secondes pendant les périodes d’inactivité. La commande ping garantit que le serveur et les proxys en cours d’utilisation ne ferment pas la connexion en raison d’une inactivité.

::: moniker-end

## <a name="streaming"></a>Diffusion en continu

la diffusion en continu bidirectionnelle gRPC peut être utilisée pour remplacer les appels gRPC unaires dans des scénarios à hautes performances. Une fois qu’un flux bidirectionnel a démarré, la diffusion en continu des messages est plus rapide que l’envoi de messages avec plusieurs appels unaires gRPC. Les messages diffusés en continu sont envoyés en tant que données sur une requête HTTP/2 existante et éliminent la surcharge liée à la création d’une nouvelle requête HTTP/2 pour chaque appel unaire.

Exemple de service :

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

Exemple de client :

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

Le remplacement des appels unaires par la diffusion bidirectionnelle pour des raisons de performances est une technique avancée qui n’est pas approprié dans de nombreuses situations.

L’utilisation des appels en diffusion en continu est un bon choix lorsque :

1. Un débit élevé ou une latence faible est requis.
2. gRPC et HTTP/2 sont identifiés comme un goulot d’étranglement au niveau des performances.
3. Un Worker du client envoie ou reçoit des messages standard avec un service gRPC.

Tenez compte de la complexité et des limitations supplémentaires liées à l’utilisation des appels de diffusion en continu au lieu de unaire :

1. Un flux peut être interrompu par un service ou une erreur de connexion. Une logique est nécessaire pour redémarrer le flux en cas d’erreur.
2. `RequestStream.WriteAsync` n’est pas sécurisé pour le Multi-Threading. Un seul message à la fois peut être écrit dans un flux. L’envoi de messages à partir de plusieurs threads sur un seul flux nécessite une file d’attente de producteur/consommateur telle que le <xref:System.Threading.Channels.Channel%601> marshalling des messages.
3. Une méthode de diffusion en continu gRPC est limitée à la réception d’un type de message et à l’envoi d’un type de message. Par exemple, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` reçoit `RequestMessage` et envoie `ResponseMessage` . La prise en charge par Protobuf des messages inconnus ou conditionnels utilisant `Any` et peut contourner `oneof` cette limitation.
