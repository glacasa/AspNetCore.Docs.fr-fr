---
title: gRPC intégration d’usine client dans .NET Core
author: jamesnk
description: Apprenez à créer des clients gRPC en utilisant l’usine cliente.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667166"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="595c8-103">gRPC intégration d’usine client dans .NET Core</span><span class="sxs-lookup"><span data-stu-id="595c8-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="595c8-104">l’intégration gRPC avec `HttpClientFactory` offre un moyen centralisé de créer des clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="595c8-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="595c8-105">Il peut être utilisé comme une alternative à [la configuration autonome gRPC instances client](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="595c8-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="595c8-106">L’intégration d’usine est disponible dans le forfait [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="595c8-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="595c8-107">L’usine offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="595c8-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="595c8-108">Fournit un emplacement central pour configurer les instances logiques des clients GRPC</span><span class="sxs-lookup"><span data-stu-id="595c8-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="595c8-109">Gère la durée de vie du sous-jacent`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="595c8-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="595c8-110">Propagation automatique de la date limite et de l’annulation dans un service ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="595c8-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="595c8-111">Enregistrer les clients gRPC</span><span class="sxs-lookup"><span data-stu-id="595c8-111">Register gRPC clients</span></span>

<span data-ttu-id="595c8-112">Pour enregistrer un client gRPC, la méthode d’extension générique `AddGrpcClient` peut être utilisée à l’intérieur, `Startup.ConfigureServices`spécifiant la classe de client et l’adresse de service typées gRPC :</span><span class="sxs-lookup"><span data-stu-id="595c8-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="595c8-113">Le type de client gRPC est enregistré comme transitoire avec injection de dépendance (DI).</span><span class="sxs-lookup"><span data-stu-id="595c8-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="595c8-114">Le client peut maintenant être injecté et consommé directement dans les types créés par DI.</span><span class="sxs-lookup"><span data-stu-id="595c8-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="595c8-115">ASP.NET les contrôleurs core MVC, SignalR les hubs et les services gRPC sont des endroits où les clients gRPC peuvent automatiquement être injectés :</span><span class="sxs-lookup"><span data-stu-id="595c8-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="595c8-116">Configurer HttpClient</span><span class="sxs-lookup"><span data-stu-id="595c8-116">Configure HttpClient</span></span>

<span data-ttu-id="595c8-117">`HttpClientFactory`crée `HttpClient` l’utilisation par le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="595c8-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="595c8-118">Les `HttpClientFactory` méthodes standard peuvent être utilisées pour ajouter `HttpClientHandler` la `HttpClient`demande sortante middleware ou pour configurer le sous-jacent de la :</span><span class="sxs-lookup"><span data-stu-id="595c8-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="595c8-119">Pour plus d’informations, voir [Faites des demandes HTTP à l’aide d’IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="595c8-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="595c8-120">Configurer le canal et les intercepteurs</span><span class="sxs-lookup"><span data-stu-id="595c8-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="595c8-121">des méthodes spécifiques à gRPC sont disponibles pour :</span><span class="sxs-lookup"><span data-stu-id="595c8-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="595c8-122">Configurer le canal sous-jacent d’un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="595c8-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="595c8-123">Ajoutez `Interceptor` des instances que le client utilisera lors des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="595c8-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="595c8-124">Date limite et propagation d’annulation</span><span class="sxs-lookup"><span data-stu-id="595c8-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="595c8-125">les clients gRPC créés par l’usine dans `EnableCallContextPropagation()` un service gRPC peuvent être configurés avec pour propager automatiquement la date limite et le jeton d’annulation aux appels d’enfants.</span><span class="sxs-lookup"><span data-stu-id="595c8-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="595c8-126">La `EnableCallContextPropagation()` méthode d’extension est disponible dans le paquet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="595c8-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="595c8-127">La propagation du contexte d’appel fonctionne en lisant la date limite et le jeton d’annulation du contexte actuel de demande de gRPC et en les propageant automatiquement aux appels sortants effectués par le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="595c8-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="595c8-128">La propagation du contexte d’appel est un excellent moyen de s’assurer que les scénarios gRPC complexes et imbriqués propagent toujours la date limite et l’annulation.</span><span class="sxs-lookup"><span data-stu-id="595c8-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="595c8-129">Pour plus d’informations sur les délais et l’annulation RPC, voir [cycle de vie RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="595c8-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="595c8-130">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="595c8-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
