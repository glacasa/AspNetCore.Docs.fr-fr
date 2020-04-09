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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="7cf15-103">Regrouper et minifier les actifs statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cf15-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="7cf15-104">Par [Scott Addie](https://twitter.com/Scott_Addie) et [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="7cf15-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="7cf15-105">Cet article explique les avantages de l’application du regroupement et de la minification, y compris la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET applications Web Core.</span><span class="sxs-lookup"><span data-stu-id="7cf15-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="7cf15-106">Qu’est-ce que le regroupement et la minification</span><span class="sxs-lookup"><span data-stu-id="7cf15-106">What is bundling and minification</span></span>

<span data-ttu-id="7cf15-107">Le regroupement et la minification sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application web.</span><span class="sxs-lookup"><span data-stu-id="7cf15-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="7cf15-108">Utilisés ensemble, le regroupement et la minification améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des actifs statiques demandés.</span><span class="sxs-lookup"><span data-stu-id="7cf15-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="7cf15-109">Le regroupement et la minification améliorent principalement le temps de chargement de la première page.</span><span class="sxs-lookup"><span data-stu-id="7cf15-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="7cf15-110">Une fois qu’une page Web a été demandée, le navigateur cache les actifs statiques (JavaScript, CSS et images).</span><span class="sxs-lookup"><span data-stu-id="7cf15-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="7cf15-111">Par conséquent, le regroupement et la minification n’améliorent pas les performances lorsque vous demandez la même page, ou pages, sur le même site demandant les mêmes actifs.</span><span class="sxs-lookup"><span data-stu-id="7cf15-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="7cf15-112">Si l’en-tête expire n’est pas réglé correctement sur les actifs et si le regroupement et la minification n’est pas utilisé, heuristique de fraîcheur du navigateur marquent les actifs périmés après quelques jours.</span><span class="sxs-lookup"><span data-stu-id="7cf15-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="7cf15-113">En outre, le navigateur nécessite une demande de validation pour chaque actif.</span><span class="sxs-lookup"><span data-stu-id="7cf15-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="7cf15-114">Dans ce cas, le regroupement et la minification offrent une amélioration des performances même après la demande de première page.</span><span class="sxs-lookup"><span data-stu-id="7cf15-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="7cf15-115">Groupement</span><span class="sxs-lookup"><span data-stu-id="7cf15-115">Bundling</span></span>

<span data-ttu-id="7cf15-116">Le regroupement consiste à combiner plusieurs fichiers en un seul.</span><span class="sxs-lookup"><span data-stu-id="7cf15-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="7cf15-117">Le regroupement réduit le nombre de demandes de serveur nécessaires pour rendre un actif Web, comme une page Web.</span><span class="sxs-lookup"><span data-stu-id="7cf15-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="7cf15-118">Vous pouvez créer n’importe quel nombre de paquets individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP du navigateur au serveur ou du service fournissant votre application.</span><span class="sxs-lookup"><span data-stu-id="7cf15-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="7cf15-119">Il en résulte une amélioration des performances de chargement de première page.</span><span class="sxs-lookup"><span data-stu-id="7cf15-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="7cf15-120">Minimisation</span><span class="sxs-lookup"><span data-stu-id="7cf15-120">Minification</span></span>

<span data-ttu-id="7cf15-121">La minification supprime les caractères inutiles du code sans modifier les fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="7cf15-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="7cf15-122">Le résultat est une réduction significative de la taille des actifs demandés (tels que le CSS, les images et les fichiers JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7cf15-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="7cf15-123">Les effets secondaires courants de la minification comprennent le raccourcissement des noms variables à un seul personnage et la suppression des commentaires et de l’espace blanc inutile.</span><span class="sxs-lookup"><span data-stu-id="7cf15-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="7cf15-124">Considérez la fonction JavaScript suivante :</span><span class="sxs-lookup"><span data-stu-id="7cf15-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="7cf15-125">La minification réduit la fonction à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="7cf15-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="7cf15-126">En plus de supprimer les commentaires et l’espace blanc inutile, le paramètre suivant et les noms variables ont été renommés comme suit:</span><span class="sxs-lookup"><span data-stu-id="7cf15-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="7cf15-127">Original</span><span class="sxs-lookup"><span data-stu-id="7cf15-127">Original</span></span> | <span data-ttu-id="7cf15-128">Affectation d'un nouveau nom</span><span class="sxs-lookup"><span data-stu-id="7cf15-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="7cf15-129">Impact du regroupement et de la minification</span><span class="sxs-lookup"><span data-stu-id="7cf15-129">Impact of bundling and minification</span></span>

<span data-ttu-id="7cf15-130">Le tableau suivant décrit les différences entre le chargement individuel des actifs et l’utilisation du regroupement et de la minification :</span><span class="sxs-lookup"><span data-stu-id="7cf15-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="7cf15-131">Action</span><span class="sxs-lookup"><span data-stu-id="7cf15-131">Action</span></span> | <span data-ttu-id="7cf15-132">Avec B/M</span><span class="sxs-lookup"><span data-stu-id="7cf15-132">With B/M</span></span> | <span data-ttu-id="7cf15-133">Sans B/M</span><span class="sxs-lookup"><span data-stu-id="7cf15-133">Without B/M</span></span> | <span data-ttu-id="7cf15-134">Modifier</span><span class="sxs-lookup"><span data-stu-id="7cf15-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="7cf15-135">Demandes de fichiers</span><span class="sxs-lookup"><span data-stu-id="7cf15-135">File Requests</span></span>  | <span data-ttu-id="7cf15-136">7</span><span class="sxs-lookup"><span data-stu-id="7cf15-136">7</span></span>   | <span data-ttu-id="7cf15-137">18</span><span class="sxs-lookup"><span data-stu-id="7cf15-137">18</span></span>     | <span data-ttu-id="7cf15-138">157%</span><span class="sxs-lookup"><span data-stu-id="7cf15-138">157%</span></span>
<span data-ttu-id="7cf15-139">KB transféré</span><span class="sxs-lookup"><span data-stu-id="7cf15-139">KB Transferred</span></span> | <span data-ttu-id="7cf15-140">156</span><span class="sxs-lookup"><span data-stu-id="7cf15-140">156</span></span> | <span data-ttu-id="7cf15-141">264.68</span><span class="sxs-lookup"><span data-stu-id="7cf15-141">264.68</span></span> | <span data-ttu-id="7cf15-142">70 %</span><span class="sxs-lookup"><span data-stu-id="7cf15-142">70%</span></span>
<span data-ttu-id="7cf15-143">Temps de chargement (ms)</span><span class="sxs-lookup"><span data-stu-id="7cf15-143">Load Time (ms)</span></span> | <span data-ttu-id="7cf15-144">885</span><span class="sxs-lookup"><span data-stu-id="7cf15-144">885</span></span> | <span data-ttu-id="7cf15-145">2360</span><span class="sxs-lookup"><span data-stu-id="7cf15-145">2360</span></span>   | <span data-ttu-id="7cf15-146">167%</span><span class="sxs-lookup"><span data-stu-id="7cf15-146">167%</span></span>

<span data-ttu-id="7cf15-147">Les navigateurs sont assez verbeux en ce qui concerne les en-têtes de demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="7cf15-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="7cf15-148">Le total des octets envoyés métrique a connu une réduction significative lors du regroupement.</span><span class="sxs-lookup"><span data-stu-id="7cf15-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="7cf15-149">Le temps de charge montre une amélioration significative, mais cet exemple a couru localement.</span><span class="sxs-lookup"><span data-stu-id="7cf15-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="7cf15-150">Des gains de performance plus importants sont réalisés lors de l’utilisation du regroupement et de la minification avec des actifs transférés sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="7cf15-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="7cf15-151">Choisissez une stratégie de regroupement et de minification</span><span class="sxs-lookup"><span data-stu-id="7cf15-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="7cf15-152">Les modèles de projet MVC et Razor Pages constituent une solution hors boîte pour le regroupement et la minification composé d’un fichier de configuration JSON.</span><span class="sxs-lookup"><span data-stu-id="7cf15-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="7cf15-153">Les outils tiers, tels que le coureur de tâches [Grunt,](xref:client-side/using-grunt) accomplissent les mêmes tâches avec un peu plus de complexité.</span><span class="sxs-lookup"><span data-stu-id="7cf15-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="7cf15-154">Un outil tiers est un grand ajustement lorsque votre flux de travail&mdash;de développement nécessite un traitement au-delà du regroupement et de la minification tels que le linting et l’optimisation d’image.</span><span class="sxs-lookup"><span data-stu-id="7cf15-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="7cf15-155">En utilisant le regroupement et la minification du temps de conception, les fichiers minifiés sont créés avant le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="7cf15-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="7cf15-156">Le regroupement et la minification avant le déploiement offrent l’avantage d’une charge de serveur réduite.</span><span class="sxs-lookup"><span data-stu-id="7cf15-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="7cf15-157">Cependant, il est important de reconnaître que le regroupement et la minification en temps de conception augmente la complexité de la construction et ne fonctionne qu’avec des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="7cf15-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7cf15-158">Configurer le regroupement et la minification</span><span class="sxs-lookup"><span data-stu-id="7cf15-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="7cf15-159">Dans ASP.NET Core 2.0 ou plus tôt, les modèles de projet MVC et Razor Pages fournissent un fichier de configuration *bundleconfig.json* qui définit les options pour chaque forfait :</span><span class="sxs-lookup"><span data-stu-id="7cf15-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7cf15-160">Dans ASP.NET Core 2.1 ou plus tard, ajoutez un nouveau fichier JSON, nommé *bundleconfig.json*, à la racine du projet MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7cf15-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="7cf15-161">Inclure le JSON suivant dans ce dossier comme point de départ:</span><span class="sxs-lookup"><span data-stu-id="7cf15-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="7cf15-162">Le *fichier bundleconfig.json* définit les options pour chaque bundle.</span><span class="sxs-lookup"><span data-stu-id="7cf15-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="7cf15-163">Dans l’exemple précédent, une configuration de paquet unique est définie pour les fichiers JavaScript*personnalisés (wwwroot/js/site.js*) et de la feuille de style *(wwwroot/css/site.css*).</span><span class="sxs-lookup"><span data-stu-id="7cf15-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="7cf15-164">Les options de configuration comprennent ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="7cf15-164">Configuration options include:</span></span>

* <span data-ttu-id="7cf15-165">`outputFileName`: Le nom du fichier du paquet à la sortie.</span><span class="sxs-lookup"><span data-stu-id="7cf15-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="7cf15-166">Peut contenir un chemin relatif à partir du fichier *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="7cf15-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="7cf15-167">**Obligatoire**</span><span class="sxs-lookup"><span data-stu-id="7cf15-167">**required**</span></span>
* <span data-ttu-id="7cf15-168">`inputFiles`: Un tableau de fichiers à regrouper.</span><span class="sxs-lookup"><span data-stu-id="7cf15-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="7cf15-169">Ce sont des chemins relatifs au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="7cf15-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="7cf15-170">**optionnel,** une valeur vide se traduit par un fichier de sortie vide.</span><span class="sxs-lookup"><span data-stu-id="7cf15-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="7cf15-171">les motifs [de globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) sont soutenus.</span><span class="sxs-lookup"><span data-stu-id="7cf15-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="7cf15-172">`minify`: Les options de minification pour le type de sortie.</span><span class="sxs-lookup"><span data-stu-id="7cf15-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="7cf15-173">**optionnel,** \*par défaut - `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="7cf15-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="7cf15-174">Les options de configuration sont disponibles par type de fichier de sortie.</span><span class="sxs-lookup"><span data-stu-id="7cf15-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="7cf15-175">Minificateur CSS</span><span class="sxs-lookup"><span data-stu-id="7cf15-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="7cf15-176">Minificateur JavaScript</span><span class="sxs-lookup"><span data-stu-id="7cf15-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="7cf15-177">Minificateur HTML</span><span class="sxs-lookup"><span data-stu-id="7cf15-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="7cf15-178">`includeInProject`: Indicateur indiquant s’il faut ajouter des fichiers générés au fichier du projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="7cf15-179">**facultatif**, *par défaut - faux*</span><span class="sxs-lookup"><span data-stu-id="7cf15-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="7cf15-180">`sourceMap`: Indicateur indiquant s’il convient de générer une carte source pour le fichier groupé.</span><span class="sxs-lookup"><span data-stu-id="7cf15-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="7cf15-181">**facultatif**, *par défaut - faux*</span><span class="sxs-lookup"><span data-stu-id="7cf15-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="7cf15-182">`sourceMapRootPath`: Le chemin de racine pour stocker le fichier de carte source générée.</span><span class="sxs-lookup"><span data-stu-id="7cf15-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="7cf15-183">Exécution du regroupement et de la minification en temps de construction</span><span class="sxs-lookup"><span data-stu-id="7cf15-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="7cf15-184">Le package [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet permet l’exécution du regroupement et de la minification au moment de la construction.</span><span class="sxs-lookup"><span data-stu-id="7cf15-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="7cf15-185">Le paquet injecte [des cibles MSBuild](/visualstudio/msbuild/msbuild-targets) qui fonctionnent à l’heure de construction et de nettoyage.</span><span class="sxs-lookup"><span data-stu-id="7cf15-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="7cf15-186">Le fichier *bundleconfig.json* est analysé par le processus de build pour produire les fichiers de sortie en fonction de la configuration définie.</span><span class="sxs-lookup"><span data-stu-id="7cf15-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="7cf15-187">BuildBundlerMinifier appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support.</span><span class="sxs-lookup"><span data-stu-id="7cf15-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7cf15-188">Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="7cf15-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7cf15-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cf15-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7cf15-190">Ajoutez le package *BuildBundlerMinifier* à votre projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="7cf15-191">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-191">Build the project.</span></span> <span data-ttu-id="7cf15-192">Ce qui suit apparaît dans la fenêtre de sortie :</span><span class="sxs-lookup"><span data-stu-id="7cf15-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="7cf15-193">Nettoyez le projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-193">Clean the project.</span></span> <span data-ttu-id="7cf15-194">Ce qui suit apparaît dans la fenêtre de sortie :</span><span class="sxs-lookup"><span data-stu-id="7cf15-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="7cf15-195">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cf15-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7cf15-196">Ajoutez le package *BuildBundlerMinifier* à votre projet :</span><span class="sxs-lookup"><span data-stu-id="7cf15-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="7cf15-197">Si vous utilisez ASP.NET Core 1.x, restaurer le paquet nouvellement ajouté:</span><span class="sxs-lookup"><span data-stu-id="7cf15-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="7cf15-198">Générez le projet :</span><span class="sxs-lookup"><span data-stu-id="7cf15-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="7cf15-199">Voici :</span><span class="sxs-lookup"><span data-stu-id="7cf15-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="7cf15-200">Nettoyer le projet :</span><span class="sxs-lookup"><span data-stu-id="7cf15-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="7cf15-201">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="7cf15-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="7cf15-202">Exécution ponctuelle du regroupement et de la minification</span><span class="sxs-lookup"><span data-stu-id="7cf15-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="7cf15-203">Il est possible d’exécuter les tâches de regroupement et de minification sur une base ad hoc, sans construire le projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="7cf15-204">Ajoutez le forfait [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet à votre projet :</span><span class="sxs-lookup"><span data-stu-id="7cf15-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="7cf15-205">BundlerMinifier.Core appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support.</span><span class="sxs-lookup"><span data-stu-id="7cf15-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7cf15-206">Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="7cf15-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="7cf15-207">Ce paquet étend le CLI de base .NET pour inclure l’outil *de paquet dotnet.*</span><span class="sxs-lookup"><span data-stu-id="7cf15-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="7cf15-208">La commande suivante peut être exécutée dans la fenêtre de la console de gestionnaire de paquet (PMC) ou dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="7cf15-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="7cf15-209">NuGet Package Manager ajoute des dépendances au fichier `<PackageReference />` '.csproj sous forme de nœuds.</span><span class="sxs-lookup"><span data-stu-id="7cf15-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="7cf15-210">La `dotnet bundle` commande est enregistrée auprès de l’ÉLIC de base .NET seulement lorsqu’un `<DotNetCliToolReference />` nœud est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7cf15-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="7cf15-211">Modifier le fichier 'csproj en conséquence.</span><span class="sxs-lookup"><span data-stu-id="7cf15-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="7cf15-212">Ajouter des fichiers au flux de travail</span><span class="sxs-lookup"><span data-stu-id="7cf15-212">Add files to workflow</span></span>

<span data-ttu-id="7cf15-213">Prenons un exemple dans lequel un fichier *custom.css* supplémentaire est ajouté ressemblant à ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="7cf15-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="7cf15-214">Pour minifier *custom.css* et l’empaqueter avec *site.css* dans un fichier *site.min.css,* ajoutez le chemin relatif à *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="7cf15-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="7cf15-215">Alternativement, le modèle de globbing suivant pourrait être employé :</span><span class="sxs-lookup"><span data-stu-id="7cf15-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="7cf15-216">Ce modèle de glisse correspond à tous les fichiers CSS et exclut le modèle de fichier minifié.</span><span class="sxs-lookup"><span data-stu-id="7cf15-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="7cf15-217">Générez l’application.</span><span class="sxs-lookup"><span data-stu-id="7cf15-217">Build the application.</span></span> <span data-ttu-id="7cf15-218">Ouvrez *site.min.css* et remarquez que le contenu de *custom.css* est annexé à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="7cf15-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="7cf15-219">Regroupement et minification basés sur l’environnement</span><span class="sxs-lookup"><span data-stu-id="7cf15-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="7cf15-220">Comme meilleure pratique, les fichiers groupés et minifiés de votre application doivent être utilisés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="7cf15-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="7cf15-221">Pendant le développement, les fichiers originaux facilitent le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7cf15-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="7cf15-222">Spécifiez les fichiers à inclure dans vos pages en utilisant [l’Aide à l’étiquette environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues.</span><span class="sxs-lookup"><span data-stu-id="7cf15-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="7cf15-223">L’Environnement Tag Helper ne rend son contenu que lorsqu’il est en cours d’exécution dans des [environnements](xref:fundamentals/environments)spécifiques .</span><span class="sxs-lookup"><span data-stu-id="7cf15-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7cf15-224">L’étiquette suivante `environment` rend les fichiers CSS non `Development` traités lorsqu’ils sont exécutés dans l’environnement :</span><span class="sxs-lookup"><span data-stu-id="7cf15-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="7cf15-225">L’étiquette suivante `environment` rend les fichiers CSS groupés et minifiés lorsqu’ils sont en cours d’exécution dans un environnement autre que `Development`.</span><span class="sxs-lookup"><span data-stu-id="7cf15-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="7cf15-226">Par exemple, `Production` l’exécution ou `Staging` déclenche le rendu de ces feuilles de style:</span><span class="sxs-lookup"><span data-stu-id="7cf15-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="7cf15-227">Consommer bundleconfig.json de Gulp</span><span class="sxs-lookup"><span data-stu-id="7cf15-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="7cf15-228">Il y a des cas où le flux de travail de regroupement et de minification d’une application nécessite un traitement supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="7cf15-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="7cf15-229">Les exemples incluent l’optimisation des images, le busting de cache, et le traitement d’actifs CDN.</span><span class="sxs-lookup"><span data-stu-id="7cf15-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="7cf15-230">Pour répondre à ces exigences, vous pouvez convertir le flux de travail de regroupement et de minification pour utiliser Gulp.</span><span class="sxs-lookup"><span data-stu-id="7cf15-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="7cf15-231">Utilisez l’extension Bundler & Minifier</span><span class="sxs-lookup"><span data-stu-id="7cf15-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="7cf15-232">L’extension Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) gère la conversion en Gulp.</span><span class="sxs-lookup"><span data-stu-id="7cf15-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="7cf15-233">L’extension Bundler & Minifier appartient à un projet communautaire sur GitHub pour lequel Microsoft ne fournit aucun support.</span><span class="sxs-lookup"><span data-stu-id="7cf15-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7cf15-234">Les questions devraient être déposées [ici](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="7cf15-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="7cf15-235">Cliquez à droite sur le fichier *bundleconfig.json* dans Solution Explorer et sélectionnez **Bundler & Minifier** > **Convert To Gulp...**:</span><span class="sxs-lookup"><span data-stu-id="7cf15-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Convertir à Gulp élément de menu contexte](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="7cf15-237">Les fichiers *gulpfile.js* et *package.json* sont ajoutés au projet.</span><span class="sxs-lookup"><span data-stu-id="7cf15-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="7cf15-238">Les paquets [npm](https://www.npmjs.com/) de soutien énumérés `devDependencies` dans la section du fichier *package.json* sont installés.</span><span class="sxs-lookup"><span data-stu-id="7cf15-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="7cf15-239">Exécutez la commande suivante dans la fenêtre PMC pour installer le Gulp CLI comme une dépendance mondiale :</span><span class="sxs-lookup"><span data-stu-id="7cf15-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="7cf15-240">Le fichier *gulpfile.js* lit le fichier *bundleconfig.json* pour les entrées, les sorties et les paramètres.</span><span class="sxs-lookup"><span data-stu-id="7cf15-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="7cf15-241">Convertir manuellement</span><span class="sxs-lookup"><span data-stu-id="7cf15-241">Convert manually</span></span>

<span data-ttu-id="7cf15-242">Si Visual Studio et/ou l’extension Bundler & Minifier ne sont pas disponibles, convertissez manuellement.</span><span class="sxs-lookup"><span data-stu-id="7cf15-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="7cf15-243">Ajouter un fichier *package.json,* avec ce qui suit, `devDependencies`à la racine du projet:</span><span class="sxs-lookup"><span data-stu-id="7cf15-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="7cf15-244">Le `gulp-uglify` module ne prend pas en charge ECMAScript (ES) 2015 / ES6 et plus tard.</span><span class="sxs-lookup"><span data-stu-id="7cf15-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="7cf15-245">Installez [le goux-terser](https://www.npmjs.com/package/gulp-terser) au lieu d’utiliser `gulp-uglify` ES2015 / ES6 ou plus tard.</span><span class="sxs-lookup"><span data-stu-id="7cf15-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="7cf15-246">Installez les dépendances en exécutant la commande suivante au même niveau que *package.json*:</span><span class="sxs-lookup"><span data-stu-id="7cf15-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="7cf15-247">Installer l’IMC Gulp comme dépendance mondiale :</span><span class="sxs-lookup"><span data-stu-id="7cf15-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="7cf15-248">Copiez le fichier *gulpfile.js* ci-dessous sur la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="7cf15-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="7cf15-249">Exécuter les tâches Gulp</span><span class="sxs-lookup"><span data-stu-id="7cf15-249">Run Gulp tasks</span></span>

<span data-ttu-id="7cf15-250">Pour déclencher la tâche de minification Gulp avant la construction du projet dans Visual Studio, ajoutez la [cible MSBuild](/visualstudio/msbuild/msbuild-targets) suivante au fichier '.csproj:</span><span class="sxs-lookup"><span data-stu-id="7cf15-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="7cf15-251">Dans cet exemple, toutes `MyPreCompileTarget` les tâches définies dans `Build` la cible s’exécutent avant la cible prédéfinie.</span><span class="sxs-lookup"><span data-stu-id="7cf15-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="7cf15-252">Sortie similaire à ce qui suit apparaît dans la fenêtre de sortie de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="7cf15-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7cf15-253">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7cf15-253">Additional resources</span></span>

* [<span data-ttu-id="7cf15-254">Utiliser Grunt</span><span class="sxs-lookup"><span data-stu-id="7cf15-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="7cf15-255">Utiliser plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="7cf15-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="7cf15-256">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="7cf15-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
