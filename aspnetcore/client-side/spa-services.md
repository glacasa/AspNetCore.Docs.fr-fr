---
title: Utilisez les services JavaScript pour créer des applications à une page unique dans ASP.NET Core
author: scottaddie
description: Renseignez-vous sur les avantages de l’utilisation de JavaScript Services pour créer une application à une page unique (SPA) soutenue par ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663778"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Utilisez les services JavaScript pour créer des applications à une page unique dans ASP.NET Core

Par [Scott Addie](https://github.com/scottaddie) et [Fiyaz Hasan](https://fiyazhasan.me/)

Une application à une page unique (SPA) est un type populaire d’application web en raison de sa riche expérience utilisateur inhérente. Il peut être difficile d’intégrer des cadres SPA ou des bibliothèques du côté client, tels que [Angular](https://angular.io/) ou [React,](https://facebook.github.io/react/)avec des cadres côté serveur tels que ASP.NET Core. JavaScript Services a été développé pour réduire les frictions dans le processus d’intégration. Il permet un fonctionnement sans faille entre les différentes piles de technologie client et serveur.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Les caractéristiques décrites dans cet article sont obsolètes à partir de ASP.NET Core 3.0. Un mécanisme d’intégration de cadres SPA plus simple est disponible dans le package [Microsoft.AspNetCore.SpaServices.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet. Pour plus d’informations, voir [[Annonce] Obsoleting Microsoft.AspNetCore.SpaServices et Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Qu’est-ce que JavaScript Services

JavaScript Services est une collection de technologies côté client pour ASP.NET Core. Son objectif est de positionner ASP.NET Core en tant que plate-forme préférée des développeurs côté serveur pour la construction des SPA.

JavaScript Services se compose de deux forfaits NuGet distincts :

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Ces paquets sont utiles dans les scénarios suivants :

* Exécuter JavaScript sur le serveur
* Utiliser un cadre OU une bibliothèque SPA
* Construire des actifs côté client avec Webpack

Une grande partie de l’accent dans cet article est placé sur l’utilisation du paquet SpaServices.

## <a name="what-is-spaservices"></a>Qu’est-ce que SpaServices

SpaServices a été créé pour positionner ASP.NET Core en tant que plate-forme préférée des développeurs côté serveur pour la construction de SPAs. SpaServices n’est pas tenu de développer des SPA avec ASP.NET Core, et il ne verrouille pas les développeurs dans un cadre client particulier.

SpaServices fournit une infrastructure utile telle que :

* [Prerendering côté serveur](#server-side-prerendering)
* [Webpack Dev Middleware](#webpack-dev-middleware)
* [Remplacement du module chaud](#hot-module-replacement)
* [Aide à l’itinéraire](#routing-helpers)

Collectivement, ces composantes de l’infrastructure améliorent à la fois le flux de travail de développement et l’expérience de l’exécution. Les composants peuvent être adoptés individuellement.

## <a name="prerequisites-for-using-spaservices"></a>Conditions préalables pour l’utilisation de SpaServices

Pour travailler avec SpaServices, installez ce qui suit :

* [Node.js](https://nodejs.org/) (version 6 ou plus tard) avec npm

  * Pour vérifier que ces composants sont installés et peuvent être trouvés, exécutez ce qui suit à partir de la ligne de commande :

    ```console
    node -v && npm -v
    ```

  * Si vous êtes déployé sur un site&mdash;Web Azure, aucune action n’est requise Node.js est installé et disponible dans les environnements du serveur.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Sur Windows à l’aide de Visual Studio 2017, le SDK est installé en sélectionnant la charge de travail **de développement multiplateforme .NET Core.**

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package

## <a name="server-side-prerendering"></a>Prerendering côté serveur

Une application universelle (également connue sous le nom d’isomorphe) est une application JavaScript capable de s’exécuter à la fois sur le serveur et le client. Les cadres angulaires, réactifs et autres cadres populaires constituent une plate-forme universelle pour ce style de développement d’applications. L’idée est d’abord de rendre les composants-cadres sur le serveur via Node.js, puis de déléguer une nouvelle exécution au client.

ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) fournis par SpaServices simplifient la mise en œuvre du prerendering côté serveur en invoquant les fonctions JavaScript sur le serveur.

### <a name="server-side-prerendering-prerequisites"></a>Prérequis de prerendering côté serveur

Installer le paquet [npm aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) :

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configuration de prerendering côté serveur

Les Tag Helpers sont rendus détectables via l’enregistrement de namespace dans le fichier *_ViewImports.cshtml* du projet :

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Ces Tag Helpers résument les subtilités de communiquer directement avec les API de bas niveau en tirant parti d’une syntaxe HTML-like à l’intérieur de la vue Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-module Tag Helper

L’aide `asp-prerender-module` Tag, utilisée dans l’exemple de code précédent, exécute *ClientApp/dist/main-server.js* sur le serveur via Node.js. Pour plus de clarté, le fichier *main-server.js* est un artefact de la tâche de transpilation TypeScript-to-JavaScript dans le processus de construction [Webpack.](https://webpack.github.io/) Webpack définit un alias `main-server`point d’entrée de ; et, traversée du graphique de dépendance pour ce pseudonyme commence sur le *fichier ClientApp/boot-server.ts* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Dans l’exemple angulaire suivant, le fichier *ClientApp/boot-server.ts* utilise la fonction et `createServerRenderer` `RenderResult` le type du paquet npm pour configurer le `aspnet-prerendering` rendu serveur via Node.js. La balisage HTML destinée au rendu côté serveur est passée à un appel de `Promise` fonction de résolution, qui est enveloppé dans un objet JavaScript fortement typé. L’importance `Promise` de l’objet est qu’il fournit asynchronement la marque HTML à la page pour l’injection dans l’élément de placeholder du DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-data Tag Helper

Lorsqu’il `asp-prerender-module` est couplé avec `asp-prerender-data` l’aide Tag, l’aide Tag peut être utilisé pour passer des informations contextuelles de la vue Razor à la côté serveur JavaScript. Par exemple, la balisage suivante `main-server` transmet les données utilisateur au module :

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

L’argument reçu `UserName` est sérialisé à l’aide du `params.data` sérialisateur JSON intégré et est stocké dans l’objet. Dans l’exemple angulaire suivant, les données sont utilisées `h1` pour construire un message d’accueil personnalisé dans un élément :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Les noms de propriété passés dans Tag Helpers sont représentés avec la notation **PascalCase.** Comparez cela à JavaScript, où les mêmes noms de propriété sont représentés avec **camelCase**. La configuration de sérialisation JSON par défaut est responsable de cette différence.

Pour développer l’exemple de code précédent, les données peuvent être `globals` transmises `resolve` du serveur à la vue en hydratant la propriété fournie à la fonction :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Le `postList` tableau défini `globals` à l’intérieur de `window` l’objet est fixé à l’objet global du navigateur. Ce levage variable à la portée globale élimine la duplication de l’effort, en particulier en ce qui concerne le chargement des mêmes données une fois sur le serveur et à nouveau sur le client.

![variable globale postList attachée à l’objet de fenêtre](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev Middleware

[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduit un flux de travail de développement rationalisé par lequel Webpack construit des ressources sur demande. Le middleware compile et sert automatiquement les ressources côté client lorsqu’une page est rechargée dans le navigateur. L’autre approche consiste à invoquer manuellement Webpack via le script de construction npm du projet lorsqu’une dépendance de tiers ou le code personnalisé change. Un script de construction npm dans le fichier *package.json* est montré dans l’exemple suivant :

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack Dev Middleware préalables

Installez le forfait [npm aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) :

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configuration Webpack Dev Middleware

Webpack Dev Middleware est enregistré dans le pipeline de demande HTTP via `Configure` le code suivant dans la méthode du fichier *Startup.cs:*

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

La `UseWebpackDevMiddleware` méthode d’extension doit être appelée `UseStaticFiles` avant [d’enregistrer l’hébergement statique de fichiers](xref:fundamentals/static-files) via la méthode d’extension. Pour des raisons de sécurité, enregistrez le middleware uniquement lorsque l’application s’exécute en mode de développement.

La propriété du `output.publicPath` fichier *webpack.config.js* indique au `dist` middleware de regarder le dossier pour les changements :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Remplacement du module chaud

Pensez à la fonction [de remplacement](https://webpack.js.org/concepts/hot-module-replacement/) du module chaud (HMR) de Webpack comme une évolution de [Webpack Dev Middleware](#webpack-dev-middleware). HMR introduit tous les mêmes avantages, mais il rationalise davantage le flux de travail de développement en mettant automatiquement à jour le contenu de la page après la compilation des modifications. Ne confondez pas cela avec un rafraîchissement du navigateur, ce qui interfèrerait avec l’état actuel de mémoire et de débogage session de la SPA. Il ya un lien en direct entre le service Webpack Dev Middleware et le navigateur, ce qui signifie que les modifications sont poussés sur le navigateur.

### <a name="hot-module-replacement-prerequisites"></a>Conditions préalables de remplacement du module chaud

Installez le forfait [npm webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) :

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configuration de remplacement du module chaud

La composante HMR doit être enregistrée dans le `Configure` pipeline de demandes HTTP de MVC dans la méthode :

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Comme c’était le cas avec `UseWebpackDevMiddleware` [Webpack Dev Middleware](#webpack-dev-middleware), la méthode d’extension doit être appelée avant la méthode d’extension. `UseStaticFiles` Pour des raisons de sécurité, enregistrez le middleware uniquement lorsque l’application s’exécute en mode de développement.

Le fichier *webpack.config.js* `plugins` doit définir un tableau, même s’il est laissé vide :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Après le chargement de l’application dans le navigateur, l’onglet Console des outils de développeur fournit la confirmation de l’activation HMR :

![Message connecté de remplacement de module chaud](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Aide à l’itinéraire

Dans la plupart des ASP.NET les APE core, le routage côté client est souvent souhaité en plus du routage côté serveur. Les systèmes de routage SPA et MVC peuvent fonctionner indépendamment sans interférence. Il ya, cependant, un cas bord posant des défis: l’identification de 404 réponses HTTP.

Considérez le scénario dans lequel `/some/page` une voie sans extension de est utilisé. Supposons que la demande ne correspond pas à un itinéraire côté serveur, mais son modèle correspond à un itinéraire côté client. Maintenant, envisagez `/images/user-512.png`une demande entrante pour , qui s’attend généralement à trouver un fichier d’image sur le serveur. Si cette trajectoire de ressources demandée ne correspond à aucun itinéraire côté serveur ou fichier statique, il est peu probable que l’application côté client serait gérer généralement le retour d’un&mdash;code d’état HTTP 404 est souhaité.

### <a name="routing-helpers-prerequisites"></a>Les secours de routage des aides préalables

Installez le paquet npm de routage côté client. En utilisant Angular comme exemple :

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configuration d’aide de routage

Une méthode `MapSpaFallbackRoute` d’extension `Configure` nommée est utilisée dans la méthode :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Les itinéraires sont évalués dans l’ordre dans lequel ils sont configurés. Par conséquent, `default` l’itinéraire dans l’exemple de code précédent est utilisé en premier pour l’appariement des motifs.

## <a name="create-a-new-project"></a>Créer un projet

JavaScript Services fournit des modèles d’application préconfigurés. SpaServices est utilisé dans ces modèles en conjonction avec différents cadres et bibliothèques tels que Angular, React, et Redux.

Ces modèles peuvent être installés via le CLI .NET Core en exécutant la commande suivante :

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Une liste des modèles SPA disponibles est affichée :

| Modèles                                 | Nom court | Langage | Balises        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core avec Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core avec React.js et Redux  | reactredux | [C#]     | Web/MVC/SPA |

Pour créer un nouveau projet à l’aide de l’un des modèles SPA, incluez le **nom court** du modèle dans la nouvelle commande [dotnet.](/dotnet/core/tools/dotnet-new) La commande suivante crée une application angulaire avec ASP.NET Core MVC configuré pour le côté serveur :

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Définir le mode de configuration de temps d’exécution

Il existe deux modes de configuration de temps d’exécution primaires :

* **Développement**:
  * Inclut des cartes sources pour faciliter le débogage.
  * N’optimise pas le code côté client pour les performances.
* **Production**:
  * Exclut les cartes sources.
  * Optimise le code côté client par le regroupement et la minification.

ASP.NET Core utilise une variable `ASPNETCORE_ENVIRONMENT` d’environnement nommée pour stocker le mode de configuration. Pour plus d’informations, voir [Définir l’environnement](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Exécuter avec .NET Core CLI

Restaurer les paquets NuGet et npm requis en exécutant la commande suivante à la racine du projet :

```dotnetcli
dotnet restore && npm i
```

Construire et exécuter l’application :

```dotnetcli
dotnet run
```

L’application commence sur localhost en fonction du [mode de configuration de temps d’exécution](#set-the-runtime-configuration-mode). Naviguer `http://localhost:5000` dans le navigateur affiche la page de destination.

### <a name="run-with-visual-studio-2017"></a>Exécuter avec Visual Studio 2017

Ouvrez le fichier *.csproj* généré par la nouvelle commande [dotnet.](/dotnet/core/tools/dotnet-new) Les paquets NuGet et npm requis sont restaurés automatiquement à l’ouverture du projet. Ce processus de restauration peut prendre jusqu’à quelques minutes, et l’application est prête à fonctionner quand elle se termine. Cliquez sur le bouton `Ctrl + F5`d’exécution verte ou appuyez sur, et le navigateur s’ouvre sur la page de destination de l’application. L’application fonctionne sur localhost en fonction du [mode de configuration de temps d’exécution](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Test de l'application

Les modèles SpaServices sont préconfigurés pour exécuter des tests côté client à l’aide [de Karma](https://karma-runner.github.io/1.0/index.html) et [Jasmine](https://jasmine.github.io/). Jasmine est un cadre de test unitaire populaire pour JavaScript, tandis que Karma est un coureur de test pour ces tests. Karma est configuré pour travailler avec le [Webpack Dev Middleware](#webpack-dev-middleware) de telle sorte que le développeur n’est pas tenu de s’arrêter et d’exécuter le test chaque fois que des modifications sont apportées. Qu’il s’agisse du code en cours d’exécution contre le boîtier de test ou le cas de test lui-même, le test s’exécute automatiquement.

À titre d’exemple, deux cas de test de `CounterComponent` jasmin sont déjà prévus pour le fichier *counter.component.spec.ts* :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Ouvrez l’invite de commande dans le répertoire *ClientApp.* Exécutez la commande suivante :

```console
npm test
```

Le script lance le coureur de test Karma, qui lit les paramètres définis dans le fichier *karma.conf.js.* Entre autres paramètres, le *karma.conf.js* identifie les fichiers `files` de test à exécuter via son tableau :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publier l’application

Voir [ce numéro GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) pour plus d’informations sur la publication à Azure.

La combinaison des actifs générés du côté client et des artefacts ASP.NET Core publiés dans un ensemble prêt à être déployé peut être lourde. Heureusement, SpaServices orchestre tout ce processus de publication avec `RunWebpack`une cible MSBuild personnalisée nommée :

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

La cible MSBuild a les responsabilités suivantes :

1. Restaurer les paquets npm.
1. Créer une construction de qualité de production des actifs tiers côté client.
1. Créez une construction de qualité de production des actifs personnalisés du côté client.
1. Copiez les actifs générés par Webpack au dossier de publication.

La cible MSBuild est invoquée lors de l’exécution :

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Docs angulaires](https://angular.io/docs)
