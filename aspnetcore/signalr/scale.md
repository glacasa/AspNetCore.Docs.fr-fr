---
title: ASP.NET Core SignalR de l’hébergement et de la mise à l’échelle de production
author: bradygaster
description: Découvrez comment éviter les problèmes de performances et de mise à l’échelle dans SignalRles applications qui utilisent ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 23ac2b1c80b9d73d6e9ac57f0ef774ac2ea54be4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775074"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core de l’hébergement et de la mise à l’échelle Signalr

Par [Andrew Stanton-infirmière](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)et [Tom Dykstra](https://github.com/tdykstra),

Cet article explique les considérations relatives à l’hébergement et à la mise à l’échelle pour les applications à fort trafic qui utilisent ASP.NET Core Signalr.

## <a name="sticky-sessions"></a>Sessions rémanentes

Signalr exige que toutes les requêtes HTTP pour une connexion spécifique soient gérées par le même processus serveur. Lorsque Signalr est en cours d’exécution sur une batterie de serveurs (plusieurs serveurs), vous devez utiliser des « sessions rémanentes ». Les « sessions rémanentes » sont également appelées « affinité de session » par certains équilibrages de charge. Azure App Service utilise [application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) pour acheminer les demandes. L’activation du paramètre « affinité ARR » dans votre Azure App Service permet d’activer les « sessions rémanentes ». Les sessions rémanentes ne sont pas nécessaires dans les cas suivants :

1. En cas d’hébergement sur un seul serveur, dans un seul processus.
1. Lors de l’utilisation du service Azure Signalr.
1. Lorsque tous les clients sont configurés pour utiliser **uniquement** les WebSockets, **et** que le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client.

Dans toutes les autres circonstances (y compris lorsque le fond de panier ReDim est utilisé), l’environnement du serveur doit être configuré pour les sessions rémanentes.

Pour obtenir des conseils sur la configuration de Azure App Service pour Signalr, consultez <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>Ressources de connexion TCP

Le nombre de connexions TCP simultanées qu’un serveur Web peut prendre en charge est limité. Les clients HTTP standard utilisent des connexions *éphémères* . Ces connexions peuvent être fermées lorsque le client devient inactif et rouvert ultérieurement. En revanche, une connexion Signalr est *persistante*. Les connexions signalr restent ouvertes même lorsque le client devient inactif. Dans une application à trafic élevé qui dessert de nombreux clients, ces connexions persistantes peuvent entraîner l’atteinte du nombre maximal de connexions des serveurs.

Les connexions persistantes consomment également de la mémoire supplémentaire pour effectuer le suivi de chaque connexion.

L’utilisation intensive des ressources liées à la connexion par Signalr peut affecter les autres applications Web qui sont hébergées sur le même serveur. Lorsque Signalr s’ouvre et contient les dernières connexions TCP disponibles, les autres applications Web sur le même serveur n’ont pas non plus de connexions disponibles.

Si un serveur est à court de connexions, vous verrez des erreurs de socket aléatoires et des erreurs de réinitialisation de la connexion. Par exemple :

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Pour empêcher l’utilisation des ressources Signalr de provoquer des erreurs dans d’autres applications Web, exécutez Signalr sur des serveurs différents de ceux des autres applications Web.

Pour empêcher l’utilisation des ressources Signalr de provoquer des erreurs dans une application Signalr, augmentez la taille des instances pour limiter le nombre de connexions qu’un serveur doit gérer.

## <a name="scale-out"></a>Scale-out

Une application qui utilise Signalr doit assurer le suivi de toutes ses connexions, ce qui crée des problèmes pour une batterie de serveurs. Ajoutez un serveur et il obtient les nouvelles connexions que les autres serveurs ne connaissent pas. Par exemple, Signalr sur chaque serveur dans le diagramme suivant n’a pas connaissance des connexions sur les autres serveurs. Lorsque Signalr sur l’un des serveurs souhaite envoyer un message à tous les clients, le message est envoyé uniquement aux clients connectés à ce serveur.

![Mise à l’échelle de Signalr sans fond de panier](scale/_static/scale-no-backplane.png)

Les options pour résoudre ce problème sont le [service Azure signalr](#azure-signalr-service) et le [fond de panier ReDim](#redis-backplane).

## <a name="azure-signalr-service"></a>Service Azure SignalR

Le service Azure Signalr est un proxy plutôt qu’un fond de panier. Chaque fois qu’un client initie une connexion au serveur, le client est redirigé pour se connecter au service. Ce processus est illustré dans le diagramme suivant :

![Établissement d’une connexion au service Azure Signalr](scale/_static/azure-signalr-service-one-connection.png)

Le résultat est que le service gère toutes les connexions clientes, alors que chaque serveur n’a besoin que d’un petit nombre constant de connexions au service, comme indiqué dans le diagramme suivant :

![Clients connectés au service, serveurs connectés au service](scale/_static/azure-signalr-service-multiple-connections.png)

Cette approche de la montée en puissance parallèle présente plusieurs avantages par rapport au fond de panier ReDim :

* Les sessions rémanentes, également appelées « [affinité client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», ne sont pas nécessaires, car les clients sont immédiatement redirigés vers le service signalr Azure lorsqu’ils se connectent.
* Une application Signalr peut évoluer en fonction du nombre de messages envoyés, tandis que le service Azure Signalr est automatiquement mis à l’échelle pour gérer un nombre quelconque de connexions. Par exemple, il peut y avoir des milliers de clients, mais si seuls quelques messages par seconde sont envoyés, l’application Signalr n’a pas besoin d’effectuer une montée en charge sur plusieurs serveurs uniquement pour gérer les connexions elles-mêmes.
* Une application Signalr n’utilise pas beaucoup plus de ressources de connexion qu’une application Web sans Signalr.

Pour ces raisons, nous recommandons le service Azure Signalr pour toutes les applications ASP.NET Core Signalr hébergées sur Azure, y compris les App Service, les machines virtuelles et les conteneurs.

Pour plus d’informations, consultez la [documentation relative au service Azure signalr](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Fond de panier Redis

[Redims](https://redis.io/) est un magasin de valeurs de clé en mémoire qui prend en charge un système de messagerie avec un modèle de publication/abonnement. Le fond de panier ReDim de Signalr utilise la fonctionnalité Pub/Sub pour transférer les messages vers d’autres serveurs. Lorsqu’un client établit une connexion, les informations de connexion sont transmises au fond de panier. Lorsqu’un serveur souhaite envoyer un message à tous les clients, il envoie au fond de panier. Le fond de panier connaît tous les clients connectés et les serveurs sur lesquels ils se trouvent. Elle envoie le message à tous les clients par le biais de leurs serveurs respectifs. Ce processus est illustré dans le diagramme suivant :

![Le fond de panier ReDim, message envoyé d’un serveur à tous les clients](scale/_static/redis-backplane.png)

Le fond de panier ReDim est l’approche recommandée pour la montée en puissance parallèle pour les applications hébergées dans votre propre infrastructure. En cas de latence de connexion importante entre votre centre de données et un centre de données Azure, le service Signalr Azure peut ne pas être une option pratique pour les applications locales avec des exigences de faible latence ou de débit élevé.

Les avantages du service Azure Signalr mentionnés précédemment sont les inconvénients du fond de panier ReDim :

* Des sessions rémanentes, également appelées « [affinité du client](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)», sont nécessaires, sauf lorsque les **deux** conditions suivantes sont réunies :
  * Tous les clients sont configurés pour utiliser **uniquement** les WebSockets.
  * Le [paramètre SkipNegotiation](xref:signalr/configuration#configure-additional-options) est activé dans la configuration du client. 
   Une fois qu’une connexion est établie sur un serveur, la connexion doit rester sur ce serveur.
* Une SignalR application doit être montée en charge en fonction du nombre de clients, même si peu de messages sont envoyés.
* Une SignalR application utilise beaucoup plus de ressources de connexion qu’une application SignalRWeb sans.

## <a name="iis-limitations-on-windows-client-os"></a>Limitations IIS sur le système d’exploitation client Windows

Windows 10 et Windows 8. x sont des systèmes d’exploitation clients. IIS sur les systèmes d’exploitation clients est limité à 10 connexions simultanées. SignalRles connexions de sont :

* Transitoire et fréquemment rétabli.
* **N’est pas** supprimé immédiatement lorsqu’il n’est plus utilisé.

Les conditions précédentes permettent d’atteindre la limite de 10 connexions sur un système d’exploitation client. Lorsqu’un système d’exploitation client est utilisé pour le développement, nous vous recommandons les opérations suivantes :

* Évitez les services Internet.
* Utilisez Kestrel ou IIS Express comme cibles de déploiement.

## <a name="linux-with-nginx"></a>Linux avec Nginx

Définissez les en- `Connection` têtes `Upgrade` et du proxy sur les éléments SignalR suivants pour WebSocket :

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Pour plus d’informations, consultez [Nginx comme proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/).

## <a name="third-party-signalr-backplane-providers"></a>Fournisseurs de SignalR fond de panier tiers

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orléans](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Documentation SignalR du service Azure](/azure/azure-signalr/signalr-overview)
* [Configurer un backplane ReDim](xref:signalr/redis-backplane)
