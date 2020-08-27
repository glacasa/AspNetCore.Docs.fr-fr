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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="0ba1a-103">Communication entre processus avec gRPC</span><span class="sxs-lookup"><span data-stu-id="0ba1a-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="0ba1a-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0ba1a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0ba1a-105">les appels gRPC entre un client et un service sont généralement envoyés via des sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="0ba1a-106">TCP est idéal pour la communication sur un réseau, mais la [communication entre processus (IPC)](https://wikipedia.org/wiki/Inter-process_communication) est plus efficace lorsque le client et le service se trouvent sur le même ordinateur.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="0ba1a-107">Ce document explique comment utiliser gRPC avec des transports personnalisés dans des scénarios IPC.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="0ba1a-108">Configurer le serveur</span><span class="sxs-lookup"><span data-stu-id="0ba1a-108">Server configuration</span></span>

<span data-ttu-id="0ba1a-109">Les transports personnalisés sont pris en charge par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="0ba1a-110">Kestrel est configuré dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ba1a-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="0ba1a-111">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="0ba1a-111">The preceding example:</span></span>

* <span data-ttu-id="0ba1a-112">Configure les points de terminaison de Kestrel dans `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="0ba1a-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="0ba1a-113">Appelle <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour écouter un [Socket de domaine UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) avec le chemin d’accès spécifié.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="0ba1a-114">Kestrel offre une prise en charge intégrée des points de terminaison UDS.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="0ba1a-115">Les UDS sont pris en charge sur Linux, macOS et les [versions modernes de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="0ba1a-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="0ba1a-116">Configuration de client</span><span class="sxs-lookup"><span data-stu-id="0ba1a-116">Client configuration</span></span>

<span data-ttu-id="0ba1a-117">`GrpcChannel` prend en charge l’exécution d’appels gRPC sur des transports personnalisés.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="0ba1a-118">Lorsqu’un canal est créé, il peut être configuré avec un `SocketsHttpHandler` qui a un personnalisé `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="0ba1a-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="0ba1a-119">La fabrique permet au client d’établir des connexions sur des transports personnalisés, puis d’envoyer des requêtes HTTP sur ce transport.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0ba1a-120">`ConnectionFactory` est une nouvelle API dans .NET 5 Release Candidate 1.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="0ba1a-121">Exemple de fabrique de connexion de sockets de domaine UNIX :</span><span class="sxs-lookup"><span data-stu-id="0ba1a-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="0ba1a-122">Utilisation de la fabrique de connexion personnalisée pour créer un canal :</span><span class="sxs-lookup"><span data-stu-id="0ba1a-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="0ba1a-123">Les canaux créés à l’aide du code précédent envoient des appels gRPC sur des sockets de domaine UNIX.</span><span class="sxs-lookup"><span data-stu-id="0ba1a-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
