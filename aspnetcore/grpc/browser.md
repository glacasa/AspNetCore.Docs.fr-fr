---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core pour être callable à partir d’applications de navigateur utilisant gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977143"
---
# <a name="use-grpc-in-browser-apps"></a>Utiliser gRPC dans les applications de navigateur

Par [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-Web support in .NET est expérimental**
>
> gRPC-Web pour .NET est un projet expérimental, pas un produit engagé. Nous voulons :
>
> * Testez que notre approche de la mise en œuvre de gRPC-Web fonctionne.
> * Obtenez des commentaires sur si cette approche est utile pour les développeurs .NET par rapport à la façon traditionnelle de mettre en place gRPC-Web via un proxy.
>
> S’il [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) vous plaît laisser des commentaires pour s’assurer que nous construisons quelque chose que les développeurs aiment et sont productifs avec.

Il n’est pas possible d’appeler un service HTTP/2 gRPC à partir d’une application basée sur le navigateur. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet aux applications JavaScript et Blazor d’appeler des services gRPC. Cet article explique comment utiliser gRPC-Web dans .NET Core.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurer gRPC-Web dans ASP.NET Core

les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web aux côtés de HTTP/2 gRPC. gRPC-Web n’exige aucune modification des services. La seule modification est la configuration de démarrage.

Pour activer gRPC-Web avec un service ASP.NET Core gRPC :

* Ajoutez une référence au forfait [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)
* Configurer l’application pour utiliser gRPC-Web en ajoutant `AddGrpcWeb` et `UseGrpcWeb` en *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Le code précédent :

* Ajoute le gRPC-Web `UseGrpcWeb`middleware, , après routage et avant les points de terminaison.
* Spécifie la `endpoints.MapGrpcService<GreeterService>()` méthode prend `EnableGrpcWeb`en charge gRPC-Web avec . 

Alternativement, configurez tous les services pour `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` prendre en charge gRPC-Web en ajoutant à ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Il ya un problème connu qui provoque gRPC-Web à l’échec [lorsqu’il est hébergé par Http.sys](xref:fundamentals/servers/httpsys) dans .NET Core 3.x.
>
> Une solution de contournement pour obtenir gRPC-Web de travail sur Http.sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web et CORS

La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web. Cette restriction s’applique à la prise d’appels gRPC-Web avec des applications de navigateur. Par exemple, une application `https://www.contoso.com` de navigateur desservie par est empêchée `https://services.contoso.com`d’appeler les services gRPC-Web hébergés sur . Le partage des ressources d’origine croisée (CORS) peut être utilisé pour assouplir cette restriction.

Pour permettre à votre application de navigateur de passer des appels gRPC-Web d’origine croisée, configurez [CORS dans ASP.NET Core](xref:security/cors). Utilisez le support CORS intégré, et exposez les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>en-têtes spécifiques à gRPC avec .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Le code précédent :

* Appels `AddCors` à ajouter des services CORS et configure une stratégie CORS qui expose les en-têtes spécifiques à gRPC.
* Appels `UseCors` pour ajouter le middleware CORS après le routage et avant les points de terminaison.
* Specifie `endpoints.MapGrpcService<GreeterService>()` la méthode prend `RequiresCors`en charge CORS avec .

## <a name="call-grpc-web-from-the-browser"></a>Appelez gRPC-Web à partir du navigateur

Les applications de navigateur peuvent utiliser gRPC-Web pour appeler les services gRPC. Il ya quelques exigences et limitations lors de l’appel des services gRPC avec gRPC-Web à partir du navigateur:

* Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.
* Le streaming client et les appels bidirectionnels en streaming ne sont pas pris en charge. Le streaming serveur est pris en charge.
* L’appel des services gRPC sur un domaine différent nécessite [corS](xref:security/cors) d’être configuré sur le serveur.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web client

Il y a un client JavaScript gRPC-Web. Pour obtenir des instructions sur la façon d’utiliser gRPC-Web à partir de JavaScript, voir [écrire le code client JavaScript avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurer gRPC-Web avec le client .NET gRPC

Le client .NET gRPC peut être configuré pour passer des appels gRPC-Web. Ceci est utile pour les applications [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) qui sont hébergées dans le navigateur et ont les mêmes limites HTTP de code JavaScript. Appeler gRPC-Web avec un client .NET est [le même que HTTP/2 gRPC](xref:grpc/client). La seule modification est la façon dont le canal est créé.

Pour utiliser gRPC-Web :

* Ajoutez une référence au forfait [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)
* Assurez-vous que la référence au forfait [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) est de 2.27.0 ou plus.
* Configurer le canal `GrpcWebHandler`pour utiliser le :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Le code précédent :

* Configure un canal pour utiliser gRPC-Web.
* Crée un client et passe un appel à l’aide du canal.

Le `GrpcWebHandler` a les options de configuration suivantes lors de la création:

* **InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Le sous-jacent qui fait la `HttpClientHandler`demande gRPC HTTP, par exemple, .
* **Mode**: Un type d’énumération qui précise si `Content-Type` la `application/grpc-web` `application/grpc-web-text`demande de demande http://http de gRPC est ou .
    * `GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans codage. Valeur par défaut.
    * `GrpcWebMode.GrpcWebText`configure le contenu à coder de base64. Nécessaire pour les appels de streaming serveur dans les navigateurs.
* **HttpVersion**: `Version` Protocole HTTP utilisé pour définir [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) sur la demande httpRPC HTTP sous-jacente. gRPC-Web ne nécessite pas de version spécifique et ne remplace pas la valeur par défaut à moins d’être spécifiée.

> [!IMPORTANT]
> Les clients générés de gRPC ont des méthodes de synchronisation et d’async pour appeler des méthodes nonary. Par exemple, `SayHello` est `SayHelloAsync` synchronisé et est async. L’appel d’une méthode de synchronisation dans une application WebAssembly De Blazor entraînera une réponse imprésive de l’application. Les méthodes Async doivent toujours être utilisées dans Blazor WebAssembly.

## <a name="additional-resources"></a>Ressources supplémentaires

* [gRPC pour le projet GitHub de clients Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
