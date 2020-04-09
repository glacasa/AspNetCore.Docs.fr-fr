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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migration des services gRPC de C Core vers ASP.NET Core

Par [John Luo](https://github.com/juntaoluo)

En raison de la mise en œuvre de la pile sous-jacente, toutes les fonctionnalités ne fonctionnent pas de la même manière entre les applications [GRPC basées sur le C-core](https://grpc.io/blog/grpc-stacks) et ASP.NET applications basées sur le noyau. Ce document met en évidence les principales différences pour la migration entre les deux piles.

## <a name="grpc-service-implementation-lifetime"></a>gRPC mise en œuvre du service à vie

Dans la pile ASP.NET Core, les services gRPC, par défaut, sont créés avec une [durée de vie étendue](xref:fundamentals/dependency-injection#service-lifetimes). En revanche, gRPC C-core par défaut se lie à un service avec une [durée de vie singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Une durée de vie étendue permet à la mise en œuvre du service de résoudre d’autres services à durée de vie. Par exemple, une durée de `DbContext` vie étendue peut également se résoudre à partir du contenant DI par injection de constructeurs. Utilisation de la durée de vie portée :

* Une nouvelle instance de la mise en œuvre du service est construite pour chaque demande.
* Il n’est pas possible de partager l’état entre les demandes via les membres d’instance sur le type de implémentation.
* On s’attend à stocker les états partagés dans un service singleton dans le conteneur DI. Les états partagés stockés sont résolus dans le constructeur de la mise en œuvre du service gRPC.

Pour plus d’informations sur <xref:fundamentals/dependency-injection#service-lifetimes>les durées de service, voir .

### <a name="add-a-singleton-service"></a>Ajouter un service singleton

Pour faciliter la transition d’une mise en œuvre de base C gRPC à ASP.NET Core, il est possible de changer la durée de vie du service de mise en œuvre du service de portée à singleton. Il s’agit d’ajouter un exemple de la mise en œuvre du service au conteneur DI :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Cependant, une mise en œuvre de service avec une durée de vie à un seulton n’est plus en mesure de résoudre les services d’envergure par injection de constructeurs.

## <a name="configure-grpc-services-options"></a>Configurer les options de services gRPC

Dans les applications C-core, `grpc.max_receive_message_length` des `grpc.max_send_message_length` paramètres tels `ChannelOption` que et sont configurés avec lors [de la construction de l’instance Server](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

Dans ASP.NET Core, gRPC fournit `GrpcServiceOptions` la configuration à travers le type. Par exemple, la taille maximale du message entrant d’un `AddGrpc`service gRPC peut être configurée via . L’exemple suivant `MaxReceiveMessageSize` modifie le défaut de 4 Mo à 16 Mo :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Pour plus d’informations <xref:grpc/configuration>sur la configuration, voir .

## <a name="logging"></a>Journalisation

Les applications C-core s’appuient sur la `GrpcEnvironment` configuration de [l’enregistreur](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) à des fins de débogage. La pile ASP.NET Core fournit cette fonctionnalité grâce à [l’API d’enregistrement](xref:fundamentals/logging/index). Par exemple, un enregistreur peut être ajouté au service gRPC par injection de constructeurs :

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Les applications C-core configurent HTTPS via la [propriété Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Un concept similaire est utilisé pour configurer les serveurs dans ASP.NET Core. Par exemple, Kestrel utilise [la configuration de point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration) pour cette fonctionnalité.

## <a name="grpc-interceptors-vs-middleware"></a>intercepteurs gRPC vs Middleware

ASP.NET [Core middleware](xref:fundamentals/middleware/index) offre des fonctionnalités similaires par rapport aux intercepteurs dans les applications GRPC basées sur C-core. ASP.NET Core middleware et intercepteurs sont conceptuellement similaires. Les deux :

* Sont utilisés pour construire un pipeline qui gère une demande gRPC.
* Autoriser l’exécution des travaux avant ou après le composant suivant dans le pipeline.
* Fournir l’accès à `HttpContext`:
  * Au milieu, `HttpContext` le est un paramètre.
  * Dans les `HttpContext` intercepteurs, il `ServerCallContext` est accessible `ServerCallContext.GetHttpContext` à l’aide du paramètre avec la méthode d’extension. Notez que cette fonctionnalité est spécifique aux intercepteurs fonctionnant dans ASP.NET Core.

gRPC Interceptor différences de ASP.NET Core Middleware:

* Intercepteurs:
  * Opérer sur la couche d’abstraction gRPC à l’aide du [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fournir l’accès à :
    * Le message déséialisé envoyé à un appel.
    * Le message est renvoyé de l’appel avant qu’il ne soit sérialisé.
  * Peut attraper et gérer les exceptions jetées des services gRPC.
* Middleware :
  * Exécute devant les intercepteurs gRPC.
  * Fonctionne sur les messages HTTP/2 sous-jacents.
  * Ne peut accéder aux octets qu’à partir des flux de demande et de réponse.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
