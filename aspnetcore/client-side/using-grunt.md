---
title: Utiliser grunt dans ASP.NET Core
author: rick-anderson
description: Utiliser grunt dans ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: b51973e82bb1bd382be68a501c40ba613217fb03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773638"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="5f5db-103">Utiliser grunt dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f5db-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="5f5db-104">Grunt est un testeur de tâches JavaScript qui automatise la minimisation de script, la compilation d’écriture automatique, les outils de qualité de code « Lint », les préprocesseurs CSS et tout ce qui est nécessaire pour prendre en charge le développement de clients.</span><span class="sxs-lookup"><span data-stu-id="5f5db-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="5f5db-105">Grunt est entièrement pris en charge dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5db-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="5f5db-106">Cet exemple utilise un projet de ASP.NET Core vide comme point de départ pour montrer comment automatiser le processus de génération du client à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="5f5db-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="5f5db-107">L’exemple terminé nettoie le répertoire de déploiement cible, combine des fichiers JavaScript, vérifie la qualité du code, condense le contenu du fichier JavaScript et le déploie à la racine de votre application Web.</span><span class="sxs-lookup"><span data-stu-id="5f5db-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="5f5db-108">Nous utiliserons les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="5f5db-108">We will use the following packages:</span></span>

* <span data-ttu-id="5f5db-109">**grunt**: le package grunt Task Runner.</span><span class="sxs-lookup"><span data-stu-id="5f5db-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="5f5db-110">**grunt-contrib-Clean**: plug-in qui supprime des fichiers ou des répertoires.</span><span class="sxs-lookup"><span data-stu-id="5f5db-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="5f5db-111">**grunt-contrib-jshint**: plug-in qui vérifie la qualité du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5f5db-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="5f5db-112">**grunt-concontribution-Concat**: plug-in qui joint des fichiers dans un fichier unique.</span><span class="sxs-lookup"><span data-stu-id="5f5db-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="5f5db-113">**grunt-contrib-uglify**: plug-in qui minimise JavaScript pour réduire la taille.</span><span class="sxs-lookup"><span data-stu-id="5f5db-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="5f5db-114">**grunt-contrib-Watch**: un plug-in qui surveille l’activité des fichiers.</span><span class="sxs-lookup"><span data-stu-id="5f5db-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="5f5db-115">Préparation de l’application</span><span class="sxs-lookup"><span data-stu-id="5f5db-115">Preparing the application</span></span>

