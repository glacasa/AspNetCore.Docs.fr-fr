---
title: gRPC pour configuration .NET
author: jamesnk
description: Découvrez comment configurer gRPC pour les applications .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: cabe2d86f535bf3063dd7ede9e8a3bc5de70e244
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666900"
---
# <a name="grpc-for-net-configuration"></a>gRPC pour configuration .NET

## <a name="configure-services-options"></a>Configurer les options de services

les services gRPC `AddGrpc` sont configurés avec dans *Startup.cs*. Le tableau suivant décrit les options de configuration des services gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| MaxSendMessageSize (en) | `null` | La taille maximale du message dans les octets qui peuvent être envoyés à partir du serveur. Tenter d’envoyer un message qui dépasse la taille maximale du message configurée entraîne une exception. |
| MaxReceiveMessageSize | 4 Mo | La taille maximale du message dans les octets qui peuvent être reçus par le serveur. Si le serveur reçoit un message qui dépasse cette limite, il lance une exception. L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| EnableDetailedErrors | `false` | Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode de service. Par défaut, il s’agit de `false`. Réglage `EnableDetailedErrors` `true` pour peut fuite d’informations sensibles. |
| CompressionProvideurs | gzip | Une collection de fournisseurs de compressions utilisés pour comprimer et décompresser les messages. Les fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **de gzip.** |
| <span style="word-break:normal;word-wrap:normal">RéponseCompressionAlgorithm</span> | `null` | L’algorithme de compression utilisé pour compresser les messages envoyés à partir du serveur. L’algorithme doit correspondre `CompressionProviders`à un fournisseur de compression dans . Pour que l’algorithme comprime une réponse, le client doit indiquer qu’il prend en charge l’algorithme en l’envoyant dans l’en-tête **grpc-accept-encoding.** |
| RéponseCompressionLe niveau | `null` | Le niveau de compresse utilisé pour compresser les messages envoyés à partir du serveur. |
| Intercepteurs | None | Une collection d’intercepteurs qui sont exécutés à chaque appel gRPC. Les intercepteurs sont exécutés dans l’ordre où ils sont enregistrés. Les intercepteurs configurés à l’échelle mondiale sont exécutés avant que les intercepteurs ne soient configurés pour un seul service. Pour plus d’informations sur les intercepteurs gRPC, voir [intercepteurs gRPC vs Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Les options peuvent être configurées pour tous `AddGrpc` les `Startup.ConfigureServices`services en fournissant un délégué aux options à l’appel :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Les options pour un service unique `AddGrpc` remplacent les options `AddServiceOptions<TService>`globales fournies et peuvent être configurées à l’aide de :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurer les options client

la configuration du client `GrpcChannelOptions`gRPC est définie sur . Le tableau suivant décrit les options de configuration des canaux gRPC :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| HttpClient | Nouvelle instance | L’utilisé `HttpClient` pour faire des appels gRPC. Un client peut être configuré pour configurer une coutume, `HttpClientHandler`ou ajouter des gestionnaires supplémentaires au pipeline HTTP pour les appels gRPC. Si `HttpClient` aucun n’est `HttpClient` spécifié, une nouvelle instance est créée pour le canal. Il sera automatiquement éliminé. |
| Disposez-vous | `false` | Si `true`, `HttpClient` et un est `HttpClient` spécifié, alors l’instance sera éliminée lorsque l’est `GrpcChannel` éliminé. |
| LoggerFactory | `null` | L’utilisé `LoggerFactory` par le client pour enregistrer des informations sur les appels gRPC. Une `LoggerFactory` instance peut être résolue à `LoggerFactory.Create`partir de l’injection de dépendance ou créée à l’aide de . Pour des exemples de configuration de l’exploitation forestière, voir <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize (en) | `null` | La taille maximale du message dans les octets qui peuvent être envoyés par le client. Tenter d’envoyer un message qui dépasse la taille maximale du message configurée entraîne une exception. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 Mo | La taille maximale du message dans les octets qui peuvent être reçus par le client. Si le client reçoit un message qui dépasse cette limite, il jette une exception. L’augmentation de cette valeur permet au client de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| Informations d'identification | `null` | Une instance de `ChannelCredentials`. Les informations d’identification sont utilisées pour ajouter des métadonnées d’authentification aux appels gRPC. |
| CompressionProvideurs | gzip | Une collection de fournisseurs de compressions utilisés pour comprimer et décompresser les messages. Les fournisseurs de compression personnalisés peuvent être créés et ajoutés à la collection. Les fournisseurs configurés par défaut prennent en charge la compression **de gzip.** |

Le code suivant :

* Définit la taille maximale d’envoyer et de recevoir le message sur le canal.
* Crée un client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
