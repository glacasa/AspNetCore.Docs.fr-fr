---
title: Utiliser Grunt dans ASP.NET Core
author: rick-anderson
description: Utiliser Grunt dans ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657590"
---
# <a name="use-grunt-in-aspnet-core"></a>Utiliser Grunt dans ASP.NET Core

Grunt est un coureur de tâches JavaScript qui automatise la minification des scripts, la compilation TypeScript, la qualité du code "lint" outils, CSS pré-processeurs, et à peu près n’importe quelle corvée répétitive qui doit faire pour soutenir le développement des clients. Grunt est entièrement pris en charge dans Visual Studio.

Cet exemple utilise un projet de base ASP.NET vide comme point de départ, pour montrer comment automatiser le processus de construction du client à partir de zéro.

L’exemple terminé nettoie l’annuaire de déploiement cible, combine les fichiers JavaScript, vérifie la qualité du code, condense le contenu du fichier JavaScript et se déploie à la racine de votre application web. Nous utiliserons les paquets suivants :

* **grognement**: Le paquet de coureur de tâches De Grunt.

* **grunt-contrib-clean**: Un plugin qui supprime les fichiers ou les répertoires.

* **grunt-contrib-jshint**: Un plugin qui passe en revue la qualité du code JavaScript.

* **grunt-contrib-concat**: Un plugin qui joint les fichiers dans un seul fichier.

* **grunt-contrib-uglify**: Un plugin qui minifies JavaScript pour réduire la taille.

* **grunt-contrib-watch**: Un plugin qui surveille l’activité des fichiers.

## <a name="preparing-the-application"></a>Préparation de l’application

Pour commencer, configurez une nouvelle application Web vide et ajoutez des fichiers d’exemple TypeScript. Les fichiers TypeScript sont automatiquement compilés dans JavaScript à l’aide de paramètres Visual Studio par défaut et seront notre matière première à traiter à l’aide de Grunt.

1. Dans Visual Studio, `ASP.NET Web Application`créez un nouveau .

2. Dans le dialogue **New ASP.NET Project,** sélectionnez le modèle ASP.NET Core **Empty** et cliquez sur le bouton OK.

3. Dans la solution Explorer, examinez la structure du projet. Le `\src` dossier comprend `wwwroot` `Dependencies` des nœuds vides et des nœuds.

    ![solution web vide](using-grunt/_static/grunt-solution-explorer.png)

4. Ajoutez un nouveau `TypeScript` dossier nommé à votre répertoire de projet.

5. Avant d’ajouter des fichiers, assurez-vous que Visual Studio dispose de la possibilité de « compiler sur l’enregistrement » pour les fichiers TypeScript vérifiés. Naviguez vers **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:

    ![options de réglage de la compilation automatique des fichiers TypeScript](using-grunt/_static/typescript-options.png)

