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
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="e220d-103">Utiliser Grunt dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e220d-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="e220d-104">Grunt est un coureur de tâches JavaScript qui automatise la minification des scripts, la compilation TypeScript, la qualité du code "lint" outils, CSS pré-processeurs, et à peu près n’importe quelle corvée répétitive qui doit faire pour soutenir le développement des clients.</span><span class="sxs-lookup"><span data-stu-id="e220d-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="e220d-105">Grunt est entièrement pris en charge dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e220d-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="e220d-106">Cet exemple utilise un projet de base ASP.NET vide comme point de départ, pour montrer comment automatiser le processus de construction du client à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="e220d-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="e220d-107">L’exemple terminé nettoie l’annuaire de déploiement cible, combine les fichiers JavaScript, vérifie la qualité du code, condense le contenu du fichier JavaScript et se déploie à la racine de votre application web.</span><span class="sxs-lookup"><span data-stu-id="e220d-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="e220d-108">Nous utiliserons les paquets suivants :</span><span class="sxs-lookup"><span data-stu-id="e220d-108">We will use the following packages:</span></span>

* <span data-ttu-id="e220d-109">**grognement**: Le paquet de coureur de tâches De Grunt.</span><span class="sxs-lookup"><span data-stu-id="e220d-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="e220d-110">**grunt-contrib-clean**: Un plugin qui supprime les fichiers ou les répertoires.</span><span class="sxs-lookup"><span data-stu-id="e220d-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="e220d-111">**grunt-contrib-jshint**: Un plugin qui passe en revue la qualité du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e220d-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="e220d-112">**grunt-contrib-concat**: Un plugin qui joint les fichiers dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="e220d-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="e220d-113">**grunt-contrib-uglify**: Un plugin qui minifies JavaScript pour réduire la taille.</span><span class="sxs-lookup"><span data-stu-id="e220d-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="e220d-114">**grunt-contrib-watch**: Un plugin qui surveille l’activité des fichiers.</span><span class="sxs-lookup"><span data-stu-id="e220d-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="e220d-115">Préparation de l’application</span><span class="sxs-lookup"><span data-stu-id="e220d-115">Preparing the application</span></span>

