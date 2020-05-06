---
title: configuration de gRPC pour .NET
author: jamesnk
description: Découvrez comment configurer gRPC pour les applications .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: 6e4259b538d32490d5281f189a04ab5a04dbcce5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774689"
---
# <a name="grpc-for-net-configuration"></a>configuration de gRPC pour .NET

## <a name="configure-services-options"></a>Configurer les options des services

les services gRPC sont configurés avec `AddGrpc` dans *Startup.cs*. Le tableau suivant décrit les options de configuration des services gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Taille maximale du message, en octets, qui peut être envoyée à partir du serveur. Toute tentative d’envoi d’un message qui dépasse la taille de message maximale configurée entraîne une exception. Lorsque la valeur `null`est, la taille du message est illimitée. |
| MaxReceiveMessageSize | 4 Mo | Taille maximale du message, en octets, qui peut être reçue par le serveur. Si le serveur reçoit un message qui dépasse cette limite, il lève une exception. L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. Lorsque la valeur `null`est, la taille du message est illimitée. |
| EnableDetailedErrors | `false` | Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de service. Par défaut, il s’agit de `false`. La `EnableDetailedErrors` définition `true` de sur peut entraîner une fuite d’informations sensibles. |
| CompressionProviders | gzip | Collection de fournisseurs de compression utilisée pour compresser et décompresser des messages. Des fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algorithme de compression utilisé pour compresser les messages envoyés à partir du serveur. L’algorithme doit correspondre à un fournisseur `CompressionProviders`de compression dans. Pour que l’algorithme compresse une réponse, le client doit indiquer qu’il prend en charge l’algorithme en l’envoyant dans l’en-tête **GRPC-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Niveau de compression utilisé pour compresser les messages envoyés à partir du serveur. |
| Intercepteurs | None | Collection d’intercepteurs exécutés avec chaque appel gRPC. Les intercepteurs sont exécutés dans l’ordre dans lequel ils sont inscrits. Les intercepteurs configurés globalement sont exécutés avant les intercepteurs configurés pour un service unique. Pour plus d’informations sur les intercepteurs gRPC, consultez [intercepteurs gRPC et intergiciel](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Les options peuvent être configurées pour tous les services en fournissant un délégué `AddGrpc` d’options `Startup.ConfigureServices`à l’appel dans :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Les options pour un seul service remplacent les options globales `AddGrpc` fournies dans et peuvent être `AddServiceOptions<TService>`configurées à l’aide de :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurer les options du client

la configuration du `GrpcChannelOptions`client gRPC est activée. Le tableau suivant décrit les options de configuration des canaux gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| HttpClient | Nouvelle instance | Utilisé `HttpClient` pour effectuer des appels gRPC. Un client peut être configuré pour configurer un personnalisé `HttpClientHandler`ou ajouter des gestionnaires supplémentaires au pipeline HTTP pour les appels gRPC. Si aucun `HttpClient` n’est spécifié, une nouvelle `HttpClient` instance est créée pour le canal. Elle est automatiquement supprimée. |
| DisposeHttpClient | `false` | Si `true`, et un `HttpClient` est spécifié, l' `HttpClient` instance est supprimée lors de la suppression de `GrpcChannel` . |
| LoggerFactory | `null` | Utilisé `LoggerFactory` par le client pour enregistrer des informations sur les appels gRPC. Une `LoggerFactory` instance peut être résolue à partir de l’injection `LoggerFactory.Create`de dépendances ou créée à l’aide de. Pour obtenir des exemples de configuration de la <xref:grpc/diagnostics#grpc-client-logging>journalisation, consultez. |
| MaxSendMessageSize | `null` | Taille maximale du message, en octets, qui peut être envoyée à partir du client. Toute tentative d’envoi d’un message qui dépasse la taille de message maximale configurée entraîne une exception. Lorsque la valeur `null`est, la taille du message est illimitée. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 Mo | Taille maximale du message, en octets, qui peut être reçue par le client. Si le client reçoit un message qui dépasse cette limite, il lève une exception. L’augmentation de cette valeur permet au client de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire. Lorsque la valeur `null`est, la taille du message est illimitée. |
| Informations d'identification | `null` | Une instance de `ChannelCredentials`. Les informations d’identification sont utilisées pour ajouter des métadonnées d’authentification à des appels gRPC. |
| CompressionProviders | gzip | Collection de fournisseurs de compression utilisée pour compresser et décompresser des messages. Des fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **gzip** . |

Le code suivant :

* Définit la taille maximale des messages d’envoi et de réception sur le canal.
* Crée un client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
