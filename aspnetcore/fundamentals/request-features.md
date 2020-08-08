---
title: Fonctionnalités de requête dans ASP.NET Core
author: ardalis
description: Découvrez les détails d’implémentation d’un serveur web relatifs aux requêtes et réponses HTTP qui sont définies dans les interfaces pour ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: 4b6b34724bc848578293e586da12ead63c96cfbd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016789"
---
# <a name="request-features-in-aspnet-core"></a>Fonctionnalités de requête dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les détails d’implémentation d’un serveur web relatifs aux requêtes HTTP et aux réponses sont définis dans les interfaces. Ces interfaces sont utilisées par les implémentations de serveur et les intergiciels (middleware) pour créer et modifier le pipeline d’hébergement de l’application.

## <a name="feature-interfaces"></a>Interfaces de fonctionnalités

ASP.NET Core définit un certain nombre d’interfaces de fonctionnalités HTTP dans `Microsoft.AspNetCore.Http.Features`. Ces interfaces sont utilisées par les serveurs pour identifier les fonctionnalités prises en charge. Les interfaces de fonctionnalités suivantes gèrent les requêtes et retournent des réponses :

`IHttpRequestFeature` Définit la structure d’une requête HTTP, notamment le protocole, le chemin, la chaîne de requête, les en-têtes et le corps.

`IHttpResponseFeature` Définit la structure d’une réponse HTTP, notamment le code d’état, les en-têtes et le corps de la réponse.

`IHttpAuthenticationFeature` Définit la prise en charge de l’identification des utilisateurs basée sur un `ClaimsPrincipal` et de la spécification d’un gestionnaire d’authentification.

`IHttpUpgradeFeature` Définit la prise en charge des [mises à niveau HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), qui permettent au client de spécifier des protocoles supplémentaires qu’il veut utiliser si le serveur souhaite changer de protocole.

`IHttpBufferingFeature` Définit les méthodes permettant de désactiver la mise en mémoire tampon des requêtes et/ou des réponses.

`IHttpConnectionFeature` Définit les propriétés des adresses et ports locaux et distants.

`IHttpRequestLifetimeFeature` Définit la prise en charge de l’abandon de connexions, ou de la détection de l’arrêt prématuré d’une requête, par exemple en raison de la déconnexion d’un client.

`IHttpSendFileFeature` Définit une méthode d’envoi de fichiers de façon asynchrone.

`IHttpWebSocketFeature` Définit une API pour la prise en charge de sockets web.

`IHttpRequestIdentifierFeature` Ajoute une propriété qui peut être implémentée pour identifier de façon unique des requêtes.

`ISessionFeature` Définit les abstractions `ISessionFactory` et `ISession` pour prendre en charge les sessions utilisateur.

`ITlsConnectionFeature` Définit une API pour la récupération de certificats clients.

`ITlsTokenBindingFeature` Définit des méthodes permettant d’utiliser des paramètres de liaison de jeton TLS.

> [!NOTE]
> `ISessionFeature` n’est pas une fonctionnalité de serveur, mais est implémentée par `SessionMiddleware` (consultez [Gestion de l’état de l’application](app-state.md)).

## <a name="feature-collections"></a>Collections de fonctionnalités

La propriété `Features` de `HttpContext` fournit une interface permettant d’obtenir et de définir les fonctionnalités HTTP disponibles pour la requête actuelle. Étant donné que la collection de fonctionnalités est mutable même dans le contexte d’une requête, il est possible d’utiliser un intergiciel (middleware) pour modifier la collection et ajouter la prise en charge de fonctionnalités supplémentaires.

## <a name="middleware-and-request-features"></a>Intergiciel (middleware) et fonctionnalités de requête

Alors que les serveurs sont chargés de créer la collection de fonctionnalités, l’intergiciel peut aussi bien ajouter des fonctionnalités à cette collection et consommer des fonctionnalités de celle-ci. Par exemple, `StaticFileMiddleware` accède à la fonctionnalité `IHttpSendFileFeature`. Si la fonctionnalité existe, elle est utilisée pour envoyer le fichier statique demandé à partir de son chemin physique. Sinon, une autre méthode plus lente est utilisée pour envoyer le fichier. Quand l’interface `IHttpSendFileFeature` est disponible, elle permet au système d’exploitation d’ouvrir le fichier et d’effectuer une copie directe en mode noyau sur la carte réseau.

De plus, l’intergiciel peut effectuer des ajouts à la collection de fonctionnalités établie par le serveur. Il est même possible de remplacer des fonctionnalités existantes par un intergiciel, ce qui permet à ce dernier d’augmenter les fonctionnalités du serveur. Les fonctionnalités ajoutées à la collection sont disponibles immédiatement pour les autres intergiciels ou l’application sous-jacente elle-même plus loin dans le pipeline de requête.

La combinaison d’implémentations de serveurs personnalisées et d’améliorations spécifiques de l’intergiciel permet de construire l’ensemble précis de fonctionnalités qu’exige une application. Cela permet d’ajouter les fonctionnalités manquantes sans nécessiter un changement de serveur, et de s’assurer que seule la quantité minimale de fonctionnalités sont exposées, ce qui limite la zone de surface d’attaque et améliore les performances.

## <a name="summary"></a>Résumé

Les interfaces de fonctionnalités définissent des fonctionnalités HTTP spécifiques qu’une requête donnée peut prendre en charge. Les serveurs définissent des collections de fonctionnalités et l’ensemble initial de fonctionnalités prises en charge par ce serveur, mais il est possible d’utiliser un intergiciel pour améliorer ces fonctionnalités.

## <a name="additional-resources"></a>Ressources complémentaires

* [Serveurs](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin)