<span data-ttu-id="e220d-116">Pour commencer, configurez une nouvelle application Web vide et ajoutez des fichiers d’exemple TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e220d-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="e220d-117">Les fichiers TypeScript sont automatiquement compilés dans JavaScript à l’aide de paramètres Visual Studio par défaut et seront notre matière première à traiter à l’aide de Grunt.</span><span class="sxs-lookup"><span data-stu-id="e220d-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="e220d-118">Dans Visual Studio, `ASP.NET Web Application`créez un nouveau .</span><span class="sxs-lookup"><span data-stu-id="e220d-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="e220d-119">Dans le dialogue **New ASP.NET Project,** sélectionnez le modèle ASP.NET Core **Empty** et cliquez sur le bouton OK.</span><span class="sxs-lookup"><span data-stu-id="e220d-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="e220d-120">Dans la solution Explorer, examinez la structure du projet.</span><span class="sxs-lookup"><span data-stu-id="e220d-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="e220d-121">Le `\src` dossier comprend `wwwroot` `Dependencies` des nœuds vides et des nœuds.</span><span class="sxs-lookup"><span data-stu-id="e220d-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solution web vide](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="e220d-123">Ajoutez un nouveau `TypeScript` dossier nommé à votre répertoire de projet.</span><span class="sxs-lookup"><span data-stu-id="e220d-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="e220d-124">Avant d’ajouter des fichiers, assurez-vous que Visual Studio dispose de la possibilité de « compiler sur l’enregistrement » pour les fichiers TypeScript vérifiés.</span><span class="sxs-lookup"><span data-stu-id="e220d-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="e220d-125">Naviguez vers **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span><span class="sxs-lookup"><span data-stu-id="e220d-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![options de réglage de la compilation automatique des fichiers TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="e220d-127">Cliquez à `TypeScript` droite sur l’annuaire et sélectionnez **Ajouter > Nouvel Élément** du menu contextuelle.</span><span class="sxs-lookup"><span data-stu-id="e220d-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e220d-128">Sélectionnez l’élément **de fichier JavaScript** et nommez \*le fichier *Tastes.ts* (notez l’extension .ts).</span><span class="sxs-lookup"><span data-stu-id="e220d-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="e220d-129">Copiez la ligne de code TypeScript ci-dessous dans le fichier (lorsque vous enregistrez, un nouveau fichier *Tastes.js* apparaîtra avec la source JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e220d-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="e220d-130">Ajoutez un deuxième fichier à l’annuaire `Food.ts` **TypeScript** et nommez-le .</span><span class="sxs-lookup"><span data-stu-id="e220d-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="e220d-131">Copiez le code ci-dessous dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="e220d-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="e220d-132">Configurer NPM</span><span class="sxs-lookup"><span data-stu-id="e220d-132">Configuring NPM</span></span>

<span data-ttu-id="e220d-133">Ensuite, configurez NPM pour télécharger grognement et grognement-tâches.</span><span class="sxs-lookup"><span data-stu-id="e220d-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="e220d-134">Dans l’Explorer Solution, cliquez à droite sur le projet et **sélectionnez Ajouter > nouvel élément** du menu contextuelle.</span><span class="sxs-lookup"><span data-stu-id="e220d-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e220d-135">Sélectionnez l’élément **de fichier de configuration NPM,** laissez le nom par défaut, *package.json*et cliquez sur le bouton **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="e220d-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="e220d-136">Dans le fichier *package.json,* à l’intérieur des `devDependencies` accolades d’objets, entrez "grunt".</span><span class="sxs-lookup"><span data-stu-id="e220d-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="e220d-137">Sélectionnez `grunt` dans la liste Intellisense et appuyez sur la clé Enter.</span><span class="sxs-lookup"><span data-stu-id="e220d-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="e220d-138">Visual Studio citera le nom du paquet grognement, et ajoutera un côlon.</span><span class="sxs-lookup"><span data-stu-id="e220d-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="e220d-139">A droite du côlon, sélectionnez la dernière version stable du paquet en haut `Ctrl-Space` de la liste Intellisense (appuyez si Intellisense n’apparaît pas).</span><span class="sxs-lookup"><span data-stu-id="e220d-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="e220d-141">NPM utilise [la version sémantique](https://semver.org/) pour organiser les dépendances.</span><span class="sxs-lookup"><span data-stu-id="e220d-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="e220d-142">La version sémantique, également connue sous le nom de \<SemVer, identifie les paquets avec le schéma de numérotation majeur>. \<> mineures. \<patch>.</span><span class="sxs-lookup"><span data-stu-id="e220d-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="e220d-143">Intellisense simplifie la version sémantique en ne montrant que quelques choix communs.</span><span class="sxs-lookup"><span data-stu-id="e220d-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="e220d-144">L’élément supérieur de la liste Intellisense (0,4,5 dans l’exemple ci-dessus) est considéré comme la dernière version stable du paquet.</span><span class="sxs-lookup"><span data-stu-id="e220d-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="e220d-145">Le symbole caret correspond à la version majeure la plus récente et le tilde correspond à la version mineure la plus récente.</span><span class="sxs-lookup"><span data-stu-id="e220d-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="e220d-146">Voir la [référence de parser de version de semver de NPM](https://www.npmjs.com/package/semver) comme un guide de l’expressivité complète que SemVer fournit.</span><span class="sxs-lookup"><span data-stu-id="e220d-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="e220d-147">Ajouter plus de dépendances pour charger\* grunt-contrib- paquets pour *nettoyer*, *jshint*, *concat*, *uglify*, et *regarder* comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="e220d-148">Les versions n’ont pas besoin de correspondre à l’exemple.</span><span class="sxs-lookup"><span data-stu-id="e220d-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="e220d-149">Enregistrer le fichier *package.json.*</span><span class="sxs-lookup"><span data-stu-id="e220d-149">Save the *package.json* file.</span></span>

<span data-ttu-id="e220d-150">Les paquets `devDependencies` pour chaque élément seront téléchargés, ainsi que tous les fichiers que chaque paquet nécessite.</span><span class="sxs-lookup"><span data-stu-id="e220d-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="e220d-151">Vous pouvez trouver les fichiers de paquet dans *l’annuaire node_modules* en permettant le bouton **Afficher tous les fichiers** dans Solution **Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e220d-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grognement node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="e220d-153">Si vous en avez besoin, vous pouvez restaurer manuellement les `Dependencies\NPM` dépendances dans Solution **Explorer** en cliquant à droite et en sélectionnant l’option de menu Restore **Packages.**</span><span class="sxs-lookup"><span data-stu-id="e220d-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurer les paquets](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="e220d-155">Configurer Grunt</span><span class="sxs-lookup"><span data-stu-id="e220d-155">Configuring Grunt</span></span>

<span data-ttu-id="e220d-156">Grunt est configuré à l’aide d’un manifeste nommé *Gruntfile.js* qui définit, charge et enregistre les tâches qui peuvent être exécutées manuellement ou configurées pour s’exécuter automatiquement en fonction des événements de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e220d-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="e220d-157">Cliquez à droite sur le projet et sélectionnez **Ajouter** > **un nouvel article**.</span><span class="sxs-lookup"><span data-stu-id="e220d-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="e220d-158">Sélectionnez le modèle **d’élément de fichier JavaScript,** changez le nom en *Gruntfile.js*et cliquez sur le bouton **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="e220d-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="e220d-159">Ajoutez le code suivant à *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e220d-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="e220d-160">La `initConfig` fonction définit les options pour chaque paquet, et le reste des charges du module et les tâches d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="e220d-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="e220d-161">À `initConfig` l’intérieur de `clean` la fonction, ajoutez des options pour la tâche comme le montre l’exemple *Gruntfile.js* ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="e220d-162">La `clean` tâche accepte un éventail de chaînes d’annuaire.</span><span class="sxs-lookup"><span data-stu-id="e220d-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="e220d-163">Cette tâche supprime les fichiers de *wwwroot/lib* et supprime l’ensemble de l’annuaire */temp.*</span><span class="sxs-lookup"><span data-stu-id="e220d-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="e220d-164">Au-dessous de la `initConfig` `grunt.loadNpmTasks`fonction, ajoutez un appel à .</span><span class="sxs-lookup"><span data-stu-id="e220d-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="e220d-165">Cela rendra la tâche runnable de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e220d-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="e220d-166">Enregistrer *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e220d-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e220d-167">Le fichier devrait ressembler à quelque chose comme la capture d’écran ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-167">The file should look something like the screenshot below.</span></span>

    ![gruntfile initial](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="e220d-169">Cliquez à droite *Sur Gruntfile.js* et sélectionnez **Task Runner Explorer** dans le menu context.</span><span class="sxs-lookup"><span data-stu-id="e220d-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="e220d-170">La fenêtre **Task Runner Explorer** s’ouvrira.</span><span class="sxs-lookup"><span data-stu-id="e220d-170">The **Task Runner Explorer** window will open.</span></span>

    ![menu explorateur coureur de tâche](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="e220d-172">Vérifier `clean` que montre sous **les tâches** dans le **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e220d-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![liste de tâches d’explorateur de coureur de tâche](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="e220d-174">Cliquez à droite sur la tâche propre et **sélectionnez Exécuter** à partir du menu contexte.</span><span class="sxs-lookup"><span data-stu-id="e220d-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="e220d-175">Une fenêtre de commande affiche l’avancement de la tâche.</span><span class="sxs-lookup"><span data-stu-id="e220d-175">A command window displays progress of the task.</span></span>

    ![explorateur coureur de tâche exécuter la tâche propre](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="e220d-177">Il n’y a pas encore de fichiers ou d’annuaires à nettoyer.</span><span class="sxs-lookup"><span data-stu-id="e220d-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="e220d-178">Si vous le souhaitez, vous pouvez les créer manuellement dans l’Explorer Solution et ensuite exécuter la tâche propre comme un test.</span><span class="sxs-lookup"><span data-stu-id="e220d-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="e220d-179">Dans `initConfig` la fonction, ajoutez `concat` une entrée pour l’utilisation du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="e220d-180">Le `src` tableau de propriété répertorie les fichiers à combiner, dans l’ordre qu’ils doivent être combinés.</span><span class="sxs-lookup"><span data-stu-id="e220d-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="e220d-181">La `dest` propriété attribue le chemin au fichier combiné qui est produit.</span><span class="sxs-lookup"><span data-stu-id="e220d-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e220d-182">La `all` propriété dans le code ci-dessus est le nom d’une cible.</span><span class="sxs-lookup"><span data-stu-id="e220d-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="e220d-183">Les cibles sont utilisées dans certaines tâches Grunt pour permettre plusieurs environnements de construction.</span><span class="sxs-lookup"><span data-stu-id="e220d-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="e220d-184">Vous pouvez afficher les cibles intégrées à l’aide d’IntelliSense ou attribuer les vôtres.</span><span class="sxs-lookup"><span data-stu-id="e220d-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="e220d-185">Ajoutez `jshint` la tâche à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="e220d-186">L’utilitaire `code-quality` jshint est exécuté contre tous les fichiers JavaScript trouvés dans le répertoire *temporaire.*</span><span class="sxs-lookup"><span data-stu-id="e220d-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e220d-187">L’option "-W069" est une erreur produite par jshint lorsque JavaScript utilise la syntaxe de parenthèse pour attribuer une propriété au lieu de notation point, `Tastes["Sweet"]` c’est-à-dire au lieu de `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="e220d-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="e220d-188">L’option éteint l’avertissement pour permettre au reste du processus de continuer.</span><span class="sxs-lookup"><span data-stu-id="e220d-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="e220d-189">Ajoutez `uglify` la tâche à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="e220d-190">La tâche minifies le fichier *combined.js* trouvé dans l’annuaire temporaire et crée le fichier de résultat dans wwwroot/lib suivant le nom \* \<\>\* standard de fichier de convention de nom .min.js .</span><span class="sxs-lookup"><span data-stu-id="e220d-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="e220d-191">Sous l’appel `grunt.loadNpmTasks` à `grunt-contrib-clean`ces charges , inclure le même appel pour jshint, concat, et uglify en utilisant le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="e220d-192">Enregistrer *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e220d-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e220d-193">Le fichier devrait ressembler à l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e220d-193">The file should look something like the example below.</span></span>

    ![exemple complet de fichier grognement](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="e220d-195">Notez que la liste `clean`des `concat` `jshint` tâches `uglify` **Task Runner Explorer** Comprend , et les tâches.</span><span class="sxs-lookup"><span data-stu-id="e220d-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="e220d-196">Exécutez chaque tâche dans l’ordre et observez les résultats dans **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e220d-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="e220d-197">Chaque tâche doit s’exécuter sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="e220d-197">Each task should run without errors.</span></span>

    ![explorateur coureur de tâche exécuter chaque tâche](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="e220d-199">La tâche concat crée un nouveau fichier *combined.js* et le place dans le répertoire temporaire.</span><span class="sxs-lookup"><span data-stu-id="e220d-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="e220d-200">La `jshint` tâche s’exécute tout simplement et ne produit pas de sortie.</span><span class="sxs-lookup"><span data-stu-id="e220d-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="e220d-201">La `uglify` tâche crée un nouveau fichier *combined.min.js* et le place dans *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="e220d-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="e220d-202">À l’achèvement, la solution devrait ressembler à quelque chose comme la capture d’écran ci-dessous:</span><span class="sxs-lookup"><span data-stu-id="e220d-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![explorateur de solution après toutes les tâches](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="e220d-204">Pour plus d’informations sur les [https://www.npmjs.com/](https://www.npmjs.com/) options pour chaque paquet, visitez et recherchez le nom du paquet dans la boîte de recherche sur la page principale.</span><span class="sxs-lookup"><span data-stu-id="e220d-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="e220d-205">Par exemple, vous pouvez rechercher le paquet grognement-contrib-propre pour obtenir un lien de documentation qui explique tous ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="e220d-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="e220d-206">Récapitulons</span><span class="sxs-lookup"><span data-stu-id="e220d-206">All together now</span></span>

<span data-ttu-id="e220d-207">Utilisez la `registerTask()` méthode Grunt pour exécuter une série de tâches dans une séquence particulière.</span><span class="sxs-lookup"><span data-stu-id="e220d-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="e220d-208">Par exemple, pour exécuter l’exemple étapes ci-dessus dans l’ordre propre -> concat -> jshint -> uglify, ajouter le code ci-dessous au module.</span><span class="sxs-lookup"><span data-stu-id="e220d-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="e220d-209">Le code doit être ajouté au même niveau que les appels loadNpmTasks() à l’extérieur initConfig.</span><span class="sxs-lookup"><span data-stu-id="e220d-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="e220d-210">La nouvelle tâche apparaît dans Task Runner Explorer sous Alias Tasks.</span><span class="sxs-lookup"><span data-stu-id="e220d-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="e220d-211">Vous pouvez cliquer à droite et l’exécuter comme vous le feriez d’autres tâches.</span><span class="sxs-lookup"><span data-stu-id="e220d-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="e220d-212">La `all` tâche `clean`s’exécutera `concat`, , `jshint` et `uglify`, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="e220d-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tâches de grognement alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="e220d-214">Détection des changements</span><span class="sxs-lookup"><span data-stu-id="e220d-214">Watching for changes</span></span>

<span data-ttu-id="e220d-215">Une `watch` tâche garde un œil sur les fichiers et les répertoires.</span><span class="sxs-lookup"><span data-stu-id="e220d-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="e220d-216">La montre déclenche automatiquement les tâches si elle détecte les changements.</span><span class="sxs-lookup"><span data-stu-id="e220d-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="e220d-217">Ajoutez le code ci-dessous à initConfig pour regarder les modifications apportées aux \*fichiers .js dans le répertoire TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e220d-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="e220d-218">Si un fichier JavaScript `watch` est `all` modifié, exécutera la tâche.</span><span class="sxs-lookup"><span data-stu-id="e220d-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="e220d-219">Ajoutez un `loadNpmTasks()` appel pour `watch` montrer la tâche dans Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="e220d-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="e220d-220">Cliquez à droite sur la tâche de montre dans Task Runner Explorer et sélectionnez Run à partir du menu context.</span><span class="sxs-lookup"><span data-stu-id="e220d-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="e220d-221">La fenêtre de commande qui montre la tâche de montre en cours d’exécution affichera un "Waiting..." Message.</span><span class="sxs-lookup"><span data-stu-id="e220d-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="e220d-222">Ouvrez l’un des fichiers TypeScript, ajoutez un espace, puis enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="e220d-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="e220d-223">Cela déclenchera la tâche de la montre et déclenchera les autres tâches à exécuter dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="e220d-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="e220d-224">La capture d’écran ci-dessous montre un échantillon d’exécution.</span><span class="sxs-lookup"><span data-stu-id="e220d-224">The screenshot below shows a sample run.</span></span>

![exécution des tâches de sortie](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="e220d-226">Liaison aux événements Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e220d-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="e220d-227">Sauf si vous voulez commencer manuellement vos tâches chaque fois que vous travaillez dans Visual Studio, lier les tâches à **Before Build**, After **Build**, **Clean**, et **Project Open** événements.</span><span class="sxs-lookup"><span data-stu-id="e220d-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="e220d-228">Bind `watch` de sorte qu’il fonctionne chaque fois que Visual Studio s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="e220d-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="e220d-229">Dans Task Runner Explorer, cliquez à droite sur la tâche de la montre et **sélectionnez Bindings** > **Project Open** à partir du menu context.</span><span class="sxs-lookup"><span data-stu-id="e220d-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![lier une tâche à l’ouverture du projet](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="e220d-231">Déchargez et rechargez le projet.</span><span class="sxs-lookup"><span data-stu-id="e220d-231">Unload and reload the project.</span></span> <span data-ttu-id="e220d-232">Lorsque le projet se charge à nouveau, la tâche de la montre commence à s’exécuter automatiquement.</span><span class="sxs-lookup"><span data-stu-id="e220d-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="e220d-233">Résumé</span><span class="sxs-lookup"><span data-stu-id="e220d-233">Summary</span></span>

<span data-ttu-id="e220d-234">Grunt est un coureur de tâches puissant qui peut être utilisé pour automatiser la plupart des tâches de conception client.</span><span class="sxs-lookup"><span data-stu-id="e220d-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="e220d-235">Grunt tire parti de NPM pour livrer ses paquets, et propose l’intégration d’outillage avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e220d-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="e220d-236">Visual Studio’s Task Runner Explorer détecte les modifications apportées aux fichiers de configuration et fournit une interface pratique pour exécuter les tâches, afficher les tâches en cours d’exécution et lier les tâches aux événements Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e220d-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
