---
title: Services gRPC avec ASP.NET Core
author: juntaoluo
description: Apprenez les concepts de base lors de l’écriture de services gRPC avec ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667628"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="2f4dc-103">Services gRPC avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f4dc-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="2f4dc-104">Ce document montre comment commencer avec les services gRPC en utilisant ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2f4dc-105">Prérequis</span><span class="sxs-lookup"><span data-stu-id="2f4dc-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f4dc-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f4dc-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f4dc-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f4dc-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f4dc-108">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2f4dc-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="2f4dc-109">Bien démarrer avec le service gRPC dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f4dc-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="2f4dc-110">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2f4dc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f4dc-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f4dc-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f4dc-112">Voir [Démarrer avec les services gRPC](xref:tutorials/grpc/grpc-start) pour des instructions détaillées sur la façon de créer un projet gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2f4dc-113">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2f4dc-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2f4dc-114">Exécutez `dotnet new grpc -o GrpcGreeter` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="2f4dc-115">Ajouter des services gRPC à une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f4dc-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="2f4dc-116">gRPC nécessite le paquet [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="2f4dc-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="2f4dc-117">Configurer gRPC</span><span class="sxs-lookup"><span data-stu-id="2f4dc-117">Configure gRPC</span></span>

<span data-ttu-id="2f4dc-118">En *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f4dc-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="2f4dc-119">gRPC est activé `AddGrpc` avec la méthode.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="2f4dc-120">Chaque service gRPC est ajouté au `MapGrpcService` pipeline de routage par la méthode.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="2f4dc-121">ASP.NET les middlewares et les fonctionnalités de base partagent le pipeline de routage, donc une application peut être configurée pour servir des gestionnaires de demandes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="2f4dc-122">Les gestionnaires de demandes supplémentaires, tels que les contrôleurs MVC, travaillent en parallèle avec les services gRPC configurés.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="2f4dc-123">Configurer Kestrel</span><span class="sxs-lookup"><span data-stu-id="2f4dc-123">Configure Kestrel</span></span>

<span data-ttu-id="2f4dc-124">Points d’évaluation Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="2f4dc-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="2f4dc-125">Exiger HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-125">Require HTTP/2.</span></span>
* <span data-ttu-id="2f4dc-126">Doit être sécurisé avec [la sécurité des couches de transport (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="2f4dc-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="2f4dc-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2f4dc-127">HTTP/2</span></span>

<span data-ttu-id="2f4dc-128">gRPC nécessite HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="2f4dc-129">gRPC pour ASP.NET Core valide [httpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) est `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="2f4dc-130">Kestrel [prend en charge HTTP/2](xref:fundamentals/servers/kestrel#http2-support) sur la plupart des systèmes d’exploitation modernes.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="2f4dc-131">Les paramètres Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="2f4dc-132">TLS</span><span class="sxs-lookup"><span data-stu-id="2f4dc-132">TLS</span></span>

<span data-ttu-id="2f4dc-133">Les paramètres de Kestrel utilisés pour le gRPC doivent être fixés avec TLS.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="2f4dc-134">En cours de développement, un critère d’évaluation sécurisé avec TLS est automatiquement créé au `https://localhost:5001` moment où le certificat de développement ASP.NET Core est présent.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="2f4dc-135">Aucune configuration n'est requise.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-135">No configuration is required.</span></span> <span data-ttu-id="2f4dc-136">Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="2f4dc-137">En production, TLS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="2f4dc-138">Dans l’exemple suivant *appsettings.json,* un point de terminaison HTTP/2 sécurisé avec TLS est fourni :</span><span class="sxs-lookup"><span data-stu-id="2f4dc-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="2f4dc-139">Alternativement, les paramètres Kestrel peuvent être configurés en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f4dc-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="2f4dc-140">Négociation de protocole</span><span class="sxs-lookup"><span data-stu-id="2f4dc-140">Protocol negotiation</span></span>

<span data-ttu-id="2f4dc-141">TLS est utilisé pour plus que la sécurisation de la communication.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="2f4dc-142">La poignée de main TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="2f4dc-143">Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="2f4dc-144">Si un point de terminaison HTTP/2 est configuré sans TLS, les `HttpProtocols.Http2` [protocoles ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doivent être configurés .</span><span class="sxs-lookup"><span data-stu-id="2f4dc-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="2f4dc-145">Un point de terminaison avec `HttpProtocols.Http1AndHttp2`plusieurs protocoles (par exemple, ) ne peut pas être utilisé sans TLS parce qu’il n’y a pas de négociation.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="2f4dc-146">Toutes les connexions à la valeur de point de terminaison non sécurisée par défaut à HTTP/1.1, et les appels gRPC échouent.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="2f4dc-147">Pour plus d’informations sur l’activation http/2 et TLS avec Kestrel, voir [configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="2f4dc-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="2f4dc-148">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="2f4dc-149">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="2f4dc-150">Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="2f4dc-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="2f4dc-151">Intégration avec les API de base ASP.NET</span><span class="sxs-lookup"><span data-stu-id="2f4dc-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="2f4dc-152">les services gRPC ont un accès complet aux caractéristiques ASP.NET Core telles que [l’injection de dépendance](xref:fundamentals/dependency-injection) (DI) et [l’enregistrement .](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="2f4dc-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2f4dc-153">Par exemple, la mise en œuvre du service peut résoudre un service d’enregistreur à partir du conteneur DI via le constructeur :</span><span class="sxs-lookup"><span data-stu-id="2f4dc-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="2f4dc-154">Par défaut, la mise en œuvre du service gRPC peut résoudre d’autres services d' DI à vie (Singleton, Scoped ou Transitoire).</span><span class="sxs-lookup"><span data-stu-id="2f4dc-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="2f4dc-155">Résoudre httpContexte dans les méthodes gRPC</span><span class="sxs-lookup"><span data-stu-id="2f4dc-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="2f4dc-156">L’API gRPC donne accès à certaines données de messages HTTP/2, telles que la méthode, l’hôte, l’en-tête et les remorques.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="2f4dc-157">L’accès `ServerCallContext` se fait par l’argumentation transmise à chaque méthode gRPC :</span><span class="sxs-lookup"><span data-stu-id="2f4dc-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="2f4dc-158">`ServerCallContext`n’offre pas `HttpContext` un accès complet à toutes les ASP.NET API.</span><span class="sxs-lookup"><span data-stu-id="2f4dc-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="2f4dc-159">La `GetHttpContext` méthode d’extension `HttpContext` donne un accès complet au message http://http/2 sous-jacent dans ASP.NET API :</span><span class="sxs-lookup"><span data-stu-id="2f4dc-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="2f4dc-160">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2f4dc-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
