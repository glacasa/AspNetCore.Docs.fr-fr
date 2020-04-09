---
title: Migration des services gRPC de C Core vers ASP.NET Core
author: juntaoluo
description: Découvrez comment déplacer une application GRPC basée sur le C-core existante pour fonctionner au-dessus de ASP.NET pile Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664135"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="136b1-103">Migration des services gRPC de C Core vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="136b1-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="136b1-104">Par [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="136b1-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="136b1-105">En raison de la mise en œuvre de la pile sous-jacente, toutes les fonctionnalités ne fonctionnent pas de la même manière entre les applications [GRPC basées sur le C-core](https://grpc.io/blog/grpc-stacks) et ASP.NET applications basées sur le noyau.</span><span class="sxs-lookup"><span data-stu-id="136b1-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="136b1-106">Ce document met en évidence les principales différences pour la migration entre les deux piles.</span><span class="sxs-lookup"><span data-stu-id="136b1-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="136b1-107">gRPC mise en œuvre du service à vie</span><span class="sxs-lookup"><span data-stu-id="136b1-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="136b1-108">Dans la pile ASP.NET Core, les services gRPC, par défaut, sont créés avec une [durée de vie étendue](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="136b1-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="136b1-109">En revanche, gRPC C-core par défaut se lie à un service avec une [durée de vie singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="136b1-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="136b1-110">Une durée de vie étendue permet à la mise en œuvre du service de résoudre d’autres services à durée de vie.</span><span class="sxs-lookup"><span data-stu-id="136b1-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="136b1-111">Par exemple, une durée de `DbContext` vie étendue peut également se résoudre à partir du contenant DI par injection de constructeurs.</span><span class="sxs-lookup"><span data-stu-id="136b1-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="136b1-112">Utilisation de la durée de vie portée :</span><span class="sxs-lookup"><span data-stu-id="136b1-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="136b1-113">Une nouvelle instance de la mise en œuvre du service est construite pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="136b1-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="136b1-114">Il n’est pas possible de partager l’état entre les demandes via les membres d’instance sur le type de implémentation.</span><span class="sxs-lookup"><span data-stu-id="136b1-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="136b1-115">On s’attend à stocker les états partagés dans un service singleton dans le conteneur DI.</span><span class="sxs-lookup"><span data-stu-id="136b1-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="136b1-116">Les états partagés stockés sont résolus dans le constructeur de la mise en œuvre du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="136b1-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="136b1-117">Pour plus d’informations sur <xref:fundamentals/dependency-injection#service-lifetimes>les durées de service, voir .</span><span class="sxs-lookup"><span data-stu-id="136b1-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="136b1-118">Ajouter un service singleton</span><span class="sxs-lookup"><span data-stu-id="136b1-118">Add a singleton service</span></span>

<span data-ttu-id="136b1-119">Pour faciliter la transition d’une mise en œuvre de base C gRPC à ASP.NET Core, il est possible de changer la durée de vie du service de mise en œuvre du service de portée à singleton.</span><span class="sxs-lookup"><span data-stu-id="136b1-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="136b1-120">Il s’agit d’ajouter un exemple de la mise en œuvre du service au conteneur DI :</span><span class="sxs-lookup"><span data-stu-id="136b1-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="136b1-121">Cependant, une mise en œuvre de service avec une durée de vie à un seulton n’est plus en mesure de résoudre les services d’envergure par injection de constructeurs.</span><span class="sxs-lookup"><span data-stu-id="136b1-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="136b1-122">Configurer les options de services gRPC</span><span class="sxs-lookup"><span data-stu-id="136b1-122">Configure gRPC services options</span></span>

<span data-ttu-id="136b1-123">Dans les applications C-core, `grpc.max_receive_message_length` des `grpc.max_send_message_length` paramètres tels `ChannelOption` que et sont configurés avec lors [de la construction de l’instance Server](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="136b1-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="136b1-124">Dans ASP.NET Core, gRPC fournit `GrpcServiceOptions` la configuration à travers le type.</span><span class="sxs-lookup"><span data-stu-id="136b1-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="136b1-125">Par exemple, la taille maximale du message entrant d’un `AddGrpc`service gRPC peut être configurée via .</span><span class="sxs-lookup"><span data-stu-id="136b1-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="136b1-126">L’exemple suivant `MaxReceiveMessageSize` modifie le défaut de 4 Mo à 16 Mo :</span><span class="sxs-lookup"><span data-stu-id="136b1-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="136b1-127">Pour plus d’informations <xref:grpc/configuration>sur la configuration, voir .</span><span class="sxs-lookup"><span data-stu-id="136b1-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="136b1-128">Journalisation</span><span class="sxs-lookup"><span data-stu-id="136b1-128">Logging</span></span>

<span data-ttu-id="136b1-129">Les applications C-core s’appuient sur la `GrpcEnvironment` configuration de [l’enregistreur](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="136b1-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="136b1-130">La pile ASP.NET Core fournit cette fonctionnalité grâce à [l’API d’enregistrement](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="136b1-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="136b1-131">Par exemple, un enregistreur peut être ajouté au service gRPC par injection de constructeurs :</span><span class="sxs-lookup"><span data-stu-id="136b1-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="136b1-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="136b1-132">HTTPS</span></span>

<span data-ttu-id="136b1-133">Les applications C-core configurent HTTPS via la [propriété Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="136b1-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="136b1-134">Un concept similaire est utilisé pour configurer les serveurs dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="136b1-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="136b1-135">Par exemple, Kestrel utilise [la configuration de point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration) pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="136b1-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="136b1-136">intercepteurs gRPC vs Middleware</span><span class="sxs-lookup"><span data-stu-id="136b1-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="136b1-137">ASP.NET [Core middleware](xref:fundamentals/middleware/index) offre des fonctionnalités similaires par rapport aux intercepteurs dans les applications GRPC basées sur C-core.</span><span class="sxs-lookup"><span data-stu-id="136b1-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="136b1-138">ASP.NET Core middleware et intercepteurs sont conceptuellement similaires.</span><span class="sxs-lookup"><span data-stu-id="136b1-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="136b1-139">Les deux :</span><span class="sxs-lookup"><span data-stu-id="136b1-139">Both:</span></span>

* <span data-ttu-id="136b1-140">Sont utilisés pour construire un pipeline qui gère une demande gRPC.</span><span class="sxs-lookup"><span data-stu-id="136b1-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="136b1-141">Autoriser l’exécution des travaux avant ou après le composant suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="136b1-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="136b1-142">Fournir l’accès à `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="136b1-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="136b1-143">Au milieu, `HttpContext` le est un paramètre.</span><span class="sxs-lookup"><span data-stu-id="136b1-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="136b1-144">Dans les `HttpContext` intercepteurs, il `ServerCallContext` est accessible `ServerCallContext.GetHttpContext` à l’aide du paramètre avec la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="136b1-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="136b1-145">Notez que cette fonctionnalité est spécifique aux intercepteurs fonctionnant dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="136b1-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="136b1-146">gRPC Interceptor différences de ASP.NET Core Middleware:</span><span class="sxs-lookup"><span data-stu-id="136b1-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="136b1-147">Intercepteurs:</span><span class="sxs-lookup"><span data-stu-id="136b1-147">Interceptors:</span></span>
  * <span data-ttu-id="136b1-148">Opérer sur la couche d’abstraction gRPC à l’aide du [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="136b1-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="136b1-149">Fournir l’accès à :</span><span class="sxs-lookup"><span data-stu-id="136b1-149">Provide access to:</span></span>
    * <span data-ttu-id="136b1-150">Le message déséialisé envoyé à un appel.</span><span class="sxs-lookup"><span data-stu-id="136b1-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="136b1-151">Le message est renvoyé de l’appel avant qu’il ne soit sérialisé.</span><span class="sxs-lookup"><span data-stu-id="136b1-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="136b1-152">Peut attraper et gérer les exceptions jetées des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="136b1-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="136b1-153">Middleware :</span><span class="sxs-lookup"><span data-stu-id="136b1-153">Middleware:</span></span>
  * <span data-ttu-id="136b1-154">Exécute devant les intercepteurs gRPC.</span><span class="sxs-lookup"><span data-stu-id="136b1-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="136b1-155">Fonctionne sur les messages HTTP/2 sous-jacents.</span><span class="sxs-lookup"><span data-stu-id="136b1-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="136b1-156">Ne peut accéder aux octets qu’à partir des flux de demande et de réponse.</span><span class="sxs-lookup"><span data-stu-id="136b1-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="136b1-157">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="136b1-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
