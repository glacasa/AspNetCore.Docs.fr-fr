---
<span data-ttu-id="1c0a7-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c0a7-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c0a7-102">'Blazor'</span></span>
- <span data-ttu-id="1c0a7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c0a7-103">'Identity'</span></span>
- <span data-ttu-id="1c0a7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c0a7-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c0a7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c0a7-105">'Razor'</span></span>
- <span data-ttu-id="1c0a7-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-106">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="1c0a7-107">Présentation de gRPC sur .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c0a7-107">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="1c0a7-108">Par [John Luo](https://github.com/juntaoluo) et [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1c0a7-108">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1c0a7-109">[gRPC](https://grpc.io/docs/guides/) est un framework d’appel de procédure distante (RPC) indépendant du langage et très performant.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-109">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="1c0a7-110">Les principaux avantages de gRPC sont :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-110">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="1c0a7-111">Infrastructure RPC moderne, hautement performante et légère.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-111">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="1c0a7-112">Développement d’API « Contract-first », à l’aide de mémoires tampons de protocole par défaut, permettant des implémentations indépendantes du langage.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-112">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="1c0a7-113">Outils disponibles pour de nombreux langages afin de générer des clients et serveurs fortement typés.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-113">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="1c0a7-114">Prise en charge d’appels de streaming client, serveur et bidirectionnels.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-114">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="1c0a7-115">Utilisation réduite du réseau avec sérialisation binaire Protobuf.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-115">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="1c0a7-116">Ces avantages font de gRPC la solution idéale pour :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-116">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="1c0a7-117">Les microservices légers où l’efficacité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-117">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="1c0a7-118">Les systèmes polyglottes où plusieurs langages sont nécessaires au développement.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-118">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="1c0a7-119">Les services en temps réel de point à point qui doivent gérer des demandes ou réponses de streaming.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-119">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="1c0a7-120">Prise en charge des outils C# pour les fichiers. proto</span><span class="sxs-lookup"><span data-stu-id="1c0a7-120">C# Tooling support for .proto files</span></span>

<span data-ttu-id="1c0a7-121">gRPC utilise une approche contrat d’abord pour le développement d’API.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-121">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="1c0a7-122">Les services et les messages sont définis dans les fichiers \* \* . proto\* :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-122">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="1c0a7-123">Les types .NET pour les services, les clients et les messages sont générés automatiquement par l’inclusion de fichiers \* \* . proto\* dans un projet :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-123">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="1c0a7-124">Ajoutez une référence de package au package [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-124">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="1c0a7-125">Ajoutez des fichiers \* \* . proto\* au `<Protobuf>` groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-125">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="1c0a7-126">Pour plus d’informations sur la prise en charge des outils gRPC, consultez <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-126">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="1c0a7-127">services gRPC sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c0a7-127">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="1c0a7-128">les services gRPC peuvent être hébergés sur ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-128">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="1c0a7-129">Les services ont une intégration complète aux fonctionnalités populaires de ASP.NET Core telles que la journalisation, l’injection de dépendances, l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-129">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="1c0a7-130">Le modèle de projet de service gRPC fournit un service de démarrage :</span><span class="sxs-lookup"><span data-stu-id="1c0a7-130">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="1c0a7-131">`GreeterService`hérite du `GreeterBase` type, qui est généré à partir du `Greeter` service dans le fichier \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-131">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="1c0a7-132">Le service est rendu accessible aux clients dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1c0a7-132">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="1c0a7-133">Pour en savoir plus sur les services gRPC sur ASP.NET Core, consultez <xref:grpc/aspnetcore> .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-133">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="1c0a7-134">Appeler gRPC services avec un client .NET</span><span class="sxs-lookup"><span data-stu-id="1c0a7-134">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="1c0a7-135">les clients gRPC sont des types de client concrets qui sont [générés à partir de fichiers \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="1c0a7-135">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="1c0a7-136">Le client gRPC concret possède des méthodes qui traduisent le service gRPC dans le fichier \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-136">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="1c0a7-137">Un client gRPC est créé à l’aide d’un canal, qui représente une connexion à long terme à un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c0a7-137">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="1c0a7-138">Un canal peut être créé à l’aide de `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-138">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="1c0a7-139">Pour plus d’informations sur la création de clients et l’appel de différentes méthodes de service, consultez <xref:grpc/client> .</span><span class="sxs-lookup"><span data-stu-id="1c0a7-139">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c0a7-140">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c0a7-140">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
