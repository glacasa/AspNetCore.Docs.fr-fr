---
title: Contrôle de version des services gRPC
author: jamesnk
description: Apprenez à la version des services gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664114"
---
# <a name="versioning-grpc-services"></a>Contrôle de version des services gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

Les nouvelles fonctionnalités ajoutées à une application peuvent nécessiter des services gRPC fournis aux clients pour changer, parfois de manière inattendue et révolutionnaire. Lorsque les services gRPC changent :

* Il faut tenir compte de l’impact des changements sur les clients.
* Une stratégie de version pour appuyer les changements devrait être mise en œuvre.

## <a name="backwards-compatibility"></a>Compatibilité descendante

Le protocole gRPC est conçu pour soutenir les services qui changent au fil du temps. En général, les ajouts aux services et aux méthodes de gRPC ne sont pas révolutionnaires. Les modifications non-ruptures permettent aux clients existants de continuer à travailler sans modification. La modification ou la suppression des services gRPC brisent les changements. Lorsque les services gRPC ont des changements de rupture, les clients utilisant ce service doivent être mis à jour et redéployés.

L’apport de modifications non-révolutionnaires à un service présente un certain nombre d’avantages :

* Les clients existants continuent de fonctionner.
* Évite le travail consistant à informer les clients de briser les changements et à les mettre à jour.
* Une seule version du service doit être documentée et maintenue.

### <a name="non-breaking-changes"></a>Changements non cassants

Ces modifications ne sont pas cassants à un niveau de protocole gRPC et .NET niveau binaire.

