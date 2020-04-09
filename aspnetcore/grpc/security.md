---
title: Considérations de sécurité dans gRPC pour ASP.NET Core
author: jamesnk
description: Renseignez-vous sur les considérations de sécurité pour gRPC pour ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667320"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Considérations de sécurité dans gRPC pour ASP.NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article fournit des informations sur la sécurisation gRPC avec .NET Core.

## <a name="transport-security"></a>Sécurité du transport

les messages gRPC sont envoyés et reçus à l’aide de HTTP/2. Nous recommandons :

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) être utilisé pour sécuriser les messages dans les applications gRPC de production.
* les services gRPC ne doivent écouter et réagir que sur les ports sécurisés.

TLS est configuré dans Kestrel. Pour plus d’informations sur la configuration des paramètres Kestrel, voir [la configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Exceptions

Les messages d’exception sont généralement considérés comme des données sensibles qui ne devraient pas être révélées à un client. Par défaut, gRPC n’envoie pas les détails d’une exception lancée par un service gRPC au client. Au lieu de cela, le client reçoit un message générique indiquant qu’une erreur s’est produite. La livraison de messages d’exception au client peut être remplacée (par exemple, dans le développement ou le test) avec [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Les messages d’exception ne doivent pas être exposés au client dans les applications de production.

## <a name="message-size-limits"></a>Limites de taille des messages

Les messages entrants aux clients et services de gRPC sont chargés dans la mémoire. Les limites de taille des messages sont un mécanisme pour aider à empêcher le GRPC de consommer des ressources excessives.

gRPC utilise des limites de taille par message pour gérer les messages entrants et sortants. Par défaut, gRPC limite les messages entrants à 4 Mo. Il n’y a pas de limite aux messages sortants.

Sur le serveur, les limites de messages gRPC `AddGrpc`peuvent être configurées pour tous les services d’une application avec :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Les limites peuvent également être `AddServiceOptions<TService>`configurées pour un service individuel à l’aide de . Pour plus d’informations sur la configuration des limites de taille des messages, voir [la configuration gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Validation du certificat client

[Les certificats clients](https://tools.ietf.org/html/rfc5246#section-7.4.4) sont initialement validés lorsque la connexion est établie. Par défaut, Kestrel n’effectue pas de validation supplémentaire du certificat client d’une connexion.

Nous recommandons que les services gRPC sécurisés par les certificats clients utilisent le paquet [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth) ASP.NET l’authentification de certification de base effectuera une validation supplémentaire sur un certificat client, notamment :

* Le certificat a une utilisation de clé étendue valide (EKU)
* Est dans sa période de validité
* Vérifier la révocation du certificat
