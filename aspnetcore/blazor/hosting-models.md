---
title: ASP.NET modèles d’hébergement de base Blazor
author: guardrex
description: Comprendre Blazor les modèles Blazor d’hébergement WebAssembly et Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471832"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.NET modèles d’hébergement de base Blazor

Par [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazorest un cadre web conçu pour exécuter le côté client dans le navigateur sur un [WebAssembly-basé](https://webassembly.org/).NET runtime* Blazor *(* Blazor WebAssembly*) ou côté serveur dans ASP.NET Core ( Server ). Quel que soit le modèle d’hébergement, les modèles d’applications et de composants *sont les mêmes*.

Pour créer un projet pour les modèles <xref:blazor/get-started>d’hébergement décrits dans cet article, voir .

Pour une configuration <xref:blazor/hosting-model-configuration>avancée, voir .

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly (en)

Le modèle d’hébergement principal pour Blazor est l’exécution du côté client dans le navigateur sur WebAssembly. L’application, Blazor ses dépendances et le temps d’exécution .NET sont téléchargés sur le navigateur. L’application est exécutée directement sur le thread d’interface utilisateur du navigateur. Les mises à jour de l’interface utilisateur et le traitement des événements se produisent dans le même processus. Les actifs de l’application sont déployés sous forme de fichiers statiques sur un serveur web ou un service capable de servir du contenu statique aux clients.

![BlazorWebAssembly: Blazor L’application s’exécute sur un thread d’interface utilisateur à l’intérieur du navigateur.](hosting-models/_static/blazor-webassembly.png)

Pour créer Blazor une application à l’aide du modèle d’hébergement côté client, utilisez le ** Blazor modèle WebAssembly App** [(dotnet nouveau blazorwasm](/dotnet/core/tools/dotnet-new)).

Après avoir sélectionné le **ASP.NET Core hosted** ** Blazor modèle WebAssembly App,** vous avez la possibilité de configurer l’application pour utiliser un backend ASP.NET Core en sélectionnant la case à cocher hébergée ASP.NET Core[(dotnet nouveau blazorwasm --hébergé](/dotnet/core/tools/dotnet-new)). L’application ASP.NET Core sert Blazor l’application aux clients. L’application Blazor WebAssembly peut interagir avec le serveur sur [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor-webassembly>le réseau à l’aide d’appels Web API ou (

Les modèles incluent `blazor.webassembly.js` le script qui gère :

* Téléchargement de l’heure d’exécution .NET, l’application, et les dépendances de l’application.
* Initialisation de l’exécution de l’application.

Le Blazor modèle d’hébergement WebAssembly offre plusieurs avantages :

* Il n’y a pas de dépendance .NET côté serveur. L’application fonctionne pleinement après avoir été téléchargée sur le client.
* Les ressources et les capacités des clients sont pleinement exploitées.
* Le travail est déchargé du serveur au client.
* Un serveur web ASP.NET Core n’est pas nécessaire pour héberger l’application. Des scénarios de déploiement sans serveur sont possibles (par exemple, au service de l’application à partir d’un CDN).

Il ya des Blazor inconvénients à l’hébergement WebAssembly:

* L’application est limitée aux capacités du navigateur.
* Le matériel et les logiciels clients compétents (par exemple, le support WebAssembly) sont nécessaires.
* La taille du téléchargement est plus grande, et les applications prennent plus de temps à charger.
* .NET runtime et le support d’outillage est moins mature. Par exemple, il existe des limites dans le support et le débogage [.NET Standard.](/dotnet/standard/net-standard)

Le Blazor modèle d’application Hébergé prend en charge [les conteneurs Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Cliquez à droite sur le projet Server dans Visual Studio et sélectionnez **Add** > **Docker Support**.

## <a name="opno-locblazor-server"></a>BlazorServeur

Avec Blazor le modèle d’hébergement Server, l’application est exécutée sur le serveur à partir d’une application ASP.NET Core. Les mises à jour de l’interface utilisateur, [SignalR](xref:signalr/introduction) la gestion des événements et les appels JavaScript sont traités sur une connexion.

![Le navigateur interagit avec l’application (hébergée à l’intérieur SignalR d’une application ASP.NET Core) sur le serveur sur une connexion.](hosting-models/_static/blazor-server.png)

Pour créer Blazor une Blazor application à l’aide du modèle d’hébergement Server, utilisez le modèle ASP.NET Core ** Blazor Server App** [(dotnet nouveau blazorserver](/dotnet/core/tools/dotnet-new)). L’application ASP.NET Core héberge Blazor l’application SignalR Server et crée le point de terminaison où les clients se connectent.

L’application ASP.NET Core fait référence `Startup` à la classe de l’application pour ajouter :

* Services côté serveur.
* L’application au pipeline de manutention de la demande.

Le `blazor.server.js` script établit la connexion client. Il incombe à l’application de persister et de restaurer l’état de l’application au besoin (par exemple, en cas de perte de connexion réseau). Le `blazor.server.js` script est servi à partir d’une ressource intégrée dans le cadre partagé ASP.NET Core.

Le Blazor modèle d’hébergement Server offre plusieurs avantages :

* La taille du téléchargement Blazor est significativement plus petite qu’une application WebAssembly, et l’application se charge beaucoup plus rapidement.
* L’application tire pleinement parti des capacités du serveur, y compris l’utilisation de toutes les API compatibles .NET Core.
* .NET Core sur le serveur est utilisé pour exécuter l’application, de sorte que l’outillage .NET existant, comme le débogage, fonctionne comme prévu.
* Les clients minces sont pris en charge. Par exemple, Blazor les applications Server fonctionnent avec des navigateurs qui ne prennent pas en charge WebAssembly et sur les périphériques à ressources limitées.
* La base de code .NET/CMD de l’application, y compris le code composant de l’application, n’est pas servie aux clients.

Il ya des Blazor inconvénients à l’hébergement Server:

* Il existe généralement une latence plus élevée. Chaque interaction utilisateur implique un saut réseau.
* Il n’y a pas de support hors ligne. Si la connexion client échoue, l’application cesse de fonctionner.
* L’évolutivité est difficile pour les applications avec de nombreux utilisateurs. Le serveur doit gérer plusieurs connexions client et gérer l’état du client.
* Un serveur ASP.NET Core est nécessaire pour servir l’application. Les scénarios de déploiement sans serveur ne sont pas possibles (par exemple, au service de l’application à partir d’un CDN).

Le Blazor modèle d’application Server prend en charge [les conteneurs Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Cliquez à droite sur le projet dans Visual Studio et sélectionnez **Add** > **Docker Support**.

### <a name="comparison-to-server-rendered-ui"></a>Comparaison avec l’interface utilisateur rendue par le serveur

Une façon Blazor de comprendre les applications Server est de comprendre comment il diffère des modèles traditionnels pour le rendu de l’interface utilisateur dans ASP.NET applications Core en utilisant des vues Razor ou Des pages Razor. Les deux modèles utilisent le langage Razor pour décrire le contenu HTML, mais ils diffèrent considérablement dans la façon dont la balisage est rendu.

Lorsqu’une page ou une vue Razor est rendue, chaque ligne de code Razor émet HTML sous forme de texte. Après le rendu, le serveur dispose de la page ou l’instance de vue, y compris tout état qui a été produit. Lorsqu’une autre demande de la page se produit, par exemple lorsque la validation du serveur échoue et que le résumé de validation s’affiche :

* La page entière est redevenue sur le texte HTML.
* La page est envoyée au client.

Une Blazor application est composée d’éléments réutilisables de l’interface utilisateur appelés *composants*. Un composant contient du code C, une balisage et d’autres composants. Lorsqu’un composant est Blazor rendu, produit un graphique des composants inclus semblable à un modèle d’objet de document HTML ou XML (DOM). Ce graphique comprend l’état des composants conservé dans les propriétés et les champs. Blazorévalue le graphique des composants pour produire une représentation binaire de la majoration. Le format binaire peut être :

* Transformé en texte HTML (pendant&dagger;le prérendering ).
* Utilisé pour mettre à jour efficacement la majoration lors du rendu régulier.

&dagger;*Prerendering* &ndash; Le composant Razor demandé est compilé sur le serveur en HTML statique et envoyé au client, où il est rendu à l’utilisateur. Une fois la connexion effectuée entre le client et le serveur, les éléments statiques préditables du composant sont remplacés par des éléments interactifs. Prerendering rend l’application plus sensible à l’utilisateur.

Une mise à Blazor jour de l’interface utilisateur est déclenchée par :

* Interaction utilisateur, comme la sélection d’un bouton.
* Déclencheurs d’applications, comme une minuterie.

Le graphique est rendu, et un *diff d’interface* utilisateur (différence) est calculé. Ce diff est le plus petit ensemble de modifications DOM nécessaires pour mettre à jour l’interface utilisateur sur le client. Le diff est envoyé au client dans un format binaire et appliqué par le navigateur.

Un composant est éliminé après que l’utilisateur navigue loin de lui sur le client. Pendant qu’un utilisateur interagit avec un composant, l’état du composant (services, ressources) doit être maintenu dans la mémoire du serveur. Étant donné que l’état de nombreux composants peut être maintenu par le serveur en même temps, l’épuisement de la mémoire est une préoccupation qui doit être abordée. Pour obtenir des conseils Blazor sur la façon d’écrire une <xref:security/blazor/server>application Server pour assurer la meilleure utilisation de la mémoire du serveur, voir .

### <a name="circuits"></a>Circuits

Une Blazor application Server est construite au-dessus de [ASP.NET Core SignalR ](xref:signalr/introduction). Chaque client communique au serveur sur SignalR une ou plusieurs connexions appelées *circuit*. Un circuit Blazorest l’abstraction des SignalR connexions qui peuvent tolérer des interruptions temporaires du réseau. Lorsqu’un Blazor client SignalR voit que la connexion est déconnectée, il SignalR tente de se reconnecter au serveur à l’aide d’une nouvelle connexion.

Chaque écran de navigateur (onglet navigateur ou iframe) connecté à une Blazor application Server utilise une SignalR connexion. Il s’agit d’une autre distinction importante par rapport aux applications typiques rendues par le serveur. Dans une application rendue par un serveur, l’ouverture de la même application dans plusieurs écrans de navigateur ne se traduit généralement pas par des demandes de ressources supplémentaires sur le serveur. Dans Blazor une application Server, chaque écran de navigateur nécessite un circuit distinct et des instances distinctes de l’état des composants pour être géré par le serveur.

Blazorenvisage de fermer un onglet de navigateur ou de naviguer vers une URL externe une résiliation *gracieuse.* En cas de résiliation gracieuse, le circuit et les ressources associées sont immédiatement libérés. Un client peut également se déconnecter non-gracieusement, par exemple en raison d’une interruption de réseau. BlazorServer stocke des circuits déconnectés pour un intervalle configurable pour permettre au client de se reconnecter.

BlazorServer permet au code de définir un *gestionnaire de circuit*, ce qui permet d’exécuter du code sur les modifications apportées à l’état du circuit d’un utilisateur. Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latence de l’assurance-chômage

La latence de l’interface utilisateur est le temps qu’il faut d’une action initiée au moment où l’interface utilisateur est mise à jour. De plus petites valeurs pour la latence de l’interface utilisateur sont impératives pour qu’une application se sente sensible à un utilisateur. Dans Blazor une application Server, chaque action est envoyée au serveur, traitée, et un diff d’interface utilisateur est renvoyé. Par conséquent, la latence de l’interface utilisateur est la somme de la latence du réseau et de la latence du serveur dans le traitement de l’action.

Pour une application d’entreprise limitée à un réseau privé d’entreprise, l’effet sur les perceptions des utilisateurs de la latence due à la latence du réseau est généralement imperceptible. Pour une application déployée sur Internet, la latence peut devenir perceptible pour les utilisateurs, en particulier si les utilisateurs sont largement distribués géographiquement.

L’utilisation de la mémoire peut également contribuer à la latence de l’application. L’utilisation accrue de la mémoire entraîne une collecte fréquente des ordures ou de la mémoire de l’acuisation au disque, qui dégradent les performances de l’application et, par conséquent, augmentent la latence de l’interface utilisateur. Pour plus d’informations, consultez <xref:security/blazor/server>.

BlazorLes applications serveur doivent être optimisées pour minimiser la latence de l’interface utilisateur en réduisant la latence du réseau et l’utilisation de la mémoire. Pour une approche de mesure de <xref:host-and-deploy/blazor/server#measure-network-latency>la latence réseau, voir . Pour plus SignalR d’informations sur et Blazor, voir:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Connexion au serveur

BlazorLes applications serveur SignalR nécessitent une connexion active au serveur. Si la connexion est perdue, l’application tente de se reconnecter au serveur. Tant que l’état du client est encore dans la mémoire, la session client reprend sans perdre d’état.

Une Blazor application Server précède en réponse à la première demande du client, qui met en place l’état de l’interface utilisateur sur le serveur. Lorsque le client tente SignalR de créer une connexion, le client doit se reconnecter au même serveur. BlazorLes applications serveur qui utilisent plus d’un SignalR serveur backend doivent implémenter des sessions *collantes* pour les connexions.

Nous vous recommandons d’utiliser le [service Azure SignalR ](/azure/azure-signalr) pour Blazor les applications Server. Le service permet d’intensifier Blazor une application Server à SignalR un grand nombre de connexions simultanées. Des sessions collantes sont SignalR activées pour le `ServerStickyMode` service Azure `Required`en définissant l’option du service ou la valeur de configuration à . Pour plus d’informations, consultez <xref:host-and-deploy/blazor/server#signalr-configuration>.

Lors de l’utilisation de l’IIS, les sessions collantes sont activées avec le routage de demande d’application. Pour plus d’informations, voir [HTTP Load Balancing à l’aide de l’application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
