---
title: Regrouper et minifier les actifs statiques dans ASP.NET Core
author: scottaddie
description: Apprenez à optimiser les ressources statiques dans une application Web ASP.NET Core en appliquant des techniques de regroupement et de minification.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658269"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Regrouper et minifier les actifs statiques dans ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie) et [David Pine](https://twitter.com/davidpine7)

Cet article explique les avantages de l’application du regroupement et de la minification, y compris la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET applications Web Core.

## <a name="what-is-bundling-and-minification"></a>Qu’est-ce que le regroupement et la minification

Le regroupement et la minification sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application web. Utilisés ensemble, le regroupement et la minification améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des actifs statiques demandés.

Le regroupement et la minification améliorent principalement le temps de chargement de la première page. Une fois qu’une page Web a été demandée, le navigateur cache les actifs statiques (JavaScript, CSS et images). Par conséquent, le regroupement et la minification n’améliorent pas les performances lorsque vous demandez la même page, ou pages, sur le même site demandant les mêmes actifs. Si l’en-tête expire n’est pas réglé correctement sur les actifs et si le regroupement et la minification n’est pas utilisé, heuristique de fraîcheur du navigateur marquent les actifs périmés après quelques jours. En outre, le navigateur nécessite une demande de validation pour chaque actif. Dans ce cas, le regroupement et la minification offrent une amélioration des performances même après la demande de première page.

### <a name="bundling"></a>Groupement

Le regroupement consiste à combiner plusieurs fichiers en un seul. Le regroupement réduit le nombre de demandes de serveur nécessaires pour rendre un actif Web, comme une page Web. Vous pouvez créer n’importe quel nombre de paquets individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP du navigateur au serveur ou du service fournissant votre application. Il en résulte une amélioration des performances de chargement de première page.

### <a name="minification"></a>Minimisation

La minification supprime les caractères inutiles du code sans modifier les fonctionnalités. Le résultat est une réduction significative de la taille des actifs demandés (tels que le CSS, les images et les fichiers JavaScript). Les effets secondaires courants de la minification comprennent le raccourcissement des noms variables à un seul personnage et la suppression des commentaires et de l’espace blanc inutile.

Considérez la fonction JavaScript suivante :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

La minification réduit la fonction à ce qui suit :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

En plus de supprimer les commentaires et l’espace blanc inutile, le paramètre suivant et les noms variables ont été renommés comme suit:

Original | Affectation d'un nouveau nom
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impact du regroupement et de la minification

Le tableau suivant décrit les différences entre le chargement individuel des actifs et l’utilisation du regroupement et de la minification :

Action | Avec B/M | Sans B/M | Modifier
--- | :---: | :---: | :---:
Demandes de fichiers  | 7   | 18     | 157%
KB transféré | 156 | 264.68 | 70 %
Temps de chargement (ms) | 885 | 2360   | 167%

Les navigateurs sont assez verbeux en ce qui concerne les en-têtes de demande HTTP. Le total des octets envoyés métrique a connu une réduction significative lors du regroupement. Le temps de charge montre une amélioration significative, mais cet exemple a couru localement. Des gains de performance plus importants sont réalisés lors de l’utilisation du regroupement et de la minification avec des actifs transférés sur un réseau.

## <a name="choose-a-bundling-and-minification-strategy"></a>Choisissez une stratégie de regroupement et de minification

Les modèles de projet MVC et Razor Pages constituent une solution hors boîte pour le regroupement et la minification composé d’un fichier de configuration JSON. Les outils tiers, tels que le coureur de tâches [Grunt,](xref:client-side/using-grunt) accomplissent les mêmes tâches avec un peu plus de complexité. Un outil tiers est un grand ajustement lorsque votre flux de travail&mdash;de développement nécessite un traitement au-delà du regroupement et de la minification tels que le linting et l’optimisation d’image. En utilisant le regroupement et la minification du temps de conception, les fichiers minifiés sont créés avant le déploiement de l’application. Le regroupement et la minification avant le déploiement offrent l’avantage d’une charge de serveur réduite. Cependant, il est important de reconnaître que le regroupement et la minification en temps de conception augmente la complexité de la construction et ne fonctionne qu’avec des fichiers statiques.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et la minification

