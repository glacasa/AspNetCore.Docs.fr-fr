---
title: Services gRPC avec C#
author: juntaoluo
description: Apprenez les concepts de base lors de l’écriture de services gRPC avec C.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664205"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="9fbb2-103">gRPC services avec C\#</span><span class="sxs-lookup"><span data-stu-id="9fbb2-103">gRPC services with C\#</span></span>

<span data-ttu-id="9fbb2-104">Ce document décrit les concepts nécessaires à l’écriture d’applications [gRPC](https://grpc.io/docs/guides/) dans C.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="9fbb2-105">Les sujets abordés ici s’appliquent à la fois [c’est](https://grpc.io/blog/grpc-stacks)basé sur C et ASP.NET applications gRPC basées sur core.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="9fbb2-106">fichier proto</span><span class="sxs-lookup"><span data-stu-id="9fbb2-106">proto file</span></span>

<span data-ttu-id="9fbb2-107">gRPC utilise une approche de premier contrat pour le développement de l’API.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="9fbb2-108">Les tampons de protocole (protobuf) sont utilisés comme langage de conception d’interface (IDL) par défaut.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="9fbb2-109">Le fichier \* \*.proto\* contient :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="9fbb2-110">La définition du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="9fbb2-111">Les messages envoyés entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="9fbb2-112">Pour plus d’informations sur la syntaxe des fichiers protobuf, voir la [documentation officielle (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="9fbb2-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="9fbb2-113">Par exemple, considérez le fichier *greet.proto* utilisé dans [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="9fbb2-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="9fbb2-114">Définit un `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="9fbb2-115">Le `Greeter` service définit `SayHello` un appel.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="9fbb2-116">`SayHello`envoie `HelloRequest` un message `HelloReply` et reçoit un message :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="9fbb2-117">Ajouter un fichier .proto\# à une application C</span><span class="sxs-lookup"><span data-stu-id="9fbb2-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="9fbb2-118">Le \* \*\* fichier .proto est inclus dans un `<Protobuf>` projet en l’ajoutant au groupe d’objets :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="9fbb2-119">Support d’outillage CMD pour les fichiers .proto</span><span class="sxs-lookup"><span data-stu-id="9fbb2-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="9fbb2-120">Le paquet d’outillage [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) est nécessaire pour générer les actifs C 'à partir de \* \*fichiers .proto.\*</span><span class="sxs-lookup"><span data-stu-id="9fbb2-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="9fbb2-121">Les actifs générés (fichiers) :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-121">The generated assets (files):</span></span>

* <span data-ttu-id="9fbb2-122">Sont générés sur une base nécessaire chaque fois que le projet est construit.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="9fbb2-123">Ne sont pas ajoutés au projet ou contrôlés dans le contrôle source.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="9fbb2-124">Sont un artefact de construction contenu dans le répertoire *obj.*</span><span class="sxs-lookup"><span data-stu-id="9fbb2-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="9fbb2-125">Ce paquet est exigé par le serveur et les projets clients.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="9fbb2-126">Le `Grpc.AspNetCore` métapackage comprend `Grpc.Tools`une référence à .</span><span class="sxs-lookup"><span data-stu-id="9fbb2-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="9fbb2-127">Les projets `Grpc.AspNetCore` de serveur peuvent ajouter à `<PackageReference>` l’aide du package Manager dans Visual Studio ou en ajoutant un au fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="9fbb2-128">Les projets `Grpc.Tools` clients doivent faire directement référence aux autres paquets requis pour utiliser le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="9fbb2-129">Le paquet d’outillage n’est pas nécessaire au `PrivateAssets="All"`moment de l’exécution, de sorte que la dépendance est marquée par :</span><span class="sxs-lookup"><span data-stu-id="9fbb2-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="9fbb2-130">Actifs générés par CMD</span><span class="sxs-lookup"><span data-stu-id="9fbb2-130">Generated C# assets</span></span>

<span data-ttu-id="9fbb2-131">Le paquet d’outillage génère les types Cmd représentant les messages définis dans les fichiers \* \*.proto\* inclus.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="9fbb2-132">Pour les actifs côté serveur, un type de base de service abstrait est généré.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="9fbb2-133">Le type de base contient les définitions de tous les appels gRPC contenus dans le fichier *.proto.*</span><span class="sxs-lookup"><span data-stu-id="9fbb2-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="9fbb2-134">Créer une mise en œuvre concrète du service qui dérive de ce type de base et met en œuvre la logique pour les appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="9fbb2-135">Pour `greet.proto`l’exemple décrit précédemment, `GreeterBase` un type `SayHello` abstrait qui contient une méthode virtuelle est généré.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="9fbb2-136">Une implémentation `GreeterService` concrète l’emporte sur la méthode et met en œuvre la logique de traitement de l’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="9fbb2-137">Pour les actifs du côté client, un type de client concret est généré.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="9fbb2-138">Les appels gRPC dans le fichier *.proto* sont traduits en méthodes sur le type concret, qui peut être appelé.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="9fbb2-139">Pour `greet.proto`l’exemple décrit précédemment, `GreeterClient` un type concret est généré.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="9fbb2-140">Appelez `GreeterClient.SayHelloAsync` pour lancer un appel gRPC sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="9fbb2-141">Par défaut, les actifs du serveur et du `<Protobuf>` client sont générés pour chaque \* \*\* fichier .proto inclus dans le groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="9fbb2-142">Pour s’assurer que seuls les actifs `GrpcServices` du serveur `Server`sont générés dans un projet serveur, l’attribut est défini à .</span><span class="sxs-lookup"><span data-stu-id="9fbb2-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="9fbb2-143">De même, l’attribut est défini dans les `Client` projets clients.</span><span class="sxs-lookup"><span data-stu-id="9fbb2-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="9fbb2-144">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9fbb2-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
