---
titre : 'Debug ASP.NET Core Blazor Webassembly’Author : Description : 'Découvrez comment déboguer des Blazor applications. '
monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Déboguer ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorLes applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).  Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.

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

* Microsoft Edge (version 80 ou ultérieure)
* Google Chrome (version 70 ou ultérieure)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Activer le débogage pour Visual Studio et Visual Studio Code

Pour activer le débogage pour une Blazor application Webassembly existante, mettez à jour le fichier *launchSettings. JSON* dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Une fois mis à jour, le fichier *launchSettings. JSON* doit ressembler à l’exemple suivant :

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La `inspectUri` propriété :

* Permet à l’IDE de détecter que l’application est une Blazor application Webassembly.
* Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.

## <a name="visual-studio"></a>Visual Studio

Pour déboguer une Blazor application Webassembly dans Visual Studio :

1. Créez une nouvelle ASP.NET Core Blazor application Webassembly hébergée.
1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.
1. Définissez un point d’arrêt dans *Counter. Razor* dans la `IncrementCount` méthode.
1. Accédez à l’onglet **compteur** et sélectionnez le bouton pour atteindre le point d’arrêt :

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.

Lors du débogage de votre Blazor application Webassembly, vous pouvez également déboguer votre code serveur :

1. Définissez un point d’arrêt dans la page *fetchData. Razor* dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.
1. Accédez à l’onglet **extraire les données** pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Pour déboguer une Blazor application Webassembly dans Visual Studio code :
 
1. Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Ouvrez une Blazor application Webassembly existante avec le débogage activé.

   * Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage, vérifiez que les extensions appropriées sont installées et que le débogage de l’aperçu JavaScript est activé, puis rechargez la fenêtre :

     ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Une notification propose d’ajouter les ressources requises à l’application pour la génération et le débogage. Sélectionnez **Oui**:

     ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Le démarrage de l’application dans le débogueur est un processus en deux étapes :

   1 \. **Tout d’abord**, démarrez l’application à l’aide de la configuration de lancement **de .net Core Launch ( Blazor autonome)** .

   2 \. **Une fois l’application démarrée**, démarrez le navigateur à l’aide de l' ** Blazor Assembly Web de débogage .net core dans** la configuration de lancement chrome (nécessite chrome). Pour utiliser Edge au lieu de chrome, remplacez l' `type` de la configuration de lancement dans *. vscode/Launch. JSON* par `pwa-chrome` `pwa-msedge` .

1. Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Déboguer dans le navigateur

1. Exécutez une version Debug de l’application dans l’environnement de développement.

1. Appuyez sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

1. Le navigateur doit être exécuté avec le débogage distant activé. Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée. La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert, afin que le Blazor proxy de débogage puisse se connecter à l’application. *Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.

Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application. Développez chaque assembly et recherchez les fichiers sources *. cs* / *. Razor* disponibles pour le débogage. Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute. Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).

Blazorfournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net. Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy. Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).

## <a name="browser-source-maps"></a>Mappages des sources du navigateur

Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client. Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM. Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des erreurs, le Conseil suivant peut vous aider :

Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur. Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.
