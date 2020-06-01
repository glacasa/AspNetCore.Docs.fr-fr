---
titre : « Debug ASP.NET Core Blazor Webassembly » auteur : guardrex Description : « Découvrez comment déboguer des Blazor applications. »
monikerRange : ' >= aspnetcore-3,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 05/31/2020 No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR 'UID : éblouissant/débogage

---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Déboguer ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

BlazorLes applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome). Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.

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

Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.

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
 
Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .

![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Déboguer le Blazor Webassembly autonome

1. Ouvrez l' Blazor application Webassembly autonome dans vs code.

   Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage :
   
   * Vérifiez que vous avez installé les extensions appropriées.
   * Confirmez que le débogage de l’aperçu JavaScript est activé.
   * Rechargez la fenêtre.

   ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.

1. Quand vous y êtes invité, sélectionnez l’option de ** Blazor débogage webassembly** pour démarrer le débogage.

   ![Liste des options de débogage disponibles](index/_static/blazor-vscode-debugtypes.png)

1. L’application autonome est lancée et un navigateur de débogage est ouvert.

1. Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Déboguer le Blazor Webassembly hébergé

1. Ouvrez l' Blazor application Webassembly hébergée dans vs code.

1. Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche. Sélectionnez **Oui**.

   ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Dans la fenêtre de sélection, sélectionnez le projet *serveur* dans la solution hébergée.

Un fichier *Launch. JSON* est généré avec la configuration de lancement pour le lancement du débogueur.

### <a name="attach-to-an-existing-debugging-session"></a>Attacher à une session de débogage existante

Pour attacher une application en cours d’exécution Blazor , créez un fichier *Launch. JSON* avec la configuration suivante :

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

Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage.

| Option    | Description |
| --------- | ----------- |
| `request` | Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor application webassembly ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution. |
| `url`     | URL à ouvrir dans le navigateur lors du débogage. La valeur par défaut est `https://localhost:5001`. |
| `browser` | Navigateur à lancer pour la session de débogage. A la valeur `edge` ou `chrome`. La valeur par défaut est `chrome`. |
| `trace`   | Utilisé pour générer des journaux à partir du débogueur JS. Définissez sur `true` pour générer des journaux. |
| `hosted`  | Doit avoir la valeur `true` si vous lancez et déboguez une Blazor application webassembly hébergée. |
| `webRoot` | Spécifie le chemin d’accès absolu du serveur Web. Doit être défini si une application est servie à partir d’un sous-itinéraire. |
| `timeout` | Nombre de millisecondes d’attente de l’attachement de la session de débogage. La valeur par défaut est 30 000 millisecondes (30 secondes). |
| `program` | Référence au fichier exécutable pour exécuter le serveur de l’application hébergée. Doit être défini si `hosted` a la valeur `true` . |
| `cwd`     | Répertoire de travail dans lequel l’application doit être lancée. Doit être défini si `hosted` a la valeur `true` . |
| `env`     | Variables d’environnement à fournir au processus lancé. Applicable uniquement si `hosted` a la valeur `true` . |

### <a name="example-launch-configurations"></a>Exemples de configurations de lancement

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Lancer et déboguer une Blazor application Webassembly autonome

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

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Lancer et déboguer une Blazor application Webassembly hébergée

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Déboguer dans le navigateur

1. Exécutez une version Debug de l’application dans l’environnement de développement.

1. Appuyez sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.

1. Le navigateur doit être exécuté avec le débogage distant activé. Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée. La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert, afin que le Blazor proxy de débogage puisse se connecter à l’application. *Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.

Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application. Développez chaque assembly et recherchez les fichiers sources *. cs* / *. Razor* disponibles pour le débogage. Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute. Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).

Blazorfournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net. Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy. Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).

## <a name="browser-source-maps"></a>Mappages des sources du navigateur

Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client. Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM. Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :

* Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur. Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.
* Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs. Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
