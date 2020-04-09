---
title: Construire des applications Web progressives Blazor avec ASP.NET WebAssembly Core
author: guardrex
description: Découvrez comment créer Blazorune application Web progressive (PWA) basée sur la technologie qui utilise les fonctionnalités modernes du navigateur pour se comporter comme une application de bureau.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218945"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Construire des applications Web progressives avec ASP.NET Core Blazor WebAssembly

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Une application Web progressive (PWA) est une application à une page unique (SPA) qui utilise des API et des capacités modernes pour se comporter comme une application de bureau. Blazor WebAssembly est une plate-forme d’applications Web axée sur les normes, de sorte qu’elle peut utiliser n’importe quel navigateur API, y compris les API PWA nécessaires pour les fonctionnalités suivantes :

* Travailler hors ligne et charger instantanément, indépendamment de la vitesse du réseau.
* Exécution dans sa propre fenêtre d’application, pas seulement une fenêtre de navigateur.
* Lancement à partir du menu de démarrage du système d’exploitation de l’hôte, du dock ou de l’écran d’accueil.
* Recevoir des notifications push à partir d’un serveur backend, même si l’utilisateur n’utilise pas l’application.
* Mise à jour automatique en arrière-plan.

Le mot *progressif* est utilisé pour décrire ces applications parce que :

* Un utilisateur peut d’abord découvrir et utiliser l’application dans son navigateur Web comme n’importe quel autre SPA.
* Plus tard, l’utilisateur progresse à l’installer dans leur système d’exploitation et à activer les notifications push.

## <a name="create-a-project-from-the-pwa-template"></a>Créer un projet à partir du modèle PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Lors de la création d’une nouvelle **application Blazor WebAssembly** dans le dialogue **Créer un nouveau projet,** sélectionnez la case de cocher **Progress Web Application** :