6. Cliquez à `TypeScript` droite sur l’annuaire et sélectionnez **Ajouter > Nouvel Élément** du menu contextuelle. Sélectionnez l’élément **de fichier JavaScript** et nommez \*le fichier *Tastes.ts* (notez l’extension .ts). Copiez la ligne de code TypeScript ci-dessous dans le fichier (lorsque vous enregistrez, un nouveau fichier *Tastes.js* apparaîtra avec la source JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Ajoutez un deuxième fichier à l’annuaire `Food.ts` **TypeScript** et nommez-le . Copiez le code ci-dessous dans le fichier.

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>Configurer NPM

Ensuite, configurez NPM pour télécharger grognement et grognement-tâches.

1. Dans l’Explorer Solution, cliquez à droite sur le projet et **sélectionnez Ajouter > nouvel élément** du menu contextuelle. Sélectionnez l’élément **de fichier de configuration NPM,** laissez le nom par défaut, *package.json*et cliquez sur le bouton **Ajouter.**

2. Dans le fichier *package.json,* à l’intérieur des `devDependencies` accolades d’objets, entrez "grunt". Sélectionnez `grunt` dans la liste Intellisense et appuyez sur la clé Enter. Visual Studio citera le nom du paquet grognement, et ajoutera un côlon. A droite du côlon, sélectionnez la dernière version stable du paquet en haut `Ctrl-Space` de la liste Intellisense (appuyez si Intellisense n’apparaît pas).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM utilise [la version sémantique](https://semver.org/) pour organiser les dépendances. La version sémantique, également connue sous le nom de \<SemVer, identifie les paquets avec le schéma de numérotation majeur>. \<> mineures. \<patch>. Intellisense simplifie la version sémantique en ne montrant que quelques choix communs. L’élément supérieur de la liste Intellisense (0,4,5 dans l’exemple ci-dessus) est considéré comme la dernière version stable du paquet. Le symbole caret correspond à la version majeure la plus récente et le tilde correspond à la version mineure la plus récente. Voir la [référence de parser de version de semver de NPM](https://www.npmjs.com/package/semver) comme un guide de l’expressivité complète que SemVer fournit.

3. Ajouter plus de dépendances pour charger\* grunt-contrib- paquets pour *nettoyer*, *jshint*, *concat*, *uglify*, et *regarder* comme indiqué dans l’exemple ci-dessous. Les versions n’ont pas besoin de correspondre à l’exemple.

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. Enregistrer le fichier *package.json.*

Les paquets `devDependencies` pour chaque élément seront téléchargés, ainsi que tous les fichiers que chaque paquet nécessite. Vous pouvez trouver les fichiers de paquet dans *l’annuaire node_modules* en permettant le bouton **Afficher tous les fichiers** dans Solution **Explorer**.

![grognement node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Si vous en avez besoin, vous pouvez restaurer manuellement les `Dependencies\NPM` dépendances dans Solution **Explorer** en cliquant à droite et en sélectionnant l’option de menu Restore **Packages.**

![restaurer les paquets](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurer Grunt

Grunt est configuré à l’aide d’un manifeste nommé *Gruntfile.js* qui définit, charge et enregistre les tâches qui peuvent être exécutées manuellement ou configurées pour s’exécuter automatiquement en fonction des événements de Visual Studio.

1. Cliquez à droite sur le projet et sélectionnez **Ajouter** > **un nouvel article**. Sélectionnez le modèle **d’élément de fichier JavaScript,** changez le nom en *Gruntfile.js*et cliquez sur le bouton **Ajouter.**

1. Ajoutez le code suivant à *Gruntfile.js*. La `initConfig` fonction définit les options pour chaque paquet, et le reste des charges du module et les tâches d’enregistrement.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. À `initConfig` l’intérieur de `clean` la fonction, ajoutez des options pour la tâche comme le montre l’exemple *Gruntfile.js* ci-dessous. La `clean` tâche accepte un éventail de chaînes d’annuaire. Cette tâche supprime les fichiers de *wwwroot/lib* et supprime l’ensemble de l’annuaire */temp.*

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Au-dessous de la `initConfig` `grunt.loadNpmTasks`fonction, ajoutez un appel à . Cela rendra la tâche runnable de Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Enregistrer *Gruntfile.js*. Le fichier devrait ressembler à quelque chose comme la capture d’écran ci-dessous.

    ![gruntfile initial](using-grunt/_static/gruntfile-js-initial.png)

1. Cliquez à droite *Sur Gruntfile.js* et sélectionnez **Task Runner Explorer** dans le menu context. La fenêtre **Task Runner Explorer** s’ouvrira.

    ![menu explorateur coureur de tâche](using-grunt/_static/task-runner-explorer-menu.png)

1. Vérifier `clean` que montre sous **les tâches** dans le **Task Runner Explorer**.

    ![liste de tâches d’explorateur de coureur de tâche](using-grunt/_static/task-runner-explorer-tasks.png)

1. Cliquez à droite sur la tâche propre et **sélectionnez Exécuter** à partir du menu contexte. Une fenêtre de commande affiche l’avancement de la tâche.

    ![explorateur coureur de tâche exécuter la tâche propre](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Il n’y a pas encore de fichiers ou d’annuaires à nettoyer. Si vous le souhaitez, vous pouvez les créer manuellement dans l’Explorer Solution et ensuite exécuter la tâche propre comme un test.

1. Dans `initConfig` la fonction, ajoutez `concat` une entrée pour l’utilisation du code ci-dessous.

    Le `src` tableau de propriété répertorie les fichiers à combiner, dans l’ordre qu’ils doivent être combinés. La `dest` propriété attribue le chemin au fichier combiné qui est produit.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > La `all` propriété dans le code ci-dessus est le nom d’une cible. Les cibles sont utilisées dans certaines tâches Grunt pour permettre plusieurs environnements de construction. Vous pouvez afficher les cibles intégrées à l’aide d’IntelliSense ou attribuer les vôtres.

1. Ajoutez `jshint` la tâche à l’aide du code ci-dessous.

    L’utilitaire `code-quality` jshint est exécuté contre tous les fichiers JavaScript trouvés dans le répertoire *temporaire.*

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > L’option "-W069" est une erreur produite par jshint lorsque JavaScript utilise la syntaxe de parenthèse pour attribuer une propriété au lieu de notation point, `Tastes["Sweet"]` c’est-à-dire au lieu de `Tastes.Sweet`. L’option éteint l’avertissement pour permettre au reste du processus de continuer.

1. Ajoutez `uglify` la tâche à l’aide du code ci-dessous.

    La tâche minifies le fichier *combined.js* trouvé dans l’annuaire temporaire et crée le fichier de résultat dans wwwroot/lib suivant le nom * \<\>* standard de fichier de convention de nom .min.js .

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Sous l’appel `grunt.loadNpmTasks` à `grunt-contrib-clean`ces charges , inclure le même appel pour jshint, concat, et uglify en utilisant le code ci-dessous.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Enregistrer *Gruntfile.js*. Le fichier devrait ressembler à l’exemple ci-dessous.

    ![exemple complet de fichier grognement](using-grunt/_static/gruntfile-js-complete.png)

1. Notez que la liste `clean`des `concat` `jshint` tâches `uglify` **Task Runner Explorer** Comprend , et les tâches. Exécutez chaque tâche dans l’ordre et observez les résultats dans **Solution Explorer**. Chaque tâche doit s’exécuter sans erreurs.

    ![explorateur coureur de tâche exécuter chaque tâche](using-grunt/_static/task-runner-explorer-run-each-task.png)

    La tâche concat crée un nouveau fichier *combined.js* et le place dans le répertoire temporaire. La `jshint` tâche s’exécute tout simplement et ne produit pas de sortie. La `uglify` tâche crée un nouveau fichier *combined.min.js* et le place dans *wwwroot/lib*. À l’achèvement, la solution devrait ressembler à quelque chose comme la capture d’écran ci-dessous:

    ![explorateur de solution après toutes les tâches](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Pour plus d’informations sur les [https://www.npmjs.com/](https://www.npmjs.com/) options pour chaque paquet, visitez et recherchez le nom du paquet dans la boîte de recherche sur la page principale. Par exemple, vous pouvez rechercher le paquet grognement-contrib-propre pour obtenir un lien de documentation qui explique tous ses paramètres.

### <a name="all-together-now"></a>Récapitulons

Utilisez la `registerTask()` méthode Grunt pour exécuter une série de tâches dans une séquence particulière. Par exemple, pour exécuter l’exemple étapes ci-dessus dans l’ordre propre -> concat -> jshint -> uglify, ajouter le code ci-dessous au module. Le code doit être ajouté au même niveau que les appels loadNpmTasks() à l’extérieur initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

La nouvelle tâche apparaît dans Task Runner Explorer sous Alias Tasks. Vous pouvez cliquer à droite et l’exécuter comme vous le feriez d’autres tâches. La `all` tâche `clean`s’exécutera `concat`, , `jshint` et `uglify`, dans l’ordre.

![tâches de grognement alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Détection des changements

Une `watch` tâche garde un œil sur les fichiers et les répertoires. La montre déclenche automatiquement les tâches si elle détecte les changements. Ajoutez le code ci-dessous à initConfig pour regarder les modifications apportées aux \*fichiers .js dans le répertoire TypeScript. Si un fichier JavaScript `watch` est `all` modifié, exécutera la tâche.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Ajoutez un `loadNpmTasks()` appel pour `watch` montrer la tâche dans Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Cliquez à droite sur la tâche de montre dans Task Runner Explorer et sélectionnez Run à partir du menu context. La fenêtre de commande qui montre la tâche de montre en cours d’exécution affichera un "Waiting..." Message. Ouvrez l’un des fichiers TypeScript, ajoutez un espace, puis enregistrez le fichier. Cela déclenchera la tâche de la montre et déclenchera les autres tâches à exécuter dans l’ordre. La capture d’écran ci-dessous montre un échantillon d’exécution.

![exécution des tâches de sortie](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Liaison aux événements Visual Studio

Sauf si vous voulez commencer manuellement vos tâches chaque fois que vous travaillez dans Visual Studio, lier les tâches à **Before Build**, After **Build**, **Clean**, et **Project Open** événements.

Bind `watch` de sorte qu’il fonctionne chaque fois que Visual Studio s’ouvre. Dans Task Runner Explorer, cliquez à droite sur la tâche de la montre et **sélectionnez Bindings** > **Project Open** à partir du menu context.

![lier une tâche à l’ouverture du projet](using-grunt/_static/bindings-project-open.png)

Déchargez et rechargez le projet. Lorsque le projet se charge à nouveau, la tâche de la montre commence à s’exécuter automatiquement.

## <a name="summary"></a>Résumé

Grunt est un coureur de tâches puissant qui peut être utilisé pour automatiser la plupart des tâches de conception client. Grunt tire parti de NPM pour livrer ses paquets, et propose l’intégration d’outillage avec Visual Studio. Visual Studio’s Task Runner Explorer détecte les modifications apportées aux fichiers de configuration et fournit une interface pratique pour exécuter les tâches, afficher les tâches en cours d’exécution et lier les tâches aux événements Visual Studio.
