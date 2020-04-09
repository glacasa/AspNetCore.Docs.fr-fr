---
title: Comparer les services gRPC avec les API HTTP
author: jamesnk
description: Découvrez comment gRPC se compare aux API HTTP et quels sont les scénarios qu’il recommande.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405872"
---
# <a name="compare-grpc-services-with-http-apis"></a>Comparer les services gRPC avec les API HTTP

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article explique comment [les services gRPC](https://grpc.io/docs/guides/) se comparent aux API HTTP (y compris ASP.NET [API Web Core](xref:web-api/index)). La technologie utilisée pour fournir une API pour votre application est un choix important, et gRPC offre des avantages uniques par rapport aux API HTTP. Cet article traite des forces et des faiblesses du gRPC et recommande des scénarios pour l’utilisation de gRPC sur d’autres technologies.

## <a name="high-level-comparison"></a>Comparaison de haut niveau

Le tableau suivant offre une comparaison de haut niveau des fonctionnalités entre les API GRPC et HTTP avec JSON.

| Fonctionnalité          | gRPC                                               | HTTP APIs avec JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contrat         | Requis (*.proto*)                                | Option (OpenAPI)            |
| Protocol         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (petit, binaire)](#performance)           | JSON (grand, lisible humaine)  |
| La prescription | [Spécification stricte](#strict-specification)      | Lâche. Tout HTTP est valide.     |
| Diffusion en continu        | [Client, serveur, bidirectionnel](#streaming)       | Client, serveur                |
| Prise en charge des navigateurs  | [Non (nécessite grpc-web)](#limited-browser-support) | Oui                           |
| Sécurité         | Transport (TLS)                                    | Transport (TLS)               |
| Génération de code client | [Oui](#code-generation)                      | OpenAPI et outillage tiers |

## <a name="grpc-strengths"></a>points forts de gRPC

### <a name="performance"></a>Performances

les messages gRPC sont sérialisés à l’aide [de Protobuf](https://developers.google.com/protocol-buffers/docs/overview), un format de message binaire efficace. Protobuf sérialis très rapidement sur le serveur et le client. La sérialisation de Protobuf se traduit par de petites charges utiles de messages, importantes dans des scénarios de bande passante limités comme les applications mobiles.

gRPC est conçu pour HTTP/2, une révision majeure de HTTP qui offre des avantages de performance significatifs sur HTTP 1.x:

* Cadrage et compression binaires. Le protocole HTTP/2 est compact et efficace à la fois dans l’envoi et la réception.
* Multiplexing de plusieurs appels HTTP/2 sur une seule connexion TCP. Le multiplexe élimine [le blocage de la tête de la ligne.](https://en.wikipedia.org/wiki/Head-of-line_blocking)

### <a name="code-generation"></a>Génération de code

Tous les cadres gRPC fournissent un support de première classe pour la génération de code. Un fichier de base pour le développement gRPC est le [fichier .proto](https://developers.google.com/protocol-buffers/docs/proto3), qui définit le contrat des services et des messages gRPC. À partir de ce fichier, les cadres gRPC coderont générer une classe de base de service, des messages et un client complet.

En partageant le fichier *.proto* entre le serveur et le client, les messages et le code client peuvent être générés de bout en bout. La génération de code du client élimine la duplication des messages sur le client et le serveur, et crée un client fortement typé pour vous. Le fait de ne pas avoir à écrire un client permet d’économiser beaucoup de temps de développement dans les applications avec de nombreux services.

### <a name="strict-specification"></a>Spécification stricte

Il n’existe pas de spécifications formelles pour HTTP API avec JSON. Les développeurs débattent du meilleur format d’URL, de verbes HTTP et de codes de réponse.

La [spécification gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) est normative sur le format qu’un service gRPC doit suivre. gRPC élimine le débat et permet aux développeurs d’économiser du temps parce que le gRPC est cohérent sur toutes les plateformes et les implémentations.

### <a name="streaming"></a>Diffusion en continu

HTTP/2 fournit une base pour les flux de communication en temps réel et à longue durée de vie. gRPC fournit un support de première classe pour le streaming via HTTP/2.

Un service gRPC prend en charge toutes les combinaisons de streaming :

* Unary (pas de streaming)
* Serveur au streaming client
* Flux de serveur client à serveur
* Streaming bidirectionnel

### <a name="deadlinetimeouts-and-cancellation"></a>Délais/temps d’arrêt et annulation

gRPC permet aux clients de spécifier combien de temps ils sont prêts à attendre qu’un CPR soit terminé. La [date limite](https://grpc.io/blog/deadlines) est envoyée au serveur, et le serveur peut décider quelles mesures prendre si elle dépasse la date limite. Par exemple, le serveur peut annuler les demandes en cours de gRPC/HTTP/database sur le délai d’attente.

La propagation de la date limite et l’annulation par le biais d’appels gRPC pour enfants permet d’imposer des limites d’utilisation des ressources.

## <a name="grpc-recommended-scenarios"></a>gRPC scénarios recommandés

gRPC est bien adapté aux scénarios suivants :

* **Microservices** &ndash; gRPC est conçu pour une faible latence et une communication à haut débit. gRPC est idéal pour les microservices légers où l’efficacité est critique.
* **Point-à-point de communication** &ndash; en temps réel gRPC a un excellent soutien pour le streaming bidirectionnel. les services gRPC peuvent pousser des messages en temps réel sans sondage.
* **Polyglot environnements** &ndash; d’outillage gRPC prend en charge toutes les langues de développement populaires, ce qui rend gRPC un bon choix pour les environnements multi-langues.
* **Les environnements de** &ndash; réseau contraints gRPC messages sont sérialisés avec Protobuf, un format de message léger. Un message gRPC est toujours plus petit qu’un message JSON équivalent.

## <a name="grpc-weaknesses"></a>faiblesses gRPC

### <a name="limited-browser-support"></a>Soutien limité du navigateur

Il est impossible d’appeler directement un service gRPC à partir d’un navigateur aujourd’hui. gRPC utilise fortement les fonctionnalités HTTP/2 et aucun navigateur ne fournit le niveau de contrôle requis sur les demandes Web pour prendre en charge un client gRPC. Par exemple, les navigateurs ne permettent pas à un appelant d’exiger que HTTP/2 soit utilisé, ou de fournir l’accès à des images http://http/2 sous-jacentes.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) est une technologie supplémentaire de l’équipe gRPC qui fournit un support GRPC limité dans le navigateur. gRPC-Web se compose de deux parties : un client JavaScript qui prend en charge tous les navigateurs modernes, et un proxy gRPC-Web sur le serveur. Le client gRPC-Web appelle le proxy et le proxy se transmettra sur les demandes de gRPC au serveur gRPC.

Toutes les fonctionnalités de gRPC ne sont pas prises en charge par gRPC-Web. Le streaming client et bidirectionnel n’est pas pris en charge, et il y a un soutien limité pour le streaming serveur.

> [!TIP]
> .NET Core a un support expérimental pour gRPC-Web. Visitez <xref:grpc/browser> pour plus d’informations.

### <a name="not-human-readable"></a>Pas lisible par l’homme

Les demandes d’API HTTP sont envoyées sous forme de texte et peuvent être lues et créées par des humains.

les messages gRPC sont codés avec Protobuf par défaut. Bien que Protobuf soit efficace à envoyer et à recevoir, son format binaire n’est pas lisible par l’homme. Protobuf nécessite la description d’interface du message spécifiée dans le fichier *.proto* pour déséialiser correctement. Une outillage supplémentaire est nécessaire pour analyser les charges utiles Protobuf sur le fil et pour composer les demandes à la main.

Des fonctionnalités telles que la [réflexion serveur](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) et l’outil [de ligne de commande gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existent pour aider avec les messages binaires Protobuf. En outre, les messages Protobuf prennent en charge [la conversion à et à partir de JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). La conversion intégrée de JSON fournit un moyen efficace de convertir les messages Protobuf à et depuis la forme lisible humaine lors du débogage.

## <a name="alternative-framework-scenarios"></a>Scénarios-cadres alternatifs

D’autres cadres sont recommandés sur gRPC dans les scénarios suivants :

* **Navigateur accessible APIs** &ndash; gRPC n’est pas entièrement pris en charge dans le navigateur. gRPC-Web peut offrir un support de navigateur, mais il a des limites et introduit un proxy serveur.
* **La diffusion de la communication** &ndash; en temps réel gRPC prend en charge la communication en temps réel via le streaming, mais le concept de diffusion d’un message sur les connexions enregistrées n’existe pas. Par exemple, dans un scénario de salle de chat où de nouveaux messages de chat doivent être envoyés à tous les clients de la salle de chat, chaque appel gRPC est nécessaire pour diffuser individuellement de nouveaux messages de chat au client. [SignalR](xref:signalr/introduction)est un cadre utile pour ce scénario. SignalRa le concept de connexions persistantes et de soutien intégré pour la diffusion de messages.
* **Communication interprofessionnelle** &ndash; Un processus doit héberger un serveur HTTP/2 pour accepter les appels gRPC entrants. Pour Windows, les tuyaux de communication [interprofessionnels](/dotnet/standard/io/pipe-operations) sont une méthode de communication rapide et légère.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