![La case à cocher 'Progressive Web Application' est sélectionnée dans le nouveau dialogue de projet visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Créez un projet PWA dans `--pwa` une coque de commande avec le commutateur :

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

En option, PWA peut être configuré pour une application créée à partir du modèle hébergé ASP.NET Core. Le scénario PWA est indépendant du modèle d’hébergement.

## <a name="installation-and-app-manifest"></a>Manifeste d’installation et d’application

Lors de la visite d’une application créée à l’aide du modèle PWA, les utilisateurs ont la possibilité d’installer l’application dans le menu de démarrage, le dock ou l’écran d’accueil de leur système d’exploitation. La façon dont cette option est présentée dépend du navigateur de l’utilisateur. Lorsque vous utilisez des navigateurs basés sur le chrome de bureau, tels que Edge ou Chrome, un bouton **Add** apparaît dans la barre d’URL. Une fois que l’utilisateur sélectionne le bouton **Ajouter,** il reçoit un dialogue de confirmation :

![Le dialogue de confirmation dans Google Chrome présente un bouton Installer à l’utilisateur pour l’application 'MyBlazorPwa'.](progressive-web-app/_static/image2.png)

Sur iOS, les visiteurs peuvent installer le PWA à l’aide du bouton **Partager** de Safari et de son option **Add to Homescreen.** Sur Chrome pour Android, les utilisateurs doivent sélectionner le bouton **Menu** dans le coin supérieur droit, suivi de **Add to Home screen**.

Une fois installée, l’application apparaît dans sa propre fenêtre sans barre d’adresse :

![L’application 'MyBlazorPwa' s’exécute dans Google Chrome sans barre d’adresse.](progressive-web-app/_static/image3.png)

Pour personnaliser le titre, le schéma de couleurs, l’icône ou d’autres détails de la fenêtre, consultez le fichier *manifest.json* dans l’annuaire *wwwroot* du projet. Le schéma de ce fichier est défini par les normes web. Pour plus d’informations, voir [DOCs web MDN: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Soutien hors ligne

Par défaut, les applications créées à l’aide de l’option modèle PWA ont la prise en charge de l’exécution hors ligne. Un utilisateur doit d’abord visiter l’application pendant qu’il est en ligne. Le navigateur télécharge et cache automatiquement toutes les ressources nécessaires pour fonctionner hors connexion.

> [!IMPORTANT]
> Le soutien au développement interfèrerait avec le cycle de développement habituel d’apporter des changements et de les tester. Par conséquent, le support hors ligne n’est activé que pour les applications *publiées.* 

> [!WARNING]
> Si vous avez l’intention de distribuer un PWA hors ligne, il ya [plusieurs avertissements importants et mises en garde](#caveats-for-offline-pwas). Ces scénarios sont inhérents aux APS Blazorhors ligne et ne sont pas spécifiques à . Assurez-vous de lire et de comprendre ces mises en garde avant de faire des hypothèses sur la façon dont votre application hors connexion fonctionnera.

Pour voir comment fonctionne le support hors ligne :

1. Publiez l’application. Pour plus d’informations, consultez <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Déployez l’application sur un serveur qui prend en charge HTTPS et accédez à l’application dans un navigateur à son adresse HTTPS sécurisée.
1. Ouvrez les outils de développement du navigateur et vérifiez qu’un *travailleur de service* est inscrit à l’hôte sur **l’onglet Application** :

   ![L’onglet 'Application' des outils de développeur Google Chrome affiche un service Worker activé et en cours d’exécution.](progressive-web-app/_static/image4.png)

1. Rechargez la page et examinez **l’onglet Réseau.** **Le travailleur de service** ou le cache de **mémoire** sont répertoriés comme sources pour tous les actifs de la page :

   ![Google Chrome outils de développeur onglet 'Network' onglet montrant les sources pour tous les actifs de la page.](progressive-web-app/_static/image5.png)

1. Pour vérifier que le navigateur ne dépend pas non plus de l’accès au réseau pour charger l’application :

   * Arrêtez le serveur web et voyez comment l’application continue de fonctionner normalement, ce qui inclut les rechargements de page. De même, l’application continue de fonctionner normalement lorsqu’il y a une connexion réseau lente.
   * Instruire le navigateur pour simuler le mode hors connexion dans l’onglet **Réseau** :

   ![Google Chrome outils de développeur onglet 'Network' onglet avec le mode navigateur tomber en cours de changement de «en ligne» à «Offline».](progressive-web-app/_static/image6.png)

Le support hors ligne utilisant un travailleur de Blazorservice est une norme web, non spécifique à . Pour plus d’informations sur les travailleurs des services, consultez [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Pour en savoir plus sur les habitudes d’utilisation courantes pour les travailleurs du service, consultez [Google Web: The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Blazorle modèle PWA produit deux dossiers de travailleurs de service :

* *wwwroot/service-worker.js*, qui est utilisé pendant le développement.
* *wwwroot/service-worker.published.js*, qui est utilisé après la publication de l’application.

Pour partager la logique entre les deux dossiers des travailleurs de service, considérez l’approche suivante :

* Ajoutez un troisième fichier JavaScript pour tenir la logique commune.
* Utilisez [self.importScripts](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) pour charger la logique commune dans les deux fichiers des travailleurs de service.

### <a name="cache-first-fetch-strategy"></a>Stratégie Cache-first fetch

Le *travailleur-service intégré.published.js* service travailleur résout les demandes en utilisant une stratégie *cache-premier.* Cela signifie que le travailleur du service préfère retourner le contenu mis en cache, que l’utilisateur ait accès au réseau ou que du contenu plus récent soit disponible sur le serveur.

La stratégie cache-première est précieuse parce que :

* **Il assure la fiabilité.** &ndash;L’accès au réseau n’est pas un état boolean. Un utilisateur n’est pas simplement en ligne ou hors ligne :

  * L’appareil de l’utilisateur peut supposer qu’il est en ligne, mais le réseau peut être si lent qu’il n’est pas pratique à attendre.
  * Le réseau peut retourner des résultats invalides pour certaines URL, comme lorsqu’il existe un portail WIFI captif qui bloque ou redirige actuellement certaines demandes.
  
  C’est pourquoi l’API du `navigator.onLine` navigateur n’est pas fiable et ne devrait pas être dépendu.

* **Il assure la justesse.** &ndash;Lors de la construction d’une cache de ressources hors ligne, le travailleur du service utilise le hachage de contenu pour garantir qu’il a obtenu un instantané complet et auto-cohérent des ressources à un seul instant dans le temps. Ce cache est ensuite utilisé comme une unité atomique. Il est inutile de demander au réseau des ressources plus nouvelles, puisque les seules versions requises sont ceux déjà mis en cache. Tout le reste risque d’incohérence et d’incompatibilité (par exemple, essayer d’utiliser des versions d’assemblages .NET qui n’ont pas été compilés ensemble).

### <a name="background-updates"></a>Mises à jour de fond

En tant que modèle mental, vous pouvez penser à un PWA hors ligne comme se comportant comme une application mobile qui peut être installé. L’application démarre immédiatement indépendamment de la connectivité réseau, mais la logique de l’application installée provient d’un instantané point-dans-temps qui pourrait ne pas être la dernière version.

Le Blazor modèle PWA produit des applications qui essaient automatiquement de se mettre à jour en arrière-plan chaque fois que l’utilisateur visite et dispose d’une connexion réseau de travail. La façon dont cela fonctionne est la suivante:

* Au cours de la compilation, le projet génère un *actif de travailleur de service manifeste.* Par défaut, c’est ce *qu’on appelle service-travailleur-actifs.js*. Le manifeste répertorie toutes les ressources statiques dont l’application a besoin pour fonctionner hors connexion, telles que les assemblages .NET, les fichiers JavaScript et le CSS, y compris leurs hachages de contenu. La liste des ressources est chargée par le travailleur de service afin qu’il sache quelles ressources mettre en cache.
* Chaque fois que l’utilisateur visite l’application, le navigateur ré-demande *service-worker.js* et *service-travailleur-assets.js* en arrière-plan. Les fichiers sont comparés byte-for-byte avec le travailleur de service installé existant. Si le serveur retourne le contenu modifié pour l’un ou l’autre de ces fichiers, le travailleur du service tente d’installer une nouvelle version de lui-même.
* Lors de l’installation d’une nouvelle version de lui-même, le travailleur du service crée un nouveau cache distinct pour les ressources hors ligne et commence à remplir le cache avec des ressources répertoriées dans *service-travailleur-actifs.js*. Cette logique est `onInstall` mise en œuvre dans la fonction à l’intérieur *de service-worker.published.js*.
* Le processus se termine avec succès lorsque toutes les ressources sont chargées sans erreur et que tous les hashes de contenu correspondent. En cas de succès, le nouveau travailleur de service entre dans un état *d’activation en attente.* Dès que l’utilisateur ferme l’application (aucun onglet ou fenêtre d’application restant), le nouveau travailleur du service devient *actif* et est utilisé pour les visites ultérieures de l’application. L’ancien travailleur de service et son cache sont supprimés.
* Si le processus ne se termine pas avec succès, l’instance du nouveau travailleur de service est écartée. Le processus de mise à jour est tenté à nouveau sur la prochaine visite de l’utilisateur, quand j’espère que le client a une meilleure connexion réseau qui peut remplir les demandes.

Personnalisez ce processus en modifiant la logique des travailleurs du service. Aucun des comportements précédents Blazor n’est spécifique à, mais est simplement l’expérience par défaut fournie par l’option modèle PWA. Pour plus d’informations, voir [DOCs Web MDN: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Comment les demandes sont résolues

Tel que décrit dans la section [stratégie Cache-first fetch,](#cache-first-fetch-strategy) le travailleur du service par défaut utilise une stratégie *cache-première,* ce qui signifie qu’il essaie de servir du contenu mis en cache lorsqu’il est disponible. S’il n’y a pas de contenu mis en cache pour une certaine URL, par exemple lors de la demande de données d’une API backend, le travailleur du service revient sur une demande de réseau régulière. La demande de réseau réussit si le serveur est accessible. Cette logique est `onFetch` mise en œuvre à l’intérieur de la fonction au sein *de service-worker.published.js*.

Si les composants Razor de l’application s’appuient sur la demande de données des API backend et que vous souhaitez fournir une expérience utilisateur amicale pour les demandes échouées en raison de l’indisponibilité du réseau, implémentez la logique dans les composants de l’application. Par exemple, `try/catch` `HttpClient` utiliser autour des demandes.

### <a name="support-server-rendered-pages"></a>Prendre en charge les pages rendues par le serveur

Considérez ce qui se passe lorsque l’utilisateur navigue pour la première fois vers une URL telle que `/counter` ou tout autre lien profond dans l’application. Dans ces cas, vous ne souhaitez pas `/counter`retourner le contenu mis en cache comme `/index.html` , mais Blazor au lieu de cela besoin du navigateur pour charger le contenu mis en cache comme pour démarrer votre application WebAssembly. Ces demandes initiales sont connues sous le nom de demandes de *navigation,* au lieu de :

* *sous-résourcent les* demandes d’images, de feuilles de style ou d’autres fichiers.
* *demandes d’API par les utilisateurs de l’API/ XHR.*

Le travailleur du service par défaut contient une logique de cas spécial pour les demandes de navigation. Le travailleur du service résout les `/index.html`demandes en retournant le contenu mis en cache pour , indépendamment de l’URL demandée. Cette logique est `onFetch` mise en œuvre dans la fonction à l’intérieur *de service-worker.published.js*.

Si votre application dispose de certaines URL qui doivent retourner `/index.html` HTML rendu serveur, et ne pas servir à partir du cache, alors vous devez modifier la logique dans votre travailleur de service. Si toutes les `/Identity/` URL contenant doivent être traitées comme des demandes régulières en ligne uniquement sur le serveur, puis modifier *service-worker.published.js* `onFetch` logique. Recherchez le code suivant :

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Modifier le code pour les suivants :

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Si vous ne le faites pas, alors indépendamment de la connectivité réseau, le travailleur de `/index.html`service intercepte les demandes pour ces URL et les résout à l’aide .

### <a name="control-asset-caching"></a>Mise en cache d’actifs de contrôle

Si votre projet `ServiceWorkerAssetsManifest` définit la propriété BlazorMSBuild, « l’outillage de construction génère un actif de travailleur de service manifeste avec le nom spécifié. Le modèle PWA par défaut produit un fichier de projet contenant la propriété suivante :

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Le fichier est placé dans l’annuaire de sortie *wwwroot,* `/service-worker-assets.js`de sorte que le navigateur peut récupérer ce fichier en demandant . Pour voir le contenu de ce fichier, ouvrez */bin/Debug/TARGET FRAMEWORK/wwwroot/service-worker-assets.js* dans un éditeur de texte. Cependant, ne modifiez pas le fichier, car il est régénéré sur chaque version.

Par défaut, ce manifeste énumère :

* Toutes Blazorles ressources gérées, telles que les assemblages .NET et les fichiers .NET WebAssembly runtime nécessaires pour fonctionner hors ligne.
* Toutes les ressources pour la publication à l’annuaire *wwwroot* de l’application, telles que les images, les feuilles de style et les fichiers JavaScript, y compris les actifs Web statiques fournis par des projets externes et des forfaits NuGet.

Vous pouvez contrôler laquelle de ces ressources sont récupérées et mises `onInstall` en cache par le travailleur de service en éditant la logique dans *service-worker.published.js*. Par défaut, le travailleur de service récupère et cache des fichiers correspondant à des extensions typiques de nom de fichier Web Blazor tels que *.html*, *.css*, *.js*, et *.wasm*, plus les types de fichiers spécifiques à WebAssembly (*.dll*, *.pdb*).

Pour inclure des ressources supplémentaires qui ne sont pas présentes dans le répertoire `ItemGroup` *wwwroot* de l’application, définissez les entrées extra MSBuild, comme le montre l’exemple suivant :

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Les `AssetUrl` métadonnées spécifient l’URL de base relative que le navigateur doit utiliser lorsque vous allez chercher la ressource à cacher. Cela peut être indépendant de son nom de fichier source d’origine sur le disque.

> [!IMPORTANT]
> L’ajout d’un `ServiceWorkerAssetsManifestItem` n’entraîne pas la publication du fichier dans l’annuaire *wwwroot* de l’application. La sortie de publication doit être contrôlée séparément. La `ServiceWorkerAssetsManifestItem` seule cause l’apparition d’une entrée supplémentaire dans les biens des travailleurs de service.

## <a name="push-notifications"></a>Notifications Push

Comme tout autre PWA, un Blazor PWA WebAssembly peut recevoir des notifications push à partir d’un serveur backend. Le serveur peut envoyer des notifications push à tout moment, même lorsque l’utilisateur n’utilise pas activement l’application. Par exemple, des notifications push peuvent être envoyées lorsqu’un autre utilisateur effectue une action pertinente.

Le mécanisme d’envoi d’une Blazor notification push est entièrement indépendant de WebAssembly, car il est implémenté par le serveur backend qui peut utiliser n’importe quelle technologie. Si vous souhaitez envoyer des notifications push à partir d’un serveur ASP.NET Core, envisagez [d’utiliser une technique similaire à l’approche adoptée dans l’atelier Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Le mécanisme de réception et d’affichage d’une notification push sur le client est également indépendant de Blazor WebAssembly, car il est implémenté dans le fichier JavaScript du travailleur de service. Par exemple, voir [l’approche utilisée dans l’atelier Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Mises en garde pour les APR hors ligne

Toutes les applications ne devraient pas tenter de prendre en charge l’utilisation hors connexion. Le soutien hors ligne ajoute une complexité importante, tout en n’étant pas toujours pertinent pour les cas d’utilisation requis.

Le support hors ligne n’est généralement pertinent que :

* Si le principal magasin de données est local pour le navigateur. Par exemple, l’approche est pertinente dans une application avec une interface `localStorage` utilisateur pour un appareil [IoT](https://en.wikipedia.org/wiki/Internet_of_things) qui stocke les données dans ou [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Si l’application effectue une quantité importante de travail pour aller chercher et mettre en cache les données API backend pertinentes pour chaque utilisateur afin qu’ils puissent naviguer à travers les données hors ligne. Si l’application doit prendre en charge l’édition, un système de suivi des modifications et de synchronisation des données avec le backend doit être construit.
* Si l’objectif est de garantir que l’application se charge immédiatement indépendamment des conditions du réseau. Implémentez une expérience utilisateur appropriée autour des demandes d’API backend pour montrer l’avancement des demandes et se comporter gracieusement lorsque les demandes échouent en raison de l’indisponibilité du réseau.

En outre, les APE hors ligne doivent faire face à une série de complications supplémentaires. Les développeurs doivent se familiariser soigneusement avec les mises en garde dans les sections suivantes.

### <a name="offline-support-only-when-published"></a>Soutien hors ligne uniquement lorsqu’il est publié

Pendant le développement, vous voulez généralement voir chaque changement reflété immédiatement dans le navigateur sans passer par un processus de mise à jour d’arrière-plan. Par Blazorconséquent, le modèle PWA de 's ne permet le support hors ligne que lorsqu’il est publié.

Lors de la construction d’une application hors connexion, il ne suffit pas de tester l’application dans l’environnement développement. Vous devez tester l’application dans son état publié pour comprendre comment elle répond aux différentes conditions du réseau.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Mettre à jour l’achèvement après la navigation de l’utilisateur loin de l’application

Les mises à jour ne sont pas terminées tant que l’utilisateur n’a pas navigué loin de l’application dans tous les onglets. Comme expliqué dans la section [mises à jour De fond,](#background-updates) après avoir déployé une mise à jour de l’application, le navigateur récupère les fichiers des travailleurs de service mis à jour pour commencer le processus de mise à jour.

Ce qui surprend de nombreux développeurs, c’est que, même lorsque cette mise à jour se termine, elle ne prend **effet que** lorsque l’utilisateur n’a pas navigué loin dans tous les onglets. Il **ne** suffit pas de rafraîchir l’onglet affichant l’application, même si c’est le seul onglet affichant l’application. Jusqu’à ce que votre application soit complètement fermée, le nouveau travailleur de service reste dans *l’attente d’activer* l’état. **Ce n’est Blazorpas spécifique à , mais plutôt est un comportement de plate-forme web standard.**

Cela dérange généralement les développeurs qui essaient de tester les mises à jour de leur travailleur de service ou hors ligne mis en cache des ressources. Si vous vérifiez les outils de développeur du navigateur, vous pouvez voir quelque chose comme ce qui suit:

![L’onglet « Application » de Google Chrome montre que le serviceier de l’application « attend d’activer ».](progressive-web-app/_static/image7.png)

Tant que la liste des « clients », qui sont des onglets ou des fenêtres affichant votre application, n’est pas tentante, le travailleur continue d’attendre. La raison pour laquelle les travailleurs des services le font est de garantir l’uniformité. La cohérence signifie que toutes les ressources sont récupérées à partir de la même cache atomique.

Lorsque vous testez des modifications, vous pouvez trouver pratique de cliquer sur le lien "skipWaiting" tel qu’indiqué dans la capture d’écran précédente, puis recharger la page. Vous pouvez automatiser cela pour tous les utilisateurs en codant votre serviceur pour [sauter la phase «attente» et activer immédiatement sur la mise à jour](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Si vous sautez la phase d’attente, vous renoncez à la garantie que les ressources sont toujours récupérées de la même instance de cache.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Les utilisateurs peuvent exécuter n’importe quelle version historique de l’application

Les développeurs Web s’attendent habituellement à ce que les utilisateurs n’exécutent que la dernière version déployée de leur application web, puisque c’est normal dans le modèle de distribution Web traditionnel. Cependant, un PWA hors ligne d’abord est plus proche d’une application mobile native, où les utilisateurs ne sont pas nécessairement en cours d’exécution de la dernière version.

Comme expliqué dans la section [mises à jour De fond,](#background-updates) après avoir déployé une mise à jour de votre application, chaque utilisateur existant continue **d’utiliser une version précédente pour au moins une autre visite** parce que la mise à jour se produit en arrière-plan et n’est pas activée jusqu’à ce que l’utilisateur navigue par la suite. De plus, la version précédente utilisée n’est pas nécessairement la précédente que vous avez déployée. La version précédente peut être *n’importe quelle* version historique, selon le moment où l’utilisateur a terminé une dernière mise à jour.

Cela peut être un problème si les parties frontend et backend de votre app exigent un accord sur le schéma pour les demandes API. Vous ne devez pas déployer de modifications de schéma aPI rétrogrades jusqu’à ce que vous puissiez être sûr que tous les utilisateurs ont mis à niveau. Alternativement, empêcher les utilisateurs d’utiliser des versions plus anciennes incompatibles de l’application. Cette exigence de scénario est la même que pour les applications mobiles natives. Si vous déployez un changement de rupture dans les API serveur, l’application client est cassée pour les utilisateurs qui n’ont pas encore mis à jour.

Si possible, ne déployez pas de modifications de rupture dans vos API backend. Si vous devez le faire, envisagez d’utiliser [des API standard de travailleurs de service tels que ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) pour déterminer si l’application est à jour, et sinon, pour empêcher l’utilisation.

### <a name="interference-with-server-rendered-pages"></a>Interférence avec les pages rendues par le serveur

Tel que décrit dans la section [des pages rendues par le serveur support,](#support-server-rendered-pages) si vous souhaitez contourner le comportement du travailleur du service de retourner `/index.html` le contenu pour toutes les demandes de navigation, modifiez la logique dans votre service.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Tous les contenus manifestes de l’actif des travailleurs de service sont mis en cache par défaut

Tel que décrit dans la section [de mise en cache d’actifs de contrôle,](#control-asset-caching) le service de *fichiers-travailleur-assets.js* est généré pendant la construction et répertorie tous les actifs que le travailleur de service doit aller chercher et mettre en cache.

Étant donné que cette liste comprend par défaut tout ce qui est émis à *wwwroot*, y compris le contenu fourni par des paquets externes et des projets, vous devez faire attention de ne pas y mettre trop de contenu. Si le répertoire *wwwroot* contient des millions d’images, le travailleur de service essaie de les chercher et de les mettre en cache tous, consommant une bande passante excessive et très probablement ne pas terminer avec succès.

Mettre en œuvre une logique arbitraire pour contrôler quel sous-ensemble du `onInstall` contenu du manifeste doit être récupéré et mis en cache en modifiant la fonction dans *service-worker.published.js*.

### <a name="interaction-with-authentication"></a>Interaction avec l’authentification

Il est possible d’utiliser l’option modèle PWA en conjonction avec les options d’authentification. Un PWA hors connexion peut également prendre en charge l’authentification lorsque l’utilisateur a une connectivité réseau.

Lorsqu’un utilisateur n’a pas de connectivité réseau, il ne peut pas authentifier ou obtenir des jetons d’accès. Par défaut, tenter de visiter la page de connexion sans accès réseau se traduit par un message d'« erreur réseau ».

Vous devez concevoir un flux d’interface utilisateur qui permet à l’utilisateur de faire des choses utiles tout en hors ligne sans tenter d’authentifier ou d’obtenir des jetons d’accès. Alternativement, vous pouvez concevoir l’application pour échouer d’une manière gracieuse lorsque le réseau n’est pas disponible. Si cela n’est pas possible dans votre application, vous ne voudrez peut-être pas activer le support hors ligne.
