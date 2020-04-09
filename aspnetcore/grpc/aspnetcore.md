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
# <a name="grpc-services-with-aspnet-core"></a>Services gRPC avec ASP.NET Core

Ce document montre comment commencer avec les services gRPC en utilisant ASP.NET Core.

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Bien démarrer avec le service gRPC dans ASP.NET Core

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Voir [Démarrer avec les services gRPC](xref:tutorials/grpc/grpc-start) pour des instructions détaillées sur la façon de créer un projet gRPC.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Exécutez `dotnet new grpc -o GrpcGreeter` à partir de la ligne de commande.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Ajouter des services gRPC à une application ASP.NET Core

gRPC nécessite le paquet [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)

### <a name="configure-grpc"></a>Configurer gRPC

En *Startup.cs*:

* gRPC est activé `AddGrpc` avec la méthode.
* Chaque service gRPC est ajouté au `MapGrpcService` pipeline de routage par la méthode.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET les middlewares et les fonctionnalités de base partagent le pipeline de routage, donc une application peut être configurée pour servir des gestionnaires de demandes supplémentaires. Les gestionnaires de demandes supplémentaires, tels que les contrôleurs MVC, travaillent en parallèle avec les services gRPC configurés.

### <a name="configure-kestrel"></a>Configurer Kestrel

Points d’évaluation Kestrel gRPC:

* Exiger HTTP/2.
* Doit être sécurisé avec [la sécurité des couches de transport (TLS)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

gRPC nécessite HTTP/2. gRPC pour ASP.NET Core valide [httpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) est `HTTP/2`.

Kestrel [prend en charge HTTP/2](xref:fundamentals/servers/kestrel#http2-support) sur la plupart des systèmes d’exploitation modernes. Les paramètres Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.

#### <a name="tls"></a>TLS

Les paramètres de Kestrel utilisés pour le gRPC doivent être fixés avec TLS. En cours de développement, un critère d’évaluation sécurisé avec TLS est automatiquement créé au `https://localhost:5001` moment où le certificat de développement ASP.NET Core est présent. Aucune configuration n'est requise. Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.

En production, TLS doit être explicitement configuré. Dans l’exemple suivant *appsettings.json,* un point de terminaison HTTP/2 sécurisé avec TLS est fourni :

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativement, les paramètres Kestrel peuvent être configurés en *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Négociation de protocole

TLS est utilisé pour plus que la sécurisation de la communication. La poignée de main TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles. Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.

Si un point de terminaison HTTP/2 est configuré sans TLS, les `HttpProtocols.Http2` [protocoles ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doivent être configurés . Un point de terminaison avec `HttpProtocols.Http1AndHttp2`plusieurs protocoles (par exemple, ) ne peut pas être utilisé sans TLS parce qu’il n’y a pas de négociation. Toutes les connexions à la valeur de point de terminaison non sécurisée par défaut à HTTP/1.1, et les appels gRPC échouent.

Pour plus d’informations sur l’activation http/2 et TLS avec Kestrel, voir [configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS. Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS. Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Intégration avec les API de base ASP.NET

les services gRPC ont un accès complet aux caractéristiques ASP.NET Core telles que [l’injection de dépendance](xref:fundamentals/dependency-injection) (DI) et [l’enregistrement .](xref:fundamentals/logging/index) Par exemple, la mise en œuvre du service peut résoudre un service d’enregistreur à partir du conteneur DI via le constructeur :

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Par défaut, la mise en œuvre du service gRPC peut résoudre d’autres services d' DI à vie (Singleton, Scoped ou Transitoire).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Résoudre httpContexte dans les méthodes gRPC

L’API gRPC donne accès à certaines données de messages HTTP/2, telles que la méthode, l’hôte, l’en-tête et les remorques. L’accès `ServerCallContext` se fait par l’argumentation transmise à chaque méthode gRPC :

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`n’offre pas `HttpContext` un accès complet à toutes les ASP.NET API. La `GetHttpContext` méthode d’extension `HttpContext` donne un accès complet au message http://http/2 sous-jacent dans ASP.NET API :

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
