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
# <a name="test-services-with-grpc-tools"></a>Tester les services avec les outils gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

Les outils sont disponibles pour gRPC qui permet aux développeurs de tester les services sans générer d’applications clientes. [gRPCurl](https://github.com/fullstorydev/grpcurl) est un outil de ligne de commande qui fournit une interaction avec les services gRPC. [gRPCui](https://github.com/fullstorydev/grpcui) ajoute une interface utilisateur Web interactive pour gRPC.

Cet article explique comment :

* Téléchargez et installez gRPCurl et gRPCui.
* Configurer la réflexion gRPC avec une application gRPC ASP.NET Core.
* Découvrez et testez les services gRPC avec `grpcurl` .
* Interagissez avec les services gRPC via un navigateur à l’aide de `grpcui` .

## <a name="about-grpcurl"></a>À propos de gRPCurl

gRPCurl est un outil de ligne de commande créé par la communauté gRPC. Ses fonctionnalités sont les suivantes :

* Appel des services gRPC, y compris les services de streaming.
* Découverte de service à l’aide de la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Répertorier et décrire les services gRPC.
* Fonctionne avec des serveurs sécurisés (TLS) et non sécurisés (texte brut).

Pour plus d’informations sur le téléchargement et l’installation de `grpcurl` , consultez la [page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl#installation).

## <a name="setup-grpc-reflection"></a>Configurer la réflexion gRPC

`grpcurl` doit connaître le contrat Protobuf des services avant de pouvoir les appeler. Il existe deux façons d'effectuer cette opération :

* Utilisez la réflexion gRPC pour découvrir les contrats de service.
* Spécifiez les fichiers *. proto* dans les arguments de ligne de commande.

Il est plus facile d’utiliser gRPCurl avec la réflexion gRPC et la découverte de service. gRPC ASP.NET Core offre une prise en charge intégrée de la réflexion gRPC avec le package [gRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) . Pour configurer la réflexion dans une application :

* Ajoutez une `Grpc.AspNetCore.Server.Reflection` référence de package.
* Inscrire la réflexion dans *Startup.cs*:
  * `AddGrpcReflection` pour inscrire des services qui activent la réflexion.
  * `MapGrpcReflectionService` pour ajouter le point de terminaison du service de réflexion.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>Utilisez `grpcurl`.

L' `-help` argument décrit les `grpcurl` options de ligne de commande :

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Découvrir les services

Utilisez le `describe` verbe pour afficher les services définis par le serveur :

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

L’exemple précédent :

* Exécute un `describe` verbe sur le serveur `localhost:5001` .
* Imprime les services et les méthodes retournés par la réflexion gRPC.
  * `Greeter` est un service implémenté par l’application.
  * `ServerReflection` est le service ajouté par le `Grpc.AspNetCore.Server.Reflection` Package.

Combiner `describe` avec un service, une méthode ou un nom de message pour afficher ses détails :

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Appeler gRPC services

Appelez un service gRPC en spécifiant un nom de service et de méthode, ainsi qu’un argument JSON qui représente le message de demande. Le JSON est converti en Protobuf et envoyé au service.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

L’exemple précédent :

* `-d` l’argument spécifie un message de demande avec JSON. Cet argument doit précéder l’adresse du serveur et le nom de la méthode.
* Appelle la `SayHello` méthode sur le `greeter.Greeter` service.
* Imprime le message de réponse au format JSON.

## <a name="about-grpcui"></a>À propos de gRPCui

gRPCui est une interface utilisateur Web interactive pour gRPC. Il s’appuie sur gRPCurl et offre une interface graphique utilisateur pour la découverte et le test des services gRPC, à l’instar des outils HTTP tels que le poster.

Pour plus d’informations sur le téléchargement et l’installation de `grpcui` , consultez la [page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Utilisation de `grpcui`

Exécutez `grpcui` avec l’adresse du serveur pour interagir comme un argument.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

L’outil lance une fenêtre de navigateur avec l’interface utilisateur Web interactive. les services gRPC sont automatiquement découverts à l’aide de la réflexion gRPC.

![interface utilisateur Web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl)
* [Page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui)
* [GRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