<span data-ttu-id="5f5db-116">Pour commencer, configurez une nouvelle application Web vide et ajoutez des exemples de fichiers de machine à écrire.</span><span class="sxs-lookup"><span data-stu-id="5f5db-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="5f5db-117">Les fichiers de machine à écrire sont automatiquement compilés en JavaScript à l’aide des paramètres par défaut de Visual Studio et sont nos documents bruts à traiter à l’aide de Grunt.</span><span class="sxs-lookup"><span data-stu-id="5f5db-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="5f5db-118">Dans Visual Studio, créez un nouveau `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="5f5db-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="5f5db-119">Dans la boîte de dialogue **nouveau projet ASP.net** , sélectionnez le ASP.net Core modèle **vide** , puis cliquez sur le bouton OK.</span><span class="sxs-lookup"><span data-stu-id="5f5db-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="5f5db-120">Dans le Explorateur de solutions, passez en revue la structure du projet.</span><span class="sxs-lookup"><span data-stu-id="5f5db-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="5f5db-121">Le `\src` dossier comprend des `wwwroot` nœuds et `Dependencies` vides.</span><span class="sxs-lookup"><span data-stu-id="5f5db-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solution Web vide](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="5f5db-123">Ajoutez un nouveau dossier nommé `TypeScript` dans le répertoire de votre projet.</span><span class="sxs-lookup"><span data-stu-id="5f5db-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="5f5db-124">Avant d’ajouter des fichiers, assurez-vous que Visual Studio a l’option « compiler à l’enregistrement » pour les fichiers de machine à écrire activée.</span><span class="sxs-lookup"><span data-stu-id="5f5db-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="5f5db-125">Accédez à **Outils** > **options** > **éditeur** > de texte**projet**de**machine à écrire** > :</span><span class="sxs-lookup"><span data-stu-id="5f5db-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![options configuration de la compilation automatique des fichiers de définition d’une machine](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="5f5db-127">Cliquez avec le bouton `TypeScript` droit sur le répertoire et sélectionnez **Ajouter > nouvel élément** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5f5db-128">Sélectionnez l’élément de **fichier JavaScript** et nommez le fichier *goûts. TS* ( \*Notez l’extension. TS).</span><span class="sxs-lookup"><span data-stu-id="5f5db-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="5f5db-129">Copiez la ligne de code machine à écrire ci-dessous dans le fichier (lorsque vous enregistrez, un nouveau fichier *goûts. js* s’affiche avec la source JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5f5db-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="5f5db-130">Ajoutez un deuxième fichier au répertoire de la **machine** à écrire `Food.ts`et nommez-le.</span><span class="sxs-lookup"><span data-stu-id="5f5db-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="5f5db-131">Copiez le code ci-dessous dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="5f5db-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="5f5db-132">Configuration de NPM</span><span class="sxs-lookup"><span data-stu-id="5f5db-132">Configuring NPM</span></span>

<span data-ttu-id="5f5db-133">Ensuite, configurez NPM pour télécharger grunt et grunt-Tasks.</span><span class="sxs-lookup"><span data-stu-id="5f5db-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="5f5db-134">Dans le Explorateur de solutions, cliquez avec le bouton droit sur le projet et sélectionnez **ajouter > nouvel élément** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="5f5db-135">Sélectionnez l’élément de **fichier de configuration NPM** , laissez le nom par défaut *Package. JSON*, puis cliquez sur le bouton **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="5f5db-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="5f5db-136">Dans le fichier *Package. JSON* , à l' `devDependencies` intérieur des accolades d’objet, entrez « grunt ».</span><span class="sxs-lookup"><span data-stu-id="5f5db-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="5f5db-137">Sélectionnez `grunt` dans la liste IntelliSense et appuyez sur la touche entrée.</span><span class="sxs-lookup"><span data-stu-id="5f5db-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="5f5db-138">Visual Studio indiquera le nom du package grunt et ajoutera un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="5f5db-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="5f5db-139">À droite du signe deux-points, sélectionnez la dernière version stable du package en haut de la liste IntelliSense (appuyez sur `Ctrl-Space` si IntelliSense n’apparaît pas).</span><span class="sxs-lookup"><span data-stu-id="5f5db-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="5f5db-141">NPM utilise le contrôle de [version sémantique](https://semver.org/) pour organiser les dépendances.</span><span class="sxs-lookup"><span data-stu-id="5f5db-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="5f5db-142">Le contrôle de version sémantique, également appelé SemVer, identifie les packages avec la> \<principale du schéma de numérotation. \<> mineur. \<> de correctifs.</span><span class="sxs-lookup"><span data-stu-id="5f5db-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="5f5db-143">IntelliSense simplifie le contrôle de version sémantique en n’apparaissant que quelques choix courants.</span><span class="sxs-lookup"><span data-stu-id="5f5db-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="5f5db-144">L’élément supérieur dans la liste IntelliSense (0.4.5 dans l’exemple ci-dessus) est considéré comme la dernière version stable du package.</span><span class="sxs-lookup"><span data-stu-id="5f5db-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="5f5db-145">Le signe insertion (^) correspond à la version principale la plus récente et le tilde (~) correspond à la version mineure la plus récente.</span><span class="sxs-lookup"><span data-stu-id="5f5db-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="5f5db-146">Consultez la [référence de l’analyseur de version NPM semver](https://www.npmjs.com/package/semver) en tant que guide de l’expression complète fournie par semver.</span><span class="sxs-lookup"><span data-stu-id="5f5db-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="5f5db-147">Ajoutez d’autres dépendances pour charger grunt-\* contrib-packages pour *Clean*, *jshint*, *concat*, *uglify*et *Watch* comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="5f5db-148">Les versions n’ont pas besoin de correspondre à l’exemple.</span><span class="sxs-lookup"><span data-stu-id="5f5db-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="5f5db-149">Enregistrez le fichier *Package. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5f5db-149">Save the *package.json* file.</span></span>

<span data-ttu-id="5f5db-150">Les packages pour chaque `devDependencies` élément seront téléchargés, ainsi que tous les fichiers requis par chaque package.</span><span class="sxs-lookup"><span data-stu-id="5f5db-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="5f5db-151">Vous pouvez trouver les fichiers du package dans le répertoire *node_modules* en activant le bouton **Afficher tous les fichiers** dans **Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="5f5db-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="5f5db-153">Si nécessaire, vous pouvez restaurer manuellement les dépendances dans **Explorateur de solutions** en cliquant avec le bouton `Dependencies\NPM` droit sur et en sélectionnant l’option de menu **restaurer les packages** .</span><span class="sxs-lookup"><span data-stu-id="5f5db-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurer les packages](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="5f5db-155">Configuration de Grunt</span><span class="sxs-lookup"><span data-stu-id="5f5db-155">Configuring Grunt</span></span>

<span data-ttu-id="5f5db-156">Grunt est configuré à l’aide d’un manifeste nommé *Gruntfile. js* qui définit, charge et inscrit des tâches qui peuvent être exécutées manuellement ou configurées pour s’exécuter automatiquement en fonction des événements dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5db-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="5f5db-157">Cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **un nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="5f5db-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="5f5db-158">Sélectionnez le modèle d’élément de **fichier JavaScript** , remplacez le nom par *Gruntfile. js*, puis cliquez sur le bouton **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="5f5db-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="5f5db-159">Ajoutez le code suivant à *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="5f5db-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="5f5db-160">La `initConfig` fonction définit les options pour chaque package, et le reste du module charge et inscrit les tâches.</span><span class="sxs-lookup"><span data-stu-id="5f5db-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="5f5db-161">À l' `initConfig` intérieur de la fonction, ajoutez `clean` les options de la tâche, comme indiqué dans l’exemple *Gruntfile. js* ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="5f5db-162">La `clean` tâche accepte un tableau de chaînes de répertoire.</span><span class="sxs-lookup"><span data-stu-id="5f5db-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="5f5db-163">Cette tâche supprime les fichiers de *wwwroot/lib* et supprime l’intégralité du répertoire */temp* .</span><span class="sxs-lookup"><span data-stu-id="5f5db-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="5f5db-164">Sous la `initConfig` fonction, ajoutez un appel à `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="5f5db-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="5f5db-165">Cela rendra la tâche exécutable à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5db-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="5f5db-166">Enregistrez *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="5f5db-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="5f5db-167">Le fichier doit ressembler à la capture d’écran ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-167">The file should look something like the screenshot below.</span></span>

    ![gruntfile initial](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="5f5db-169">Cliquez avec le bouton droit sur *Gruntfile. js* et sélectionnez Explorateur de l’exécution de **tâches** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="5f5db-170">La fenêtre de l' **Explorateur du Runner de tâche** s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="5f5db-170">The **Task Runner Explorer** window will open.</span></span>

    ![menu de l’Explorateur de l’exécuteur de tâches](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="5f5db-172">Vérifiez que `clean` affiche sous **tâches** dans l' **Explorateur**de l’exécuteur de tâches.</span><span class="sxs-lookup"><span data-stu-id="5f5db-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Liste des tâches de l’Explorateur de l’exécuteur de tâches](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="5f5db-174">Cliquez avec le bouton droit sur la tâche nettoyer et sélectionnez **exécuter** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="5f5db-175">Une fenêtre de commande affiche la progression de la tâche.</span><span class="sxs-lookup"><span data-stu-id="5f5db-175">A command window displays progress of the task.</span></span>

    ![tâche de nettoyage de l’Explorateur de l’exécuteur de tâches](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="5f5db-177">Il n’y a pas encore de fichiers ou de répertoires à nettoyer.</span><span class="sxs-lookup"><span data-stu-id="5f5db-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="5f5db-178">Si vous le souhaitez, vous pouvez les créer manuellement dans le Explorateur de solutions puis exécuter la tâche nettoyer en tant que test.</span><span class="sxs-lookup"><span data-stu-id="5f5db-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="5f5db-179">Dans la `initConfig` fonction, ajoutez une entrée pour `concat` à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="5f5db-180">Le `src` tableau de propriétés répertorie les fichiers à combiner, dans l’ordre dans lequel ils doivent être combinés.</span><span class="sxs-lookup"><span data-stu-id="5f5db-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="5f5db-181">La `dest` propriété assigne le chemin d’accès au fichier combiné qui est produit.</span><span class="sxs-lookup"><span data-stu-id="5f5db-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5f5db-182">La `all` propriété dans le code ci-dessus est le nom d’une cible.</span><span class="sxs-lookup"><span data-stu-id="5f5db-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="5f5db-183">Les cibles sont utilisées dans certaines tâches grunt pour autoriser plusieurs environnements de génération.</span><span class="sxs-lookup"><span data-stu-id="5f5db-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="5f5db-184">Vous pouvez afficher les cibles intégrées à l’aide d’IntelliSense ou attribuer les vôtres.</span><span class="sxs-lookup"><span data-stu-id="5f5db-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="5f5db-185">Ajoutez la `jshint` tâche à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="5f5db-186">L’utilitaire `code-quality` jshint est exécuté sur chaque fichier JavaScript trouvé dans le répertoire *temp* .</span><span class="sxs-lookup"><span data-stu-id="5f5db-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="5f5db-187">L’option « -W069 » est une erreur produite par jshint lorsque JavaScript utilise la syntaxe des crochets pour assigner une propriété à la place `Tastes["Sweet"]` de la `Tastes.Sweet`notation par points, c’est-à-dire au lieu de.</span><span class="sxs-lookup"><span data-stu-id="5f5db-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="5f5db-188">L’option désactive l’avertissement pour permettre au reste du processus de continuer.</span><span class="sxs-lookup"><span data-stu-id="5f5db-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="5f5db-189">Ajoutez la `uglify` tâche à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="5f5db-190">La tâche minimise le fichier *. js combiné* situé dans le répertoire temporaire et crée le fichier de résultats dans wwwroot/lib en suivant le nom \* \<\>de fichier\*de convention d’affectation de noms standard. min. js.</span><span class="sxs-lookup"><span data-stu-id="5f5db-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="5f5db-191">Sous l’appel à `grunt.loadNpmTasks` ces charges `grunt-contrib-clean`, incluez le même appel pour jshint, Concat et uglify à l’aide du code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="5f5db-192">Enregistrez *Gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="5f5db-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="5f5db-193">Le fichier doit ressembler à l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f5db-193">The file should look something like the example below.</span></span>

    ![exemple de fichier grunt complet](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="5f5db-195">Notez que la liste tâches de l’Explorateur de `clean` **tâche Runner** comprend `uglify` des tâches, `concat` `jshint` et.</span><span class="sxs-lookup"><span data-stu-id="5f5db-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="5f5db-196">Exécutez chaque tâche dans l’ordre et observez les résultats dans **Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="5f5db-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="5f5db-197">Chaque tâche doit s’exécuter sans erreur.</span><span class="sxs-lookup"><span data-stu-id="5f5db-197">Each task should run without errors.</span></span>

    ![l’Explorateur de tâche Runner exécute chaque tâche](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="5f5db-199">La tâche Concat crée un fichier *. js combiné* et le place dans le répertoire Temp.</span><span class="sxs-lookup"><span data-stu-id="5f5db-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="5f5db-200">La `jshint` tâche s’exécute simplement et ne produit pas de sortie.</span><span class="sxs-lookup"><span data-stu-id="5f5db-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="5f5db-201">La `uglify` tâche crée un nouveau fichier *. min. js combiné* et le place dans *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="5f5db-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="5f5db-202">Une fois l’opération terminée, la solution devrait ressembler à la capture d’écran ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="5f5db-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Explorateur de solutions après toutes les tâches](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="5f5db-204">Pour plus d’informations sur les options pour chaque package, [https://www.npmjs.com/](https://www.npmjs.com/) visitez et recherchez le nom du package dans la zone de recherche de la page principale.</span><span class="sxs-lookup"><span data-stu-id="5f5db-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="5f5db-205">Par exemple, vous pouvez consulter le grunt-pretrib-Clean package pour obtenir un lien vers la documentation qui décrit tous ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="5f5db-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="5f5db-206">Récapitulons</span><span class="sxs-lookup"><span data-stu-id="5f5db-206">All together now</span></span>

<span data-ttu-id="5f5db-207">Utilisez la méthode `registerTask()` grunt pour exécuter une série de tâches dans une séquence particulière.</span><span class="sxs-lookup"><span data-stu-id="5f5db-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="5f5db-208">Par exemple, pour exécuter les étapes de l’exemple ci-dessus dans l’ordre de nettoyage-> Concat-> jshint-> uglify, ajoutez le code ci-dessous au module.</span><span class="sxs-lookup"><span data-stu-id="5f5db-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="5f5db-209">Le code doit être ajouté au même niveau que les appels loadNpmTasks (), en dehors de initConfig.</span><span class="sxs-lookup"><span data-stu-id="5f5db-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="5f5db-210">La nouvelle tâche apparaît dans l’Explorateur de l’exécuteur de tâches, sous tâches d’alias.</span><span class="sxs-lookup"><span data-stu-id="5f5db-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="5f5db-211">Vous pouvez cliquer avec le bouton droit et l’exécuter exactement comme vous le feriez pour d’autres tâches.</span><span class="sxs-lookup"><span data-stu-id="5f5db-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="5f5db-212">La `all` tâche s’exécute `clean`, `concat` `jshint` et `uglify`, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="5f5db-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tâches grunt alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="5f5db-214">Détection des changements</span><span class="sxs-lookup"><span data-stu-id="5f5db-214">Watching for changes</span></span>

<span data-ttu-id="5f5db-215">Une `watch` tâche garde un œil sur les fichiers et les répertoires.</span><span class="sxs-lookup"><span data-stu-id="5f5db-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="5f5db-216">La montre déclenche automatiquement des tâches si elle détecte des modifications.</span><span class="sxs-lookup"><span data-stu-id="5f5db-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="5f5db-217">Ajoutez le code ci-dessous à initConfig pour surveiller les \*modifications apportées aux fichiers. js dans le répertoire de machine à écrire.</span><span class="sxs-lookup"><span data-stu-id="5f5db-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="5f5db-218">Si un fichier JavaScript est modifié, `watch` exécute la `all` tâche.</span><span class="sxs-lookup"><span data-stu-id="5f5db-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="5f5db-219">Ajoutez un appel à `loadNpmTasks()` pour afficher la `watch` tâche dans l’Explorateur de l’exécuteur de tâches.</span><span class="sxs-lookup"><span data-stu-id="5f5db-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="5f5db-220">Cliquez avec le bouton droit sur la tâche espion dans l’Explorateur de tâches et sélectionnez Exécuter dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="5f5db-221">La fenêtre de commande qui affiche la tâche d’observation en cours d’exécution affiche une « attente... » Message.</span><span class="sxs-lookup"><span data-stu-id="5f5db-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="5f5db-222">Ouvrez l’un des fichiers de machine à écrire, ajoutez un espace, puis enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="5f5db-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="5f5db-223">Cela déclenche la tâche Watch et déclenche l’exécution des autres tâches dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="5f5db-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="5f5db-224">La capture d’écran ci-dessous montre un exemple d’exécution.</span><span class="sxs-lookup"><span data-stu-id="5f5db-224">The screenshot below shows a sample run.</span></span>

![sortie des tâches en cours d’exécution](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="5f5db-226">Lier à des événements Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f5db-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="5f5db-227">À moins que vous ne souhaitiez démarrer vos tâches manuellement chaque fois que vous travaillez dans Visual Studio, liez des tâches à **avant la génération**, après les événements de **génération**, de **nettoyage**et d' **ouverture de projet** .</span><span class="sxs-lookup"><span data-stu-id="5f5db-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="5f5db-228">Liez `watch` afin qu’il s’exécute chaque fois que Visual Studio s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="5f5db-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="5f5db-229">Dans l’Explorateur de l’exécuteur de tâches, cliquez avec le bouton droit sur la tâche espion, puis sélectionnez **liaisons** > **projet ouvert** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="5f5db-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![lier une tâche à l’ouverture du projet](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="5f5db-231">Déchargez et rechargez le projet.</span><span class="sxs-lookup"><span data-stu-id="5f5db-231">Unload and reload the project.</span></span> <span data-ttu-id="5f5db-232">Lorsque le projet se recharge, la tâche espion commence automatiquement.</span><span class="sxs-lookup"><span data-stu-id="5f5db-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="5f5db-233">Résumé</span><span class="sxs-lookup"><span data-stu-id="5f5db-233">Summary</span></span>

<span data-ttu-id="5f5db-234">Grunt est un testeur de tâches puissant qui peut être utilisé pour automatiser la plupart des tâches de génération de clients.</span><span class="sxs-lookup"><span data-stu-id="5f5db-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="5f5db-235">Grunt tire parti de NPM pour fournir ses packages et propose des fonctionnalités d’intégration avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5db-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="5f5db-236">L’Explorateur de tâche Runner de Visual Studio détecte les modifications apportées aux fichiers de configuration et fournit une interface pratique pour exécuter des tâches, afficher des tâches en cours d’exécution et lier des tâches à des événements Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5db-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
