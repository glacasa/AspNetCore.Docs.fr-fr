---
title: Communication entre processus avec gRPC
author: jamesnk
description: Découvrez comment utiliser gRPC pour la communication entre processus.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945703"
---
# <a name="inter-process-communication-with-grpc"></a>Communication entre processus avec gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

les appels gRPC entre un client et un service sont généralement envoyés via des sockets TCP. TCP est idéal pour la communication sur un réseau, mais la [communication entre processus (IPC)](https://wikipedia.org/wiki/Inter-process_communication) est plus efficace lorsque le client et le service se trouvent sur le même ordinateur. Ce document explique comment utiliser gRPC avec des transports personnalisés dans des scénarios IPC.

## <a name="server-configuration"></a>Configurer le serveur

Les transports personnalisés sont pris en charge par Kestrel. Kestrel est configuré dans *Program.cs*:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

L’exemple précédent :

* Configure les points de terminaison de Kestrel dans `ConfigureKestrel` .
* Appelle <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour écouter un [Socket de domaine UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) avec le chemin d’accès spécifié.

Kestrel offre une prise en charge intégrée des points de terminaison UDS. Les UDS sont pris en charge sur Linux, macOS et les [versions modernes de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuration de client

`GrpcChannel` prend en charge l’exécution d’appels gRPC sur des transports personnalisés. Lorsqu’un canal est créé, il peut être configuré avec un `SocketsHttpHandler` qui a un personnalisé `ConnectionFactory` . La fabrique permet au client d’établir des connexions sur des transports personnalisés, puis d’envoyer des requêtes HTTP sur ce transport.

> [!IMPORTANT]
> `ConnectionFactory` est une nouvelle API dans .NET 5 Release Candidate 1.

Exemple de fabrique de connexion de sockets de domaine UNIX :

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

Utilisation de la fabrique de connexion personnalisée pour créer un canal :

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Les canaux créés à l’aide du code précédent envoient des appels gRPC sur des sockets de domaine UNIX.
