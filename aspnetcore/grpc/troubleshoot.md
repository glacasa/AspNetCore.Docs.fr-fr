---
title: Dépanner gRPC sur .NET Core
author: jamesnk
description: Erreurs de dépannage lors de l’utilisation de gRPC sur .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664128"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Dépanner gRPC sur .NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

Ce document traite des problèmes couramment rencontrés lors du développement d’applications gRPC sur .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Décalage entre la configuration client et service SSL/TLS

Le modèle et les échantillons gRPC utilisent [transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) pour sécuriser les services gRPC par défaut. les clients de gRPC doivent utiliser une connexion sécurisée pour appeler les services gRPC sécurisés avec succès.

Vous pouvez vérifier que le service ASP.NET Core gRPC utilise TLS dans les journaux écrits sur le démarrage de l’application. Le service sera à l’écoute sur un point de terminaison HTTPS :

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Le client .NET `https` Core doit utiliser dans l’adresse du serveur pour passer des appels avec une connexion sécurisée :

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Toutes les implémentations de clients gRPC prennent en charge TLS. gRPC clients d’autres langues nécessitent `SslCredentials`généralement le canal configuré avec . `SslCredentials`spécifie le certificat que le client utilisera, et il doit être utilisé au lieu d’informations d’identification non sécurisées. Pour des exemples de configuration des différentes implémentations de clients gRPC pour utiliser TLS, voir [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Appelez un service gRPC avec un certificat non fiable/invalide

Le client .NET gRPC exige que le service ait un certificat de confiance. Le message d’erreur suivant est retourné lors de l’appel d’un service gRPC sans certificat de confiance :

> Exception non gérée. System.Net.http.httpRequestException: La connexion SSL n’a pas pu être établie, voir exception interne.
> ---> System.Security.Authentication.AuthenticationException : Le certificat distant n’est pas valide selon la procédure de validation.

Vous pouvez voir cette erreur si vous testez votre application localement et le certificat de développement core HTTPS ASP.NET n’est pas fiable. Pour obtenir des instructions afin de résoudre ce problème, consultez [Faire confiance au certificat de développement ASP.NET Core HTTPS sur Windows et macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Si vous appelez un service gRPC sur une autre machine et que vous n’êtes pas en mesure de faire confiance au certificat, le client gRPC peut être configuré pour ignorer le certificat invalide. Le code suivant utilise [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) pour permettre aux appels sans certificat de confiance :

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Les certificats non fiables ne doivent être utilisés que lors du développement de l’application. Les applications de production doivent toujours utiliser des certificats valides.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Appelez les services gRPC non sécurisés avec le client .NET Core

Une configuration supplémentaire est nécessaire pour appeler des services gRPC non sécurisés avec le client .NET Core. Le client gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` doit `true` définir `http` le commutateur et l’utiliser dans l’adresse du serveur :

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Impossible de démarrer ASP.NET’application Core gRPC sur macOS

Kestrel ne prend pas en charge HTTP/2 avec TLS sur macOS et les anciennes versions Windows telles que Windows 7. Le modèle et les échantillons de ASP.NET Core gRPC utilisent TLS par défaut. Vous verrez le message d’erreur suivant lorsque vous tentez de démarrer le serveur gRPC :

> Impossible de https://localhost:5001 se lier à l’interface IPv4 loopback: «HTTP/2 sur TLS n’est pas pris en charge sur macOS en raison de la prise de support ALPN manquant.

Pour contourner ce problème, configurez Kestrel et le client gRPC pour utiliser HTTP/2 *sans* TLS. Vous ne devriez le faire que pendant le développement. Le fait de ne pas utiliser TLS entraînera l’envoi de messages gRPC sans cryptage.

Kestrel doit configurer un point de terminaison HTTP/2 sans TLS en *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Lorsqu’un point de terminaison HTTP/2 est configuré sans TLS, les `HttpProtocols.Http2` [protocoles ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doivent être définis . `HttpProtocols.Http1AndHttp2`ne peut pas être utilisé parce que TLS est tenu de négocier HTTP/2. Sans TLS, toutes les connexions à la valeur par défaut de point de terminaison à HTTP/1.1, et les appels gRPC échouent.

Le client gRPC doit également être configuré pour ne pas utiliser TLS. Pour plus d’informations, voir [Call insecure gRPC services avec .NET Core client](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 sans TLS ne doit être utilisé que pendant le développement de l’application. Les applications de production doivent toujours utiliser la sécurité des transports. Pour plus d’informations, voir [considérations de sécurité dans gRPC pour ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>les actifs gRPC C N’sont pas du code généré à partir de fichiers .proto

GRPC génération de code de clients en béton et les classes de base de service exige des fichiers protobuf et l’outillage pour être référencé à partir d’un projet. Vous devez inclure :

* *.fichiers proto* que vous `<Protobuf>` souhaitez utiliser dans le groupe d’éléments. [Les fichiers *.proto* importés](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) doivent être référencés par le projet.
* Référence de paquet au paquet d’outillage gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Pour plus d’informations sur la génération <xref:grpc/basics>d’actifs GRPC C, voir .

Par défaut, `<Protobuf>` une référence génère un client concret et une classe de base de service. L’attribut de `GrpcServices` l’élément de référence peut être utilisé pour limiter la génération d’actifs C. Les `GrpcServices` options valides sont les suivantes :

* `Both`(par défaut lorsqu’il n’est pas présent)
* `Server`
* `Client`
* `None`

Une application web ASP.NET Core hébergeant les services gRPC n’a besoin que de la classe de base de service générée :

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Une application client gRPC effectuant des appels gRPC n’a besoin que du client en béton généré :

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Projets WPF incapables de générer des actifs gRPC C ' à partir de fichiers .proto

Les projets WPF ont un [problème connu](https://github.com/dotnet/wpf/issues/810) qui empêche la génération de code gRPC de fonctionner correctement. Tous les types de gRPC générés `Grpc.Tools` dans un projet WPF par référencement et *.proto* fichiers créeront des erreurs de compilation lorsqu’ils sont utilisés :

> erreur CS0246: Le type ou nom de l’espace de nom «MyGrpcServices» n’a pas pu être trouvé (manquez-vous une directive en utilisant ou une référence d’assemblage?)

Vous pouvez contourner ce problème en :

1. Créez un nouveau projet de bibliothèque de classe .NET Core.
2. Dans le nouveau projet, ajoutez des références pour activer [la génération de code C à partir de * \** fichiers .proto :](xref:grpc/basics#generated-c-assets)
    * Ajoutez une référence au package [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
    * Ajoutez * \** des fichiers `<Protobuf>` .proto au groupe d’éléments.
3. Dans l’application WPF, ajoutez une référence au nouveau projet.

L’application WPF peut utiliser les types générés par le GRPC à partir du projet de bibliothèque de nouvelle classe.

[!INCLUDE[](~/includes/gRPCazure.md)]
