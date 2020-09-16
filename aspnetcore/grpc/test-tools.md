---
title: Tester les services avec les outils gRPC
author: jamesnk
description: Découvrez comment tester les services avec les outils gRPC. gRPCurl un outil en ligne de commande pour interagir avec les services gRPC. gRPCui est une interface utilisateur Web interactive.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594417"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="a7008-105">Tester les services avec les outils gRPC</span><span class="sxs-lookup"><span data-stu-id="a7008-105">Test services with gRPC tools</span></span>

<span data-ttu-id="a7008-106">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a7008-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a7008-107">Les outils sont disponibles pour gRPC qui permet aux développeurs de tester les services sans générer d’applications clientes.</span><span class="sxs-lookup"><span data-stu-id="a7008-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="a7008-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) est un outil de ligne de commande qui fournit une interaction avec les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="a7008-109">[gRPCui](https://github.com/fullstorydev/grpcui) ajoute une interface utilisateur Web interactive pour gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="a7008-110">Cet article explique comment :</span><span class="sxs-lookup"><span data-stu-id="a7008-110">This article discusses how to:</span></span>

* <span data-ttu-id="a7008-111">Téléchargez et installez gRPCurl et gRPCui.</span><span class="sxs-lookup"><span data-stu-id="a7008-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="a7008-112">Configurer la réflexion gRPC avec une application gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a7008-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="a7008-113">Découvrez et testez les services gRPC avec `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="a7008-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="a7008-114">Interagissez avec les services gRPC via un navigateur à l’aide de `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="a7008-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="a7008-115">À propos de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="a7008-115">About gRPCurl</span></span>

<span data-ttu-id="a7008-116">gRPCurl est un outil de ligne de commande créé par la communauté gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="a7008-117">Ses fonctionnalités sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="a7008-117">Its features include:</span></span>

* <span data-ttu-id="a7008-118">Appel des services gRPC, y compris les services de streaming.</span><span class="sxs-lookup"><span data-stu-id="a7008-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="a7008-119">Découverte de service à l’aide de la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="a7008-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="a7008-120">Répertorier et décrire les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="a7008-121">Fonctionne avec des serveurs sécurisés (TLS) et non sécurisés (texte brut).</span><span class="sxs-lookup"><span data-stu-id="a7008-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="a7008-122">Pour plus d’informations sur le téléchargement et l’installation de `grpcurl` , consultez la [page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="a7008-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="a7008-123">Configurer la réflexion gRPC</span><span class="sxs-lookup"><span data-stu-id="a7008-123">Setup gRPC reflection</span></span>

<span data-ttu-id="a7008-124">`grpcurl` doit connaître le contrat Protobuf des services avant de pouvoir les appeler.</span><span class="sxs-lookup"><span data-stu-id="a7008-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="a7008-125">Il existe deux façons d'effectuer cette opération :</span><span class="sxs-lookup"><span data-stu-id="a7008-125">There are two ways to do this:</span></span>

* <span data-ttu-id="a7008-126">Utilisez la réflexion gRPC pour découvrir les contrats de service.</span><span class="sxs-lookup"><span data-stu-id="a7008-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="a7008-127">Spécifiez les fichiers *. proto* dans les arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="a7008-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="a7008-128">Il est plus facile d’utiliser gRPCurl avec la réflexion gRPC et la découverte de service.</span><span class="sxs-lookup"><span data-stu-id="a7008-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="a7008-129">gRPC ASP.NET Core offre une prise en charge intégrée de la réflexion gRPC avec le package [gRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="a7008-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="a7008-130">Pour configurer la réflexion dans une application :</span><span class="sxs-lookup"><span data-stu-id="a7008-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="a7008-131">Ajoutez une `Grpc.AspNetCore.Server.Reflection` référence de package.</span><span class="sxs-lookup"><span data-stu-id="a7008-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="a7008-132">Inscrire la réflexion dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a7008-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="a7008-133">`AddGrpcReflection` pour inscrire des services qui activent la réflexion.</span><span class="sxs-lookup"><span data-stu-id="a7008-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="a7008-134">`MapGrpcReflectionService` pour ajouter le point de terminaison du service de réflexion.</span><span class="sxs-lookup"><span data-stu-id="a7008-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="a7008-135">Utilisez `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="a7008-135">Use `grpcurl`</span></span>

