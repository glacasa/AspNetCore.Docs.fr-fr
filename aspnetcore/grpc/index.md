---
title: Introduction à gRPC sur .NET Core
author: juntaoluo
description: Découvrez les services gRPC avec le serveur Kestrel et la pile ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667313"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introduction à gRPC sur .NET Core

Par [John Luo](https://github.com/juntaoluo) et [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) est un framework d’appel de procédure distante (RPC) indépendant du langage et très performant.

Les principaux avantages de gRPC sont :
* Cadre RPC moderne, performant et léger.
* Développement d’API « Contract-first », à l’aide de mémoires tampons de protocole par défaut, permettant des implémentations indépendantes du langage.
* Outils disponibles pour de nombreux langages afin de générer des clients et serveurs fortement typés.
* Prise en charge d’appels de streaming client, serveur et bidirectionnels.
* Utilisation réduite du réseau avec sérialisation binaire Protobuf.

Ces avantages font de gRPC la solution idéale pour :
* Les microservices légers où l’efficacité est essentielle.
* Les systèmes polyglottes où plusieurs langages sont nécessaires au développement.
* Les services en temps réel de point à point qui doivent gérer des demandes ou réponses de streaming.

## <a name="c-tooling-support-for-proto-files"></a>Support d’outillage CMD pour les fichiers .proto

gRPC utilise une approche de premier contrat pour le développement de l’API. Les services et * \** les messages sont définis dans les fichiers .proto :

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

.Les types net pour les services, * \** les clients et les messages sont automatiquement générés par l’inclusion de fichiers .proto dans un projet :

* Ajoutez une référence au package [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
* Ajoutez * \** des fichiers `<Protobuf>` .proto au groupe d’éléments.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Pour plus d’informations sur le <xref:grpc/basics>support d’outillage gRPC, voir .

## <a name="grpc-services-on-aspnet-core"></a>gRPC services sur ASP.NET Core

les services gRPC peuvent être hébergés sur ASP.NET Core. Les services sont pleinement associés aux caractéristiques populaires ASP.NET Core telles que l’exploitation forestière, l’injection de dépendance (DI), l’authentification et l’autorisation.

Le modèle de projet de service gRPC fournit un service de démarrage :

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`hérite du `GreeterBase` type, qui est `Greeter` généré à partir du service dans le * \*fichier .proto.* Le service est rendu accessible aux clients en *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Pour en savoir plus sur les services gRPC sur ASP.NET Core, voir <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Appelez les services gRPC avec un client .NET

les clients gRPC sont des types de clients concrets qui sont [générés à partir de * \** fichiers .proto](xref:grpc/basics#generated-c-assets). Le client en béton gRPC a des méthodes * \** qui se traduisent par le service gRPC dans le fichier .proto.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Un client gRPC est créé à l’aide d’un canal, ce qui représente une connexion à longue durée de vie à un service gRPC. Un canal peut `GrpcChannel.ForAddress`être créé à l’aide de .

Pour plus d’informations sur la création <xref:grpc/client>de clients, et d’appeler différentes méthodes de service, voir .

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
