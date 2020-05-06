---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core à appeler à partir d’applications de navigateur à l’aide de gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774741"
---
# <a name="use-grpc-in-browser-apps"></a>Utiliser gRPC dans les applications de navigateur

Par [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-la prise en charge de Web dans .NET est expérimentale**
>
> gRPC-Web pour .NET est un projet expérimental, et non un produit validé. Nous voulons :
>
> * Testez que notre approche de l’implémentation de gRPC-Web fonctionne.
> * Recevez des commentaires sur si cette approche est utile pour les développeurs .NET par rapport à la méthode traditionnelle de configuration de gRPC-Web via un proxy.
>
> N’hésitez pas [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) à nous faire part de vos commentaires.

Il n’est pas possible d’appeler un service gRPC HTTP/2 à partir d’une application basée sur un navigateur. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) est un protocole qui permet au navigateur JavaScript Blazor et aux applications d’appeler des services gRPC. Cet article explique comment utiliser gRPC-Web dans .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web dans ASP.NET Core vs. Envoy

Il existe deux façons d’ajouter gRPC-Web à une application ASP.NET Core :

* Prenez en charge gRPC-Web en parallèle de gRPC HTTP/2 dans ASP.NET Core. Cette option utilise l’intergiciel (middleware) `Grpc.AspNetCore.Web` fourni par le package.
* Utilisez la prise en charge gRPC-Web [du proxy Envoy](https://www.envoyproxy.io/) pour traduire GRPC-Web en gRPC http/2. L’appel traduit est ensuite transféré vers l’application ASP.NET Core.

Chaque approche présente des avantages et des inconvénients. Si vous utilisez déjà Envoy en tant que proxy dans l’environnement de votre application, il peut être judicieux de l’utiliser également pour fournir la prise en charge de gRPC-Web. Si vous souhaitez une solution simple pour gRPC-Web qui nécessite uniquement ASP.NET Core, `Grpc.AspNetCore.Web` est un bon choix.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurer gRPC-Web dans ASP.NET Core

les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC. gRPC-Web ne nécessite aucune modification des services. La seule modification concerne la configuration du démarrage.

Pour activer gRPC-Web avec un service ASP.NET Core gRPC :

* Ajoutez une référence au package [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Configurez l’application pour utiliser gRPC-Web `AddGrpcWeb` en `UseGrpcWeb` ajoutant et à *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Le code précédent :

* Ajoute l’intergiciel (middleware) Web gRPC `UseGrpcWeb`,, après le routage et avant les points de terminaison.
* Spécifie `endpoints.MapGrpcService<GreeterService>()` que la méthode prend en charge `EnableGrpcWeb`gRPC-Web avec. 

Vous pouvez également configurer tous les services pour prendre en charge gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` Web en ajoutant à ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Il existe un problème connu qui provoque l’échec de gRPC-Web lorsqu’il est [hébergé par http. sys](xref:fundamentals/servers/httpsys) dans .net Core 3. x.
>
> Une solution de contournement pour obtenir gRPC-Web Working sur http. sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web et CORS

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction s’applique à la création d’appels gRPC-Web avec des applications de navigateur. Par exemple, une application de navigateur servie par est bloquée de l' `https://www.contoso.com` appel de gRPC `https://services.contoso.com`-services Web hébergés sur. Le partage des ressources Cross-Origin (CORS) peut être utilisé pour assouplir cette restriction.

Pour permettre à votre application de navigateur d’effectuer des appels gRPC-Web Cross-Origin, configurez [cors dans ASP.net Core](xref:security/cors). Utilisez la prise en charge de CORS intégrée et exposez des en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>spécifiques à gRPC avec.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Le code précédent :

* Appelle `AddCors` pour ajouter des services cors et configure une stratégie cors qui expose des en-têtes spécifiques à gRPC.
* Appelle `UseCors` pour ajouter l’intergiciel (middleware) cors après le routage et avant les points de terminaison.
* Spécifie `endpoints.MapGrpcService<GreeterService>()` que la méthode prend `RequiresCors`en charge cors avec.

## <a name="call-grpc-web-from-the-browser"></a>Appeler gRPC-Web à partir du navigateur

Les applications de navigateur peuvent utiliser gRPC-Web pour appeler des services gRPC. Il existe certaines exigences et limitations lors de l’appel de services gRPC avec gRPC-Web à partir du navigateur :

* Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.
* Les appels de diffusion en continu et bidirectionnelle du client ne sont pas pris en charge. La diffusion en continu du serveur est prise en charge.
* L’appel de services gRPC sur un autre domaine requiert la configuration de [cors](xref:security/cors) sur le serveur.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-client Web

Il existe un client JavaScript gRPC-Web. Pour obtenir des instructions sur l’utilisation de gRPC-Web à partir de JavaScript, consultez [écrire du code JavaScript client avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurer gRPC-Web avec le client .NET gRPC

Le client .NET gRPC peut être configuré pour effectuer des appels gRPC-Web. Cela est utile pour [ Blazor les applications webassembly](xref:blazor/index#blazor-webassembly) , qui sont hébergées dans le navigateur et qui ont les mêmes limitations http du code JavaScript. L’appel de gRPC-Web avec un client .NET est [identique à http/2 gRPC](xref:grpc/client). La seule modification est la manière dont le canal est créé.

Pour utiliser gRPC-Web :

* Ajoutez une référence au package [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Assurez-vous que la référence au package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) est 2.27.0 ou supérieure.
* Configurez le canal pour `GrpcWebHandler`utiliser le :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Le code précédent :

* Configure un canal pour utiliser gRPC-Web.
* Crée un client et effectue un appel à l’aide du canal.

A `GrpcWebHandler` les options de configuration suivantes lors de la création :

* **InnerHandler**: sous- <xref:System.Net.Http.HttpMessageHandler> jacent qui effectue la requête http gRPC, par exemple `HttpClientHandler`,.
* **Mode**: type énumération qui spécifie si la requête `Content-Type` http gRPC `application/grpc-web` est `application/grpc-web-text`ou.
    * `GrpcWebMode.GrpcWeb`configure le contenu à envoyer sans encodage. Valeur par défaut.
    * `GrpcWebMode.GrpcWebText`configure le contenu pour qu’il soit encodé en base64. Requis pour les appels de diffusion en continu du serveur dans les navigateurs.
* **HttpVersion**: protocole `Version` http utilisé pour définir [HttpRequestMessage. version](xref:System.Net.Http.HttpRequestMessage.Version) sur la requête http gRPC sous-jacente. gRPC-Web n’a pas besoin d’une version spécifique et ne remplace pas la valeur par défaut, sauf indication contraire.

> [!IMPORTANT]
> Les clients gRPC générés ont des méthodes synchrones et asynchrones pour appeler des méthodes unaires. Par exemple, `SayHello` est Sync et `SayHelloAsync` est Async. L’appel d’une méthode Sync Blazor dans une application webassembly entraîne la non-réponse de l’application. Les méthodes Async doivent toujours être utilisées Blazor dans webassembly.

## <a name="additional-resources"></a>Ressources supplémentaires

* [gRPC pour le projet GitHub des clients Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