<span data-ttu-id="a7008-136">L' `-help` argument décrit les `grpcurl` options de ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="a7008-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="a7008-137">Découvrir les services</span><span class="sxs-lookup"><span data-stu-id="a7008-137">Discover services</span></span>

<span data-ttu-id="a7008-138">Utilisez le `describe` verbe pour afficher les services définis par le serveur :</span><span class="sxs-lookup"><span data-stu-id="a7008-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="a7008-139">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="a7008-139">The preceding example:</span></span>

* <span data-ttu-id="a7008-140">Exécute un `describe` verbe sur le serveur `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a7008-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="a7008-141">Imprime les services et les méthodes retournés par la réflexion gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="a7008-142">`Greeter` est un service implémenté par l’application.</span><span class="sxs-lookup"><span data-stu-id="a7008-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="a7008-143">`ServerReflection` est le service ajouté par le `Grpc.AspNetCore.Server.Reflection` Package.</span><span class="sxs-lookup"><span data-stu-id="a7008-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="a7008-144">Combiner `describe` avec un service, une méthode ou un nom de message pour afficher ses détails :</span><span class="sxs-lookup"><span data-stu-id="a7008-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="a7008-145">Appeler gRPC services</span><span class="sxs-lookup"><span data-stu-id="a7008-145">Call gRPC services</span></span>

<span data-ttu-id="a7008-146">Appelez un service gRPC en spécifiant un nom de service et de méthode, ainsi qu’un argument JSON qui représente le message de demande.</span><span class="sxs-lookup"><span data-stu-id="a7008-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="a7008-147">Le JSON est converti en Protobuf et envoyé au service.</span><span class="sxs-lookup"><span data-stu-id="a7008-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="a7008-148">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="a7008-148">The preceding example:</span></span>

* <span data-ttu-id="a7008-149">`-d` l’argument spécifie un message de demande avec JSON.</span><span class="sxs-lookup"><span data-stu-id="a7008-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="a7008-150">Cet argument doit précéder l’adresse du serveur et le nom de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a7008-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="a7008-151">Appelle la `SayHello` méthode sur le `greeter.Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="a7008-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="a7008-152">Imprime le message de réponse au format JSON.</span><span class="sxs-lookup"><span data-stu-id="a7008-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="a7008-153">À propos de gRPCui</span><span class="sxs-lookup"><span data-stu-id="a7008-153">About gRPCui</span></span>

<span data-ttu-id="a7008-154">gRPCui est une interface utilisateur Web interactive pour gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="a7008-155">Il s’appuie sur gRPCurl et offre une interface graphique utilisateur pour la découverte et le test des services gRPC, à l’instar des outils HTTP tels que le poster.</span><span class="sxs-lookup"><span data-stu-id="a7008-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="a7008-156">Pour plus d’informations sur le téléchargement et l’installation de `grpcui` , consultez la [page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="a7008-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="a7008-157">Utilisation de `grpcui`</span><span class="sxs-lookup"><span data-stu-id="a7008-157">Using `grpcui`</span></span>

<span data-ttu-id="a7008-158">Exécutez `grpcui` avec l’adresse du serveur pour interagir comme un argument.</span><span class="sxs-lookup"><span data-stu-id="a7008-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="a7008-159">L’outil lance une fenêtre de navigateur avec l’interface utilisateur Web interactive.</span><span class="sxs-lookup"><span data-stu-id="a7008-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="a7008-160">les services gRPC sont automatiquement découverts à l’aide de la réflexion gRPC.</span><span class="sxs-lookup"><span data-stu-id="a7008-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interface utilisateur Web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="a7008-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a7008-162">Additional resources</span></span>

* [<span data-ttu-id="a7008-163">Page d’accueil gRPCurl GitHub</span><span class="sxs-lookup"><span data-stu-id="a7008-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="a7008-164">Page d’accueil gRPCui GitHub</span><span class="sxs-lookup"><span data-stu-id="a7008-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="a7008-165">GRPC. AspNetCore. Server. Reflection</span><span class="sxs-lookup"><span data-stu-id="a7008-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
