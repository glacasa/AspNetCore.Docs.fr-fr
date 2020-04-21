---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: Apprenez à déboiffer Blazor les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661607"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Debug ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorLes applications WebAssembly peuvent être déboisées à l’aide des outils de développement du navigateur dans les navigateurs basés sur le Chrome (Edge/Chrome).  Vous pouvez également déboiffer votre application à l’aide de Visual Studio ou de Visual Studio Code.

Les scénarios disponibles comprennent :

* Réglez et supprimez les points d’arrêt.
* Exécutez l’application avec un support de débogage dans Visual Studio et Visual Studio Code (support<kbd>F5).</kbd>
* Une seule étape (<kbd>F10</kbd>) à travers le code.
* Reprenez l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio Code.
* Dans l’affichage local, observez les valeurs des variables *locales.*
* Voir la pile d’appels, y compris les chaînes d’appels qui vont de JavaScript en .NET et de .NET à JavaScript.

Pour *l’instant,* vous ne pouvez pas :

* Inspectez les tableaux.
* Planer pour inspecter les membres.
* Débogé d’étape dans ou hors du code géré.
* Ayez un soutien complet pour inspecter les types de valeur.
* Cassez-vous sur des exceptions non manipulées.
* Hit points d’arrêt pendant le démarrage de l’application.
* Débogé d’une application avec un travailleur de service.

Nous continuerons d’améliorer l’expérience de débogage dans les prochaines versions.

## <a name="prerequisites"></a>Prérequis

Le débogage nécessite l’un ou l’autre des navigateurs suivants :

* Microsoft Edge (version 80 ou plus tard)
* Google Chrome (version 70 ou plus tard)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Activez le débogage pour Visual Studio et Visual Studio Code

Le débogage est activé automatiquement pour les nouveaux projets créés à l’aide Blazor du ASP.NET Core 3.2 Preview 3 ou plus tard du modèle de projet WebAssembly[(la version actuelle est de 3,2 Aperçu 4](xref:blazor/get-started)).

Pour permettre le débogage d’une application WebAssembly existante, Blazor mettez à jour le fichier *launchSettings.json* dans le projet de démarrage afin d’inclure la propriété suivante `inspectUri` dans chaque profil de lancement :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Une fois mis à jour, le fichier *launchSettings.json* doit ressembler à l’exemple suivant :

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La `inspectUri` propriété:

* Permet à l’IDE de Blazor détecter que l’application est une application WebAssembly.
* Instructe l’infrastructure de débogage de Blazorscript pour se connecter au navigateur par le proxy de débogage de '.

## <a name="visual-studio"></a>Visual Studio

Pour débomber Blazor une application WebAssembly dans Visual Studio :

