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
# <a name="grpc-services-with-c"></a>gRPC services avec C\#

Ce document décrit les concepts nécessaires à l’écriture d’applications [gRPC](https://grpc.io/docs/guides/) dans C. Les sujets abordés ici s’appliquent à la fois [c’est](https://grpc.io/blog/grpc-stacks)basé sur C et ASP.NET applications gRPC basées sur core.

## <a name="proto-file"></a>fichier proto

gRPC utilise une approche de premier contrat pour le développement de l’API. Les tampons de protocole (protobuf) sont utilisés comme langage de conception d’interface (IDL) par défaut. Le fichier * \*.proto* contient :

* La définition du service gRPC.
* Les messages envoyés entre les clients et les serveurs.

Pour plus d’informations sur la syntaxe des fichiers protobuf, voir la [documentation officielle (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Par exemple, considérez le fichier *greet.proto* utilisé dans [Get started with gRPC service](xref:tutorials/grpc/grpc-start):

* Définit un `Greeter` service.
* Le `Greeter` service définit `SayHello` un appel.
* `SayHello`envoie `HelloRequest` un message `HelloReply` et reçoit un message :

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Ajouter un fichier .proto\# à une application C

Le * \** fichier .proto est inclus dans un `<Protobuf>` projet en l’ajoutant au groupe d’objets :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Support d’outillage CMD pour les fichiers .proto

Le paquet d’outillage [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) est nécessaire pour générer les actifs C 'à partir de * \*fichiers .proto.* Les actifs générés (fichiers) :

* Sont générés sur une base nécessaire chaque fois que le projet est construit.
* Ne sont pas ajoutés au projet ou contrôlés dans le contrôle source.
* Sont un artefact de construction contenu dans le répertoire *obj.*

Ce paquet est exigé par le serveur et les projets clients. Le `Grpc.AspNetCore` métapackage comprend `Grpc.Tools`une référence à . Les projets `Grpc.AspNetCore` de serveur peuvent ajouter à `<PackageReference>` l’aide du package Manager dans Visual Studio ou en ajoutant un au fichier du projet :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Les projets `Grpc.Tools` clients doivent faire directement référence aux autres paquets requis pour utiliser le client gRPC. Le paquet d’outillage n’est pas nécessaire au `PrivateAssets="All"`moment de l’exécution, de sorte que la dépendance est marquée par :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Actifs générés par CMD

Le paquet d’outillage génère les types Cmd représentant les messages définis dans les fichiers * \*.proto* inclus.

Pour les actifs côté serveur, un type de base de service abstrait est généré. Le type de base contient les définitions de tous les appels gRPC contenus dans le fichier *.proto.* Créer une mise en œuvre concrète du service qui dérive de ce type de base et met en œuvre la logique pour les appels gRPC. Pour `greet.proto`l’exemple décrit précédemment, `GreeterBase` un type `SayHello` abstrait qui contient une méthode virtuelle est généré. Une implémentation `GreeterService` concrète l’emporte sur la méthode et met en œuvre la logique de traitement de l’appel gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Pour les actifs du côté client, un type de client concret est généré. Les appels gRPC dans le fichier *.proto* sont traduits en méthodes sur le type concret, qui peut être appelé. Pour `greet.proto`l’exemple décrit précédemment, `GreeterClient` un type concret est généré. Appelez `GreeterClient.SayHelloAsync` pour lancer un appel gRPC sur le serveur.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Par défaut, les actifs du serveur et du `<Protobuf>` client sont générés pour chaque * \** fichier .proto inclus dans le groupe d’éléments. Pour s’assurer que seuls les actifs `GrpcServices` du serveur `Server`sont générés dans un projet serveur, l’attribut est défini à .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

De même, l’attribut est défini dans les `Client` projets clients.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