::: moniker range="<= aspnetcore-2.0"

Dans ASP.NET Core 2.0 ou plus tôt, les modèles de projet MVC et Razor Pages fournissent un fichier de configuration *bundleconfig.json* qui définit les options pour chaque forfait :

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Dans ASP.NET Core 2.1 ou plus tard, ajoutez un nouveau fichier JSON, nommé *bundleconfig.json*, à la racine du projet MVC ou Razor Pages. Inclure le JSON suivant dans ce dossier comme point de départ:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Le *fichier bundleconfig.json* définit les options pour chaque bundle. Dans l’exemple précédent, une configuration de paquet unique est définie pour les fichiers JavaScript*personnalisés (wwwroot/js/site.js*) et de la feuille de style *(wwwroot/css/site.css*).

Les options de configuration comprennent ce qui suit :

* `outputFileName`: Le nom du fichier du paquet à la sortie. Peut contenir un chemin relatif à partir du fichier *bundleconfig.json.* **Obligatoire**
* `inputFiles`: Un tableau de fichiers à regrouper. Ce sont des chemins relatifs au fichier de configuration. **optionnel,** une valeur vide se traduit par un fichier de sortie vide. les motifs [de globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) sont soutenus.
* `minify`: Les options de minification pour le type de sortie. **optionnel,** *par défaut - `minify: { enabled: true }` *
  * Les options de configuration sont disponibles par type de fichier de sortie.
    * [Minificateur CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minificateur JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minificateur HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Indicateur indiquant s’il faut ajouter des fichiers générés au fichier du projet. **facultatif**, *par défaut - faux*
* `sourceMap`: Indicateur indiquant s’il convient de générer une carte source pour le fichier groupé. **facultatif**, *par défaut - faux*
* `sourceMapRootPath`: Le chemin de racine pour stocker le fichier de carte source générée.

## <a name="build-time-execution-of-bundling-and-minification"></a>Exécution du regroupement et de la minification en temps de construction

Le package [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet permet l’exécution du regroupement et de la minification au moment de la construction. Le paquet injecte [des cibles MSBuild](/visualstudio/msbuild/msbuild-targets) qui fonctionnent à l’heure de construction et de nettoyage. Le fichier *bundleconfig.json* est analysé par le processus de build pour produire les fichiers de sortie en fonction de la configuration définie.

> [!NOTE]
> BuildBundlerMinifier appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support. Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ajoutez le package *BuildBundlerMinifier* à votre projet.

Créez le projet. Ce qui suit apparaît dans la fenêtre de sortie :

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Nettoyez le projet. Ce qui suit apparaît dans la fenêtre de sortie :

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Ajoutez le package *BuildBundlerMinifier* à votre projet :

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Si vous utilisez ASP.NET Core 1.x, restaurer le paquet nouvellement ajouté:

```dotnetcli
dotnet restore
```

Générez le projet :

```dotnetcli
dotnet build
```

Voici :

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Nettoyer le projet :

```dotnetcli
dotnet clean
```

Vous obtenez la sortie suivante :

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Exécution ponctuelle du regroupement et de la minification

Il est possible d’exécuter les tâches de regroupement et de minification sur une base ad hoc, sans construire le projet. Ajoutez le forfait [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet à votre projet :

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support. Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).

Ce paquet étend le CLI de base .NET pour inclure l’outil *de paquet dotnet.* La commande suivante peut être exécutée dans la fenêtre de la console de gestionnaire de paquet (PMC) ou dans une coque de commande :

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet Package Manager ajoute des dépendances au fichier `<PackageReference />` '.csproj sous forme de nœuds. La `dotnet bundle` commande est enregistrée auprès de l’ÉLIC de base .NET seulement lorsqu’un `<DotNetCliToolReference />` nœud est utilisé. Modifier le fichier 'csproj en conséquence.

## <a name="add-files-to-workflow"></a>Ajouter des fichiers au flux de travail

Prenons un exemple dans lequel un fichier *custom.css* supplémentaire est ajouté ressemblant à ce qui suit:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Pour minifier *custom.css* et l’empaqueter avec *site.css* dans un fichier *site.min.css,* ajoutez le chemin relatif à *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativement, le modèle de globbing suivant pourrait être employé :
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Ce modèle de glisse correspond à tous les fichiers CSS et exclut le modèle de fichier minifié.

Générez l’application. Ouvrez *site.min.css* et remarquez que le contenu de *custom.css* est annexé à la fin du fichier.

## <a name="environment-based-bundling-and-minification"></a>Regroupement et minification basés sur l’environnement

Comme meilleure pratique, les fichiers groupés et minifiés de votre application doivent être utilisés dans un environnement de production. Pendant le développement, les fichiers originaux facilitent le débogage de l’application.

Spécifiez les fichiers à inclure dans vos pages en utilisant [l’Aide à l’étiquette environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues. L’Environnement Tag Helper ne rend son contenu que lorsqu’il est en cours d’exécution dans des [environnements](xref:fundamentals/environments)spécifiques .

L’étiquette suivante `environment` rend les fichiers CSS non `Development` traités lorsqu’ils sont exécutés dans l’environnement :

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

L’étiquette suivante `environment` rend les fichiers CSS groupés et minifiés lorsqu’ils sont en cours d’exécution dans un environnement autre que `Development`. Par exemple, `Production` l’exécution ou `Staging` déclenche le rendu de ces feuilles de style:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Consommer bundleconfig.json de Gulp

Il y a des cas où le flux de travail de regroupement et de minification d’une application nécessite un traitement supplémentaire. Les exemples incluent l’optimisation des images, le busting de cache, et le traitement d’actifs CDN. Pour répondre à ces exigences, vous pouvez convertir le flux de travail de regroupement et de minification pour utiliser Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Utilisez l’extension Bundler & Minifier

L’extension Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) gère la conversion en Gulp.

> [!NOTE]
> L’extension Bundler & Minifier appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support. Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).

Cliquez à droite sur le fichier *bundleconfig.json* dans Solution Explorer et sélectionnez **Bundler & Minifier** > **Convert To Gulp...**:

![Convertir à Gulp élément de menu contexte](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Les fichiers *gulpfile.js* et *package.json* sont ajoutés au projet. Les paquets [npm](https://www.npmjs.com/) de soutien énumérés `devDependencies` dans la section du fichier *package.json* sont installés.

Exécutez la commande suivante dans la fenêtre PMC pour installer le Gulp CLI comme une dépendance mondiale :

```console
npm i -g gulp-cli
```

Le fichier *gulpfile.js* lit le fichier *bundleconfig.json* pour les entrées, les sorties et les paramètres.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Convertir manuellement

Si Visual Studio et/ou l’extension Bundler & Minifier ne sont pas disponibles, convertissez manuellement.

Ajouter un fichier *package.json,* avec ce qui suit, `devDependencies`à la racine du projet:

> [!WARNING]
> Le `gulp-uglify` module ne prend pas en charge ECMAScript (ES) 2015 / ES6 et plus tard. Installez [le goux-terser](https://www.npmjs.com/package/gulp-terser) au lieu d’utiliser `gulp-uglify` ES2015 / ES6 ou plus tard.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installez les dépendances en exécutant la commande suivante au même niveau que *package.json*:

```console
npm i
```

Installer l’IMC Gulp comme dépendance mondiale :

```console
npm i -g gulp-cli
```

Copiez le fichier *gulpfile.js* ci-dessous sur la racine du projet :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Exécuter les tâches Gulp

Pour déclencher la tâche de minification Gulp avant la construction du projet dans Visual Studio, ajoutez la [cible MSBuild](/visualstudio/msbuild/msbuild-targets) suivante au fichier '.csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Dans cet exemple, toutes `MyPreCompileTarget` les tâches définies dans `Build` la cible s’exécutent avant la cible prédéfinie. Sortie similaire à ce qui suit apparaît dans la fenêtre de sortie de Visual Studio :

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utiliser Grunt](xref:client-side/using-grunt)
* [Utiliser plusieurs environnements](xref:fundamentals/environments)
* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