* **Ajout d’un nouveau service**
* **Ajout d’une nouvelle méthode à un service**
* **Ajout d’un champ à un message de demande** - Champs ajoutés à un message de demande sont déséialisés avec la valeur par [défaut](https://developers.google.com/protocol-buffers/docs/proto3#default) sur le serveur lorsqu’il n’est pas défini. Pour être un changement non-rupture, le service doit réussir lorsque le nouveau domaine n’est pas défini par des clients plus âgés.
* **Ajout d’un champ à un message de réponse** - Champs ajoutés à un message de réponse sont déséialisés dans la collection de champs [inconnus](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) du message sur le client.
* **Ajout d’une valeur à un enum** - Enums sont sérialisés comme une valeur numérique. De nouvelles valeurs enum sont déséialisées sur le client à la valeur enum sans nom enum. Pour être un changement non-rupture, les clients plus âgés doivent fonctionner correctement lors de la réception de la nouvelle valeur enum.

### <a name="binary-breaking-changes"></a>Changements de rupture binaires

Les modifications suivantes ne sont pas rupture à un niveau de protocole gRPC, mais le client doit être mis à jour si elle se met à niveau vers le dernier contrat *.proto* ou client .NET assemblage. La compatibilité binaire est importante si vous prévoyez de publier une bibliothèque gRPC à NuGet.

* **Retrait d’un champ** - Les valeurs d’un champ supprimé sont déséialisées dans [les champs inconnus](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)d’un message. Il ne s’agit pas d’un protocole gRPC brisant le changement, mais le client doit être mis à jour si elle met à niveau vers le dernier contrat. Il est important qu’un numéro de champ supprimé ne soit pas accidentellement réutilisé à l’avenir. Pour s’assurer que cela ne se produise pas, spécifiez les numéros de champ supprimés et les noms sur le message à l’aide du mot clé [réservé](https://developers.google.com/protocol-buffers/docs/proto3#reserved) de Protobuf.
* **Renommer un message** - Les noms de messages ne sont généralement pas envoyés sur le réseau, donc ce n’est pas un protocole gRPC briser le changement. Le client devra être mis à jour s’il passe à niveau vers le dernier contrat. Une situation où les noms de messages **sont** envoyés sur le réseau est avec [tous](https://developers.google.com/protocol-buffers/docs/proto3#any) les champs, lorsque le nom du message est utilisé pour identifier le type de message.
* **Changer csharp_namespace** - Changer `csharp_namespace` l’espace de nom des types générés .NET. Il ne s’agit pas d’un protocole gRPC brisant le changement, mais le client doit être mis à jour si elle met à niveau vers le dernier contrat.

### <a name="protocol-breaking-changes"></a>Modifications de rupture du protocole

Les éléments suivants sont des changements de protocole et de rupture binaire :

* **Renommer un champ** - Avec le contenu Protobuf, les noms de champ ne sont utilisés que dans le code généré. Le numéro de champ est utilisé pour identifier les champs sur le réseau. Le changement de nom d’un champ n’est pas un changement de protocole pour Protobuf. Toutefois, si un serveur utilise du contenu JSON, le changement de champ est un changement de rupture.
* **Modification d’un type** de données sur le terrain - Changer le type de données d’un champ en un [type incompatible](https://developers.google.com/protocol-buffers/docs/proto3#updating) entraînera des erreurs lors du désétérialisation du message. Même si le nouveau type de données est compatible, il est probable que le client doit être mis à jour pour prendre en charge le nouveau type s’il passe à la dernière période.
* **Modification d’un numéro de champ** - Avec les charges utiles Protobuf, le numéro de champ est utilisé pour identifier les champs sur le réseau.
* **Renommer un paquet, un service ou une méthode** - gRPC utilise le nom du paquet, le nom de service et le nom de la méthode pour construire l’URL. Le client obtient un statut *UNIMPLEMENTED* du serveur.
* **Suppression d’un service ou d’une méthode** - Le client obtient un statut *UNIMPLEMENTED* du serveur lors de l’appel de la méthode supprimée.

### <a name="behavior-breaking-changes"></a>Changements de rupture de comportement

Lorsque vous modifiez les changements non-breaking, vous devez également vous demander si les clients plus âgés peuvent continuer à travailler avec le nouveau comportement de service. Par exemple, ajouter un nouveau champ à un message de demande :

* Le protocole ne brise-t-il pas le changement.
* Le retour d’un statut d’erreur sur le serveur si le nouveau champ n’est pas défini en fait un changement de rupture pour les anciens clients.

La compatibilité comportementale est déterminée par votre code spécifique à l’application.

## <a name="version-number-services"></a>Services de numéro de version

Les services doivent s’efforcer de rester compatibles avec les anciens clients. Éventuellement, les modifications apportées à votre application peuvent nécessiter des modifications de rupture. Briser les anciens clients et les forcer à être mis à jour avec votre service n’est pas une bonne expérience utilisateur. Une façon de maintenir la compatibilité à l’envers tout en effectuant des modifications de rupture est de publier plusieurs versions d’un service.

gRPC prend en charge un spécificateur [de paquet](https://developers.google.com/protocol-buffers/docs/proto3#packages) optionnel, qui fonctionne un peu comme un espace de nom .NET. En fait, `package` le sera utilisé comme l’espace de `option csharp_namespace` nom .NET généré pour les types générés .NET si n’est pas défini dans le fichier *.proto.* Le paquet peut être utilisé pour spécifier un numéro de version pour votre service et ses messages :

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Le nom du forfait est combiné avec le nom de service pour identifier une adresse de service. Une adresse de service permet d’héberger plusieurs versions d’un service côte à côte :

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Les implémentations du service versionné sont enregistrées en *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

L’inclusion d’un numéro de version dans le nom du paquet vous donne la possibilité de publier une version *v2* de votre service avec des modifications de rupture, tout en continuant à soutenir les clients plus âgés qui appellent la version *v1.* Une fois que les clients ont mis à jour pour utiliser le service *v2,* vous pouvez choisir de supprimer l’ancienne version. Lors de la planification de la publication de plusieurs versions d’un service:

* Évitez de briser les changements si cela est raisonnable.
* Ne mettez pas à jour le numéro de version à moins d’apporter des modifications de rupture.
* Mettez à jour le numéro de version lorsque vous effectuez des modifications de rupture.

La publication de plusieurs versions d’un service le double. Pour réduire les chevauchements, envisagez de déplacer la logique commerciale des implémentations de services vers un emplacement centralisé qui peut être réutilisé par les anciennes et nouvelles implémentations :

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Les services et les messages générés avec différents noms de paquets sont **différents types .NET**. Le déplacement de la logique d’entreprise vers un emplacement centralisé nécessite la cartographie des messages vers des types communs.
