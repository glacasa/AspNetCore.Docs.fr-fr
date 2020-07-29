---
title: ASP.NET Core de débogageBlazor WebAssembly
author: guardrex
description: Découvrez comment déboguer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159705"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core de débogageBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssemblyles applications peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome). Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.

Les scénarios disponibles sont les suivants :

* Définir et supprimer des points d’arrêt.
* Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).
* Une étape (<kbd>F10</kbd>) à l’aide du code.
* Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.
* Dans l' *affichage des variables locales,* observez les valeurs des variables locales.
* Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.

Pour le moment, vous *ne pouvez pas*:

* Arrêt sur les exceptions non gérées.
* Atteindre les points d’arrêt pendant le démarrage de l’application.

Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.

## <a name="prerequisites"></a>Prérequis

Le débogage requiert l’un des navigateurs suivants :

* Google Chrome (version 70 ou ultérieure) (par défaut)
* Microsoft Edge (version 80 ou ultérieure)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Activer le débogage pour Visual Studio et Visual Studio Code

Pour activer le débogage d’une Blazor WebAssembly application existante, mettez à jour le `launchSettings.json` fichier dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Une fois mis à jour, le `launchSettings.json` fichier doit ressembler à l’exemple suivant :

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La `inspectUri` propriété :

* Permet à l’IDE de détecter que l’application est une Blazor WebAssembly application.
* Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.

Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.

## <a name="visual-studio"></a>Visual Studio

Pour déboguer une Blazor WebAssembly application dans Visual Studio :

1. Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .
1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.
1. Définissez un point d’arrêt dans `Pages/Counter.razor` la `IncrementCount` méthode.
1. Accédez à l' **`Counter`** onglet et sélectionnez le bouton pour atteindre le point d’arrêt :

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.

Lors du débogage de votre Blazor WebAssembly application, vous pouvez également déboguer votre code serveur :

1. Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.
1. Accédez à l' **`Fetch Data`** onglet pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Déboguer autonomeBlazor WebAssembly

1. Ouvrez l' Blazor WebAssembly application autonome dans vs code.

   Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage :
   
   * Vérifiez que vous avez installé les extensions appropriées.
   * Confirmez que le débogage de l’aperçu JavaScript est activé.
   * Rechargez la fenêtre.

   ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.

1. Quand vous y êtes invité, sélectionnez l’option de ** Blazor WebAssembly débogage** pour démarrer le débogage.

   ![Liste des options de débogage disponibles](index/_static/blazor-vscode-debugtypes.png)

1. L’application autonome est lancée et un navigateur de débogage est ouvert.

1. Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Débogage hébergéBlazor WebAssembly

1. Ouvrez le Blazor WebAssembly dossier de solution de l’application hébergée dans vs code.

1. Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche. Sélectionnez **Oui**.

   ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Dans la palette de commandes en haut de la fenêtre, sélectionnez le projet *serveur* dans la solution hébergée.

Un `launch.json` fichier est généré à l’aide de la configuration de lancement pour le lancement du débogueur.

### <a name="attach-to-an-existing-debugging-session"></a>Attacher à une session de débogage existante

Pour attacher une application en cours d’exécution Blazor , créez un `launch.json` fichier avec la configuration suivante :

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes. Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.

### <a name="launch-configuration-options"></a>Lancer les options de configuration

Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage ( `.vscode/launch.json` ).

| Option    | Description |
| --------- | ----------- |
| `request` | Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor WebAssembly application ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution. |
| `url`     | URL à ouvrir dans le navigateur lors du débogage. La valeur par défaut est `https://localhost:5001`. |
| `browser` | Navigateur à lancer pour la session de débogage. A la valeur `edge` ou `chrome`. La valeur par défaut est `chrome`. |
| `trace`   | Utilisé pour générer des journaux à partir du débogueur JS. Définissez sur `true` pour générer des journaux. |
| `hosted`  | Doit avoir la valeur en `true` cas de lancement et de débogage d’une application hébergée Blazor WebAssembly . |
| `webRoot` | Spécifie le chemin d’accès absolu du serveur Web. Doit être défini si une application est servie à partir d’un sous-itinéraire. |
| `timeout` | Nombre de millisecondes d’attente de l’attachement de la session de débogage. La valeur par défaut est 30 000 millisecondes (30 secondes). |
| `program` | Référence au fichier exécutable pour exécuter le serveur de l’application hébergée. Doit être défini si `hosted` a la valeur `true` . |
| `cwd`     | Répertoire de travail dans lequel l’application doit être lancée. Doit être défini si `hosted` a la valeur `true` . |
| `env`     | Variables d’environnement à fournir au processus lancé. Applicable uniquement si `hosted` a la valeur `true` . |

### <a name="example-launch-configurations"></a>Exemples de configurations de lancement

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Lancer et déboguer une Blazor WebAssembly application autonome

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Attacher à une application en cours d’exécution à une URL spécifiée

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Lancer et déboguer une application hébergée Blazor WebAssembly avec Microsoft Edge

La configuration du navigateur est par défaut Google Chrome. Lorsque vous utilisez Microsoft Edge pour le débogage, affectez à la valeur `browser` `edge` . Pour utiliser Google Chrome, vous ne devez pas définir l' `browser` option ou définir la valeur de l’option sur `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

Dans l’exemple précédent, `MyHostedApp.Server.dll` est l’assembly de l’application *serveur* . Le `.vscode` dossier se trouve dans le dossier de la solution, en regard des `Client` `Server` dossiers, et `Shared` .

## <a name="debug-in-the-browser"></a>Déboguer dans le navigateur

1. Exécutez une version Debug de l’application dans l’environnement de développement.

1. Lancez un navigateur et accédez à l’URL de l’application (par exemple, `https://localhost:5001` ).

1. Dans le navigateur, essayez de commencer le débogage à distance en appuyant sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

   Le navigateur doit s’exécuter avec le débogage à distance activé, qui n’est pas le paramètre par défaut. Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est affichée avec des instructions pour lancer le navigateur avec le port de débogage ouvert. Suivez les instructions de votre navigateur pour ouvrir une nouvelle fenêtre de navigateur. Fermez la fenêtre de navigateur précédente.

1. Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage (<kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) ouvre un nouvel onglet du débogueur.

1. Après un moment, l’onglet **sources** affiche une liste des assemblys .net de l’application dans le `file://` nœud.

1. Dans le code du composant ( `.razor` fichiers) et les fichiers de code C# ( `.cs` ), les points d’arrêt que vous définissez sont atteints lors de l’exécution du code. Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).

Blazorfournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net. Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy. Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).

## <a name="browser-source-maps"></a>Mappages des sources du navigateur

Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client. Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM. Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :

* Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur. Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.
* Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs. Pour plus d’informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio requiert l’option **activer le débogage JavaScript pour ASP.net (chrome, Edge et IE)** dans **Outils**  >  **options**  >  **débogage**  >  **général**. Il s’agit du paramètre par défaut pour Visual Studio. Si le débogage ne fonctionne pas, vérifiez que l’option est sélectionnée.
