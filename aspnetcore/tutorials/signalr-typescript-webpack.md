---
title: Utiliser ASP.NET Core SignalR avec TypeScript et Webpack
author: ssougnez
description: Dans ce tutoriel, vous configurez Webpack pour regrouper SignalR et créer une application web ASP.NET Core dont le client est écrit dans TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511338"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Utiliser ASP.NET Core SignalR avec TypeScript et Webpack

Par [Sébastien Sougnez](https://twitter.com/ssougnez) et [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) permet aux développeurs de regrouper et générer les ressources côté client d’une application web. Ce tutoriel montre comment utiliser Webpack dans une application web ASP.NET Core SignalR dont le client est écrit en [TypeScript](https://www.typescriptlang.org/).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Générer automatiquement un modèle d’application ASP.NET Core SignalR de démarrage
> * Configurer le client TypeScript SignalR
> * Configurer un pipeline de build à l’aide de Webpack
> * Configurer le serveur SignalR
> * Activer la communication entre le client et le serveur

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([comment télécharger](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge **de travail ASP.NET et le développement web**
* [SDK .NET Core 3.0 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [SDK .NET Core 3.0 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pour Visual Studio Code version 1.17.1 ou ultérieure](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Créer l’application web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH*. Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation. Suivez ces instructions dans Visual Studio :

1. Lancez Visual Studio. À la fenêtre de départ, sélectionnez **Continuer sans code**.
1. Naviguez vers **tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.
1. Sélectionnez l’entrée *$(PATH)* dans la liste. Cliquez sur la flèche pour déplacer l’entrée à la deuxième position de la liste, et sélectionnez **OK**.

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

La configuration Visual Studio est complète.

1. Utilisez l’option **de** > menu File**New** > **Project** et choisissez le **modèle d’application Web ASP.NET Core.** Sélectionnez **Suivant**.
1. Nommez le projet *SignalRWebPack*, et sélectionnez **Créer**.
1. Sélectionnez *.NET Core* à partir du cadre cible de décrochage, et sélectionnez *ASP.NET Noyau 3.1* à partir de la baisse du sélecteur-cadre. Sélectionnez le modèle **vide,** et sélectionnez **Créer**.

Ajouter `Microsoft.TypeScript.MSBuild` le paquet au projet :

1. Dans **Solution Explorer** (droite, cliquez à droite sur le nœud du projet et **sélectionnez Manage NuGet Packages**. Dans l’onglet **Parcourir,** recherchez, `Microsoft.TypeScript.MSBuild`puis cliquez sur **Installer** sur la droite pour installer le paquet.

Visual Studio ajoute le paquet NuGet sous le nœud **dépendances** dans **Solution Explorer**, permettant la compilation TypeScript dans le projet.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le **Terminal intégré** :

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* La `dotnet new` commande crée une application web ASP.NET Core vide dans un répertoire *SignalRWebPack.*
* La `code` commande ouvre le dossier *SignalRWebPack* dans l’exemple actuel de Visual Studio Code.

Exécuter la commande CLI CLI de base suivante dans le **terminal intégré**:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

La commande précédente ajoute le package [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) permettant la compilation TypeScript dans le projet.

---

## <a name="configure-webpack-and-typescript"></a>Configurer Webpack et TypeScript

Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.

1. Exécutez la commande suivante dans la racine du projet pour créer un fichier *package.json* :

    ```console
    npm init -y
    ```

1. Ajoutez la propriété surlignée au fichier *package.json* et enregistrez les modifications du fichier :

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.

1. Installez les packages npm nécessaires. À partir de la racine du projet, exécutez la commande suivante :

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Détails de commande à prendre en compte :

    * Un numéro de version suit le signe `@` pour chaque nom de package. npm installe ces versions de package spécifiques.
    * L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json*. Par exemple, `"webpack": "4.41.5"` peut être utilisé à la place de `"webpack": "^4.41.5"`. Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.

    Voir les documents [npm-installer](https://docs.npmjs.com/cli/install) pour plus de détails.

1. Remplacer `scripts` la propriété du fichier *package.json* par le code suivant :

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Explication des scripts :

    * `build`: Regroupe les ressources côté client en mode de développement et veille aux modifications de fichiers. L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change. L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking). Utilisez uniquement `build` dans le développement.
    * `release`: Regroupe les ressources côté client en mode production.
    * `publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production. La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.

1. Créez un fichier nommé *webpack.config.js*, dans la racine du projet, avec le code suivant :

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Le fichier précédent configure la compilation Webpack. Détails de configuration à prendre en compte :

    * La propriété `output` remplace la valeur par défaut de *dist*. Le regroupement est émis dans le répertoire *wwwroot* à la place.
    * Le tableau `resolve.extensions` inclut *.js* pour importer le client JavaScript SignalR.

1. Créer un nouvel annuaire *de src* dans la racine du projet pour stocker les actifs du projet côté client.

1. Créez *src/index.html* avec la balisage suivant.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Le code HTML précédent définit le balisage réutilisable de la page d’accueil.

1. Créez un répertoire *src/css*. Son objectif est de stocker les fichiers *.css* du projet.

1. Créez *le src/css/main.css* avec le CSS suivant :

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Le fichier *main.css* précédent définit le style de l’application.

1. Créez *src/tsconfig.json* avec le JSON suivant :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Créez *des src/index.ts* avec le code suivant :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :

    * `keyup`: Cet événement s’allume `tbMessage`lorsque l’utilisateur tape dans la boîte à texte. La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée**.
    * `click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer**. La fonction `send` est appelée.

## <a name="configure-the-app"></a>Configurer l’application

1. Dans `Startup.Configure`, ajouter des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Le code précédent permet au serveur de localiser et de servir le fichier *index.html.*  Le fichier est servi si l’utilisateur entre son URL complète ou l’URL racine de l’application web.

1. À la `Startup.Configure`fin de , la `ChatHub` carte d’une route */hub* vers le hub. Remplacez le code qui affiche *Hello World!* par la ligne suivante : 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. Dans `Startup.ConfigureServices`, appelez [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Créez un nouvel annuaire nommé *Hubs* dans la racine du projet *SignalRWebPack/* pour stocker le hub SignalR.

1. Créez un hub *Hubs/ChatHub.cs* avec le code suivant :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Ajoutez la `using` déclaration suivante en haut du fichier *Startup.cs* pour résoudre la `ChatHub` référence :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Activer la communication entre le client et le serveur

L’application affiche actuellement un formulaire de base pour envoyer des messages, mais n’est pas encore fonctionnelle. Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.

1. Exécutez la commande suivante à la racine du projet :

    ```console
    npm i @microsoft/signalr @types/node
    ```

    La commande précédente installe :

     * Le [client SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), qui permet au client d’envoyer des messages au serveur.
     * Les définitions de type TypeScript pour Node.js, qui permet de compiler le temps de vérification des types Node.js.

1. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Le code précédent prend en charge la réception de messages du serveur. La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur. La fonction `withUrl` configure l’URL du hub.

    SignalR permet l’échange de messages entre un client et un serveur. Chaque message a un nom spécifique. Par exemple, les `messageReceived` messages avec le nom peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone des messages. L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`. N’importe quel nombre de noms de messages peut être écouté. Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu. Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`. Il est ajouté à l’élément `div` principal qui affiche les messages.

1. Maintenant que le client peut recevoir un message, configurez-le pour en envoyer. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`. Le premier paramètre de la méthode est le nom du message. Les données du message se trouvent dans les autres paramètres. Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur. Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte. Si l’envoi fonctionne, la valeur de la zone de texte est effacée.

1. Ajoutez la méthode `NewMessage` à la classe `ChatHub` :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit. Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages. Il vous suffit d’avoir une méthode du même nom que le message.

    Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`. La méthode C# `NewMessage` attend les données envoyées par le client. Un appel est fait à [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Les messages reçus sont envoyés à tous les clients connectés au hub.

## <a name="test-the-app"></a>Test de l'application

Vérifiez que l’application fonctionne avec les étapes suivantes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Exécuter Webpack en mode de *mise en production*. À l’aide de la fenêtre **De la console Package Manager,** exécutez la commande suivante dans la racine du projet. Si vous ne vous trouvez pas à la racine du projet, tapez `cd SignalRWebPack` avant d’entrer la commande.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sélectionnez **Debug** > **Start sans déboguer** pour lancer l’application dans un navigateur sans attacher le débbugger. Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.

   Si vous obtenez des erreurs de compilation, essayez de fermer et de rouvrir la solution. 

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message**, puis cliquez sur le bouton **Envoyer**. Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :

    ```dotnetcli
    dotnet run
    ```

    Le serveur web démarre l’application et la rend disponible sur localhost.

1. Ouvrez un navigateur et accédez à `http://localhost:<port_number>`. Le fichier *wwwroot/index.html* est présent. Copiez l’URL à partir de la barre d’adresse.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message**, puis cliquez sur le bouton **Envoyer**. Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge **de travail ASP.NET et le développement web**
* [.NET Core SDK 2.2 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pour Visual Studio Code version 1.17.1 ou ultérieure](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Créer l’application web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH*. Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation. Suivez ces instructions dans Visual Studio :

1. Naviguez vers **tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.
1. Sélectionnez l’entrée *$(PATH)* dans la liste. Cliquez sur la flèche vers le haut pour déplacer l’entrée à la deuxième position dans la liste.

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

La configuration de Visual Studio est terminée. Nous allons maintenant créer le projet.

1. Utilisez l’option de menu **Fichier** > **Nouveau** > **Projet** et choisissez le modèle **Application web ASP.NET Core**.
1. Nommez le projet *SignalRWebPack*, et sélectionnez **Créer**.
1. Sélectionnez *.NET Core* dans la liste déroulante du framework cible, puis *ASP.NET Core 2.2* dans la liste déroulante du sélecteur de framework. Sélectionnez le modèle **vide,** et sélectionnez **Créer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le **Terminal intégré** :

```dotnetcli
dotnet new web -o SignalRWebPack
```

Une application web ASP.NET Core vide, ciblant .NET Core, est créée dans un répertoire *SignalRWebPack*.

---

## <a name="configure-webpack-and-typescript"></a>Configurer Webpack et TypeScript

Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.

1. Exécutez la commande suivante dans la racine du projet pour créer un fichier *package.json* :

    ```console
    npm init -y
    ```

1. Ajoutez la propriété en surbrillance au fichier *package.json* :

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.

1. Installez les packages npm nécessaires. À partir de la racine du projet, exécutez la commande suivante :

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Détails de commande à prendre en compte :

    * Un numéro de version suit le signe `@` pour chaque nom de package. npm installe ces versions de package spécifiques.
    * L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json*. Par exemple, `"webpack": "4.29.3"` peut être utilisé à la place de `"webpack": "^4.29.3"`. Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.

    Voir les documents [npm-installer](https://docs.npmjs.com/cli/install) pour plus de détails.

1. Remplacer `scripts` la propriété du fichier *package.json* par le code suivant :

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Explication des scripts :

    * `build`: Regroupe les ressources côté client en mode de développement et veille aux modifications de fichiers. L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change. L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking). Utilisez uniquement `build` dans le développement.
    * `release`: Regroupe les ressources côté client en mode production.
    * `publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production. La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.

1. Créez un fichier nommé *webpack.config.js* dans la racine du projet, avec le code suivant :

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Le fichier précédent configure la compilation Webpack. Détails de configuration à prendre en compte :

    * La propriété `output` remplace la valeur par défaut de *dist*. Le regroupement est émis dans le répertoire *wwwroot* à la place.
    * Le tableau `resolve.extensions` inclut *.js* pour importer le client JavaScript SignalR.

1. Créer un nouvel annuaire *de src* dans la racine du projet pour stocker les actifs du projet côté client.

1. Créez *src/index.html* avec la balisage suivant.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Le code HTML précédent définit le balisage réutilisable de la page d’accueil.

1. Créez un répertoire *src/css*. Son objectif est de stocker les fichiers *.css* du projet.

1. Créez *le src/css/main.css* avec la majoration suivante :

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Le fichier *main.css* précédent définit le style de l’application.

1. Créez *src/tsconfig.json* avec le JSON suivant :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Créez *des src/index.ts* avec le code suivant :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :

    * `keyup`: Cet événement s’allume `tbMessage` lorsque l’utilisateur tape dans la boîte à texte. La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée**.
    * `click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer**. La fonction `send` est appelée.

## <a name="configure-the-aspnet-core-app"></a>Configurer l’application ASP.NET Core

1. Le code fourni dans la méthode `Startup.Configure` affiche *Hello World!*. Remplacez l’appel de méthode `app.Run` par des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Le code précédent permet au serveur de localiser et traiter le fichier *index.html*, que l’utilisateur entre son URL complète ou l’URL racine de l’application web.

1. Appelez [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) dans `Startup.ConfigureServices`. Il ajoute les services SignalR au projet.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Mappez une route */hub* au hub `ChatHub`. Ajoutez les lignes suivantes `Startup.Configure`à la fin de :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Créez un répertoire *Hubs* à la racine du projet. Son objectif est de stocker le hub SignalR, qui est créé à l’étape suivante.

1. Créez un hub *Hubs/ChatHub.cs* avec le code suivant :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Ajoutez le code suivant en haut du fichier *Startup.cs* pour résoudre la référence de `ChatHub` :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Activer la communication entre le client et le serveur

Actuellement, l’application affiche un formulaire simple pour envoyer des messages. Rien ne se produit quand vous essayez de le faire. Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.

1. Exécutez la commande suivante à la racine du projet :

    ```console
    npm install @aspnet/signalr
    ```

    La commande précédente installe le [client SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), ce qui permet au client d’envoyer des messages au serveur.

1. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Le code précédent prend en charge la réception de messages du serveur. La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur. La fonction `withUrl` configure l’URL du hub.

    SignalR permet l’échange de messages entre un client et un serveur. Chaque message a un nom spécifique. Par exemple, les `messageReceived` messages avec le nom peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone des messages. L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`. Vous pouvez écouter n’importe quel nombre de noms de message. Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu. Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`. Le nouveau message est `div` ajouté à l’élément principal affichant les messages.

1. Maintenant que le client peut recevoir un message, configurez-le pour en envoyer. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`. Le premier paramètre de la méthode est le nom du message. Les données du message se trouvent dans les autres paramètres. Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur. Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte. Si l’envoi fonctionne, la valeur de la zone de texte est effacée.

1. Ajoutez la méthode `NewMessage` à la classe `ChatHub` :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit. Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages. Il vous suffit d’avoir une méthode du même nom que le message.

    Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`. La méthode C# `NewMessage` attend les données envoyées par le client. Un appel est fait à [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Les messages reçus sont envoyés à tous les clients connectés au hub.

## <a name="test-the-app"></a>Test de l'application

Vérifiez que l’application fonctionne avec les étapes suivantes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Exécuter Webpack en mode de *mise en production*. À l’aide de la fenêtre **De la console Package Manager,** exécutez la commande suivante dans la racine du projet. Si vous ne vous trouvez pas à la racine du projet, tapez `cd SignalRWebPack` avant d’entrer la commande.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sélectionnez **Debug** > **Start sans déboguer** pour lancer l’application dans un navigateur sans attacher le débbugger. Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message**, puis cliquez sur le bouton **Envoyer**. Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :

    ```dotnetcli
    dotnet run
    ```

    Le serveur web démarre l’application et la rend disponible sur localhost.

1. Ouvrez un navigateur et accédez à `http://localhost:<port_number>`. Le fichier *wwwroot/index.html* est présent. Copiez l’URL à partir de la barre d’adresse.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message**, puis cliquez sur le bouton **Envoyer**. Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
