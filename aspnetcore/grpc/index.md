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
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="6d374-103">Introduction à gRPC sur .NET Core</span><span class="sxs-lookup"><span data-stu-id="6d374-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="6d374-104">Par [John Luo](https://github.com/juntaoluo) et [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6d374-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6d374-105">[gRPC](https://grpc.io/docs/guides/) est un framework d’appel de procédure distante (RPC) indépendant du langage et très performant.</span><span class="sxs-lookup"><span data-stu-id="6d374-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="6d374-106">Les principaux avantages de gRPC sont :</span><span class="sxs-lookup"><span data-stu-id="6d374-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="6d374-107">Cadre RPC moderne, performant et léger.</span><span class="sxs-lookup"><span data-stu-id="6d374-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="6d374-108">Développement d’API « Contract-first », à l’aide de mémoires tampons de protocole par défaut, permettant des implémentations indépendantes du langage.</span><span class="sxs-lookup"><span data-stu-id="6d374-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="6d374-109">Outils disponibles pour de nombreux langages afin de générer des clients et serveurs fortement typés.</span><span class="sxs-lookup"><span data-stu-id="6d374-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="6d374-110">Prise en charge d’appels de streaming client, serveur et bidirectionnels.</span><span class="sxs-lookup"><span data-stu-id="6d374-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="6d374-111">Utilisation réduite du réseau avec sérialisation binaire Protobuf.</span><span class="sxs-lookup"><span data-stu-id="6d374-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="6d374-112">Ces avantages font de gRPC la solution idéale pour :</span><span class="sxs-lookup"><span data-stu-id="6d374-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="6d374-113">Les microservices légers où l’efficacité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="6d374-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="6d374-114">Les systèmes polyglottes où plusieurs langages sont nécessaires au développement.</span><span class="sxs-lookup"><span data-stu-id="6d374-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="6d374-115">Les services en temps réel de point à point qui doivent gérer des demandes ou réponses de streaming.</span><span class="sxs-lookup"><span data-stu-id="6d374-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="6d374-116">Support d’outillage CMD pour les fichiers .proto</span><span class="sxs-lookup"><span data-stu-id="6d374-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="6d374-117">gRPC utilise une approche de premier contrat pour le développement de l’API.</span><span class="sxs-lookup"><span data-stu-id="6d374-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="6d374-118">Les services et \* \*\* les messages sont définis dans les fichiers .proto :</span><span class="sxs-lookup"><span data-stu-id="6d374-118">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="6d374-119">.Les types net pour les services, \* \*\* les clients et les messages sont automatiquement générés par l’inclusion de fichiers .proto dans un projet :</span><span class="sxs-lookup"><span data-stu-id="6d374-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="6d374-120">Ajoutez une référence au package [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="6d374-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="6d374-121">Ajoutez \* \*\* des fichiers `<Protobuf>` .proto au groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="6d374-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="6d374-122">Pour plus d’informations sur le <xref:grpc/basics>support d’outillage gRPC, voir .</span><span class="sxs-lookup"><span data-stu-id="6d374-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="6d374-123">gRPC services sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d374-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="6d374-124">les services gRPC peuvent être hébergés sur ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d374-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="6d374-125">Les services sont pleinement associés aux caractéristiques populaires ASP.NET Core telles que l’exploitation forestière, l’injection de dépendance (DI), l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="6d374-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="6d374-126">Le modèle de projet de service gRPC fournit un service de démarrage :</span><span class="sxs-lookup"><span data-stu-id="6d374-126">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="6d374-127">`GreeterService`hérite du `GreeterBase` type, qui est `Greeter` généré à partir du service dans le \* \*fichier .proto.\*</span><span class="sxs-lookup"><span data-stu-id="6d374-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="6d374-128">Le service est rendu accessible aux clients en *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d374-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="6d374-129">Pour en savoir plus sur les services gRPC sur ASP.NET Core, voir <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="6d374-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="6d374-130">Appelez les services gRPC avec un client .NET</span><span class="sxs-lookup"><span data-stu-id="6d374-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="6d374-131">les clients gRPC sont des types de clients concrets qui sont [générés à partir de \* \*\* fichiers .proto](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="6d374-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="6d374-132">Le client en béton gRPC a des méthodes \* \*\* qui se traduisent par le service gRPC dans le fichier .proto.</span><span class="sxs-lookup"><span data-stu-id="6d374-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="6d374-133">Un client gRPC est créé à l’aide d’un canal, ce qui représente une connexion à longue durée de vie à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="6d374-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="6d374-134">Un canal peut `GrpcChannel.ForAddress`être créé à l’aide de .</span><span class="sxs-lookup"><span data-stu-id="6d374-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="6d374-135">Pour plus d’informations sur la création <xref:grpc/client>de clients, et d’appeler différentes méthodes de service, voir .</span><span class="sxs-lookup"><span data-stu-id="6d374-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="6d374-136">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6d374-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
