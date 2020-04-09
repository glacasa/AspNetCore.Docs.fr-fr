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
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC intégration d’usine client dans .NET Core

l’intégration gRPC avec `HttpClientFactory` offre un moyen centralisé de créer des clients gRPC. Il peut être utilisé comme une alternative à [la configuration autonome gRPC instances client](xref:grpc/client). L’intégration d’usine est disponible dans le forfait [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.

L’usine offre les avantages suivants :

* Fournit un emplacement central pour configurer les instances logiques des clients GRPC
* Gère la durée de vie du sous-jacent`HttpClientMessageHandler`
* Propagation automatique de la date limite et de l’annulation dans un service ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Enregistrer les clients gRPC

Pour enregistrer un client gRPC, la méthode d’extension générique `AddGrpcClient` peut être utilisée à l’intérieur, `Startup.ConfigureServices`spécifiant la classe de client et l’adresse de service typées gRPC :

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Le type de client gRPC est enregistré comme transitoire avec injection de dépendance (DI). Le client peut maintenant être injecté et consommé directement dans les types créés par DI. ASP.NET les contrôleurs core MVC, SignalR les hubs et les services gRPC sont des endroits où les clients gRPC peuvent automatiquement être injectés :

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

## <a name="configure-httpclient"></a>Configurer HttpClient

`HttpClientFactory`crée `HttpClient` l’utilisation par le client gRPC. Les `HttpClientFactory` méthodes standard peuvent être utilisées pour ajouter `HttpClientHandler` la `HttpClient`demande sortante middleware ou pour configurer le sous-jacent de la :

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

Pour plus d’informations, voir [Faites des demandes HTTP à l’aide d’IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurer le canal et les intercepteurs

des méthodes spécifiques à gRPC sont disponibles pour :

* Configurer le canal sous-jacent d’un client gRPC.
* Ajoutez `Interceptor` des instances que le client utilisera lors des appels gRPC.

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

## <a name="deadline-and-cancellation-propagation"></a>Date limite et propagation d’annulation

les clients gRPC créés par l’usine dans `EnableCallContextPropagation()` un service gRPC peuvent être configurés avec pour propager automatiquement la date limite et le jeton d’annulation aux appels d’enfants. La `EnableCallContextPropagation()` méthode d’extension est disponible dans le paquet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.

La propagation du contexte d’appel fonctionne en lisant la date limite et le jeton d’annulation du contexte actuel de demande de gRPC et en les propageant automatiquement aux appels sortants effectués par le client gRPC. La propagation du contexte d’appel est un excellent moyen de s’assurer que les scénarios gRPC complexes et imbriqués propagent toujours la date limite et l’annulation.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Pour plus d’informations sur les délais et l’annulation RPC, voir [cycle de vie RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