1. Assurez-vous d’avoir [installé la dernière version préliminaire de Visual Studio 2019 16.6](https://visualstudio.com/preview) (Aperçu 2 ou plus tard).
1. Créez une nouvelle application WebAssembly hébergée Blazor ASP.NET Core.
1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débbugger.
1. Définissez un point d’arrêt `IncrementCount` dans *Counter.razor* dans la méthode.
1. Naviguez sur l’onglet **Compteur** et sélectionnez le bouton pour appuyer sur le point d’arrêt :

   ![Compteur Debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Découvrez la valeur `currentCount` du terrain dans la fenêtre locale:

   ![Voir les habitants](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Appuyez <kbd>sur F5</kbd> pour continuer l’exécution.

Tout en débogage de votre Blazor application WebAssembly, vous pouvez également déboguer votre code serveur :

1. Définissez un point d’arrêt dans `OnInitializedAsync`la page *FetchData.razor* dans .
1. Définissez un point `WeatherForecastController` d’arrêt dans la méthode d’action. `Get`
1. Naviguez sur l’onglet **Fetch Data** pour `FetchData` atteindre le premier point d’arrêt du composant juste avant qu’il n’émete une demande HTTP au serveur :

   ![Debug Fetch Données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Appuyez sur <kbd>F5</kbd> pour continuer l’exécution, `WeatherForecastController`puis frapper le point d’arrêt sur le serveur dans le :

   ![Serveur Debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Appuyez à nouveau sur <kbd>F5</kbd> pour que l’exécution se poursuive et que la table de prévision météorologique soit rendue.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Pour débomber Blazor une application WebAssembly dans Visual Studio Code :
 
1. Installez [l’extension C et l’extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` ensemble à `true`.

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS aperçu debugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Ouvrez Blazor une application WebAssembly existante avec un débogage activé.

   * Si vous obtenez la notification suivante que la configuration supplémentaire est nécessaire pour activer le débogage, confirmez que vous avez les extensions correctes installées et JavaScript aperçu de débogage activé, puis recharger la fenêtre:

     ![Configuration supplémentaire requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Une notification offre d’ajouter les actifs requis à l’application pour la construction et le débogage. Sélectionnez **Oui**:

     ![Ajouter les actifs requis](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Démarrer l’application dans le débbugger est un processus en deux étapes:

   1\. **Tout d’abord,** démarrer l’application en utilisant la configuration de lancement **.NET Core Launch (Blazor Standalone).**

   2\. **Après le début de l’application,** démarrez le navigateur à l’aide de **l’assemblage Web .NET Core Debug Blazor dans la** configuration de lancement Chrome (nécessite Chrome). Pour utiliser Edge au lieu `type` de Chrome, modifiez la configuration du `pwa-chrome` `pwa-msedge`lancement dans *.vscode/launch.json* de .

1. Définissez un point `IncrementCount` d’arrêt dans la méthode du `Counter` composant, puis sélectionnez le bouton pour appuyer sur le point d’arrêt :

   ![Compteur de débbug dans le code VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Debug dans le navigateur

1. Exécuter une version Debug de l’application dans l’environnement Développement.

1. Changement <kbd>de</kbd>+presse<kbd>Alt</kbd>+<kbd>D</kbd>.

1. Le navigateur doit être exécuté avec un débogage à distance activé. Si le débogage à distance est désactivé, une page d’erreur **de l’onglet de navigateur débbuggable** est générée. La page d’erreur contient des instructions pour exécuter le Blazor navigateur avec le port de débogage ouvert de sorte que le proxy de débogage peut se connecter à l’application. *Fermez toutes les instances du navigateur* et redémarrez le navigateur tel qu’il est indiqué.

Une fois que le navigateur est en cours d’exécution avec un débogage à distance activé, le raccourci clavier débogage ouvre un nouvel onglet de débogage. Après un moment, l’onglet **Sources** affiche une liste des assemblages .NET dans l’application. Élargissez chaque assemblage et trouvez les fichiers source *.cs*/*.razor* disponibles pour le débogage. Définissez les points de rupture, revenez à l’onglet de l’application et les points d’arrêt sont touchés lorsque le code s’exécute. Après qu’un point d’arrêt est touché, une seule étape (<kbd>F10</kbd>) à travers le code ou le résumé (<kbd>F8</kbd>) exécution de code normalement.

Blazorfournit un proxy de débogage qui implémente le [protocole Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec . Informations spécifiques à NET. Lorsque le raccourci clavier de Blazor débogage est pressé, pointe les Chrome DevTools au proxy. Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (d’où la nécessité d’activer le débogage à distance).

## <a name="browser-source-maps"></a>Cartes source de navigateur

Les cartes source du navigateur permettent au navigateur de cartographier les fichiers compilés vers leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client. Cependant, Blazor ne carte pas actuellement C directement à JavaScript / WASM. Au Blazor lieu de cela, ne l’interprétation IL dans le navigateur, de sorte que les cartes source ne sont pas pertinentes.

## <a name="troubleshoot"></a>Dépanner

Si vous tombez dans des erreurs, la pointe suivante peut aider:

Dans l’onglet **Debugger,** ouvrez les outils de développeur dans votre navigateur. Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.
