---
title: Regrouper et minifier les actifs statiques dans ASP.NET Core
author: scottaddie
description: Apprenez à optimiser les ressources statiques dans une application Web ASP.NET Core en appliquant des techniques de regroupement et de minification.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488687"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="9506d-103">Regrouper et minifier les actifs statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9506d-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="9506d-104">Par [Scott Addie](https://twitter.com/Scott_Addie) et [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="9506d-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="9506d-105">Cet article explique les avantages de l’application du regroupement et de la minification, y compris la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET applications Web Core.</span><span class="sxs-lookup"><span data-stu-id="9506d-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="9506d-106">Qu’est-ce que le regroupement et la minification</span><span class="sxs-lookup"><span data-stu-id="9506d-106">What is bundling and minification</span></span>

<span data-ttu-id="9506d-107">Le regroupement et la minification sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application web.</span><span class="sxs-lookup"><span data-stu-id="9506d-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="9506d-108">Utilisés ensemble, le regroupement et la minification améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des actifs statiques demandés.</span><span class="sxs-lookup"><span data-stu-id="9506d-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="9506d-109">Le regroupement et la minification améliorent principalement le temps de chargement de la première page.</span><span class="sxs-lookup"><span data-stu-id="9506d-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="9506d-110">Une fois qu’une page Web a été demandée, le navigateur cache les actifs statiques (JavaScript, CSS et images).</span><span class="sxs-lookup"><span data-stu-id="9506d-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="9506d-111">Par conséquent, le regroupement et la minification n’améliorent pas les performances lorsque vous demandez la même page, ou pages, sur le même site demandant les mêmes actifs.</span><span class="sxs-lookup"><span data-stu-id="9506d-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="9506d-112">Si l’en-tête expire n’est pas réglé correctement sur les actifs et si le regroupement et la minification n’est pas utilisé, heuristique de fraîcheur du navigateur marquent les actifs périmés après quelques jours.</span><span class="sxs-lookup"><span data-stu-id="9506d-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="9506d-113">En outre, le navigateur nécessite une demande de validation pour chaque actif.</span><span class="sxs-lookup"><span data-stu-id="9506d-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="9506d-114">Dans ce cas, le regroupement et la minification offrent une amélioration des performances même après la demande de première page.</span><span class="sxs-lookup"><span data-stu-id="9506d-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="9506d-115">Groupement</span><span class="sxs-lookup"><span data-stu-id="9506d-115">Bundling</span></span>

<span data-ttu-id="9506d-116">Le regroupement consiste à combiner plusieurs fichiers en un seul.</span><span class="sxs-lookup"><span data-stu-id="9506d-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="9506d-117">Le regroupement réduit le nombre de demandes de serveur nécessaires pour rendre un actif Web, comme une page Web.</span><span class="sxs-lookup"><span data-stu-id="9506d-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="9506d-118">Vous pouvez créer n’importe quel nombre de paquets individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP du navigateur au serveur ou du service fournissant votre application.</span><span class="sxs-lookup"><span data-stu-id="9506d-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="9506d-119">Il en résulte une amélioration des performances de chargement de première page.</span><span class="sxs-lookup"><span data-stu-id="9506d-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="9506d-120">Minimisation</span><span class="sxs-lookup"><span data-stu-id="9506d-120">Minification</span></span>

<span data-ttu-id="9506d-121">La minification supprime les caractères inutiles du code sans modifier les fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="9506d-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="9506d-122">Le résultat est une réduction significative de la taille des actifs demandés (tels que le CSS, les images et les fichiers JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9506d-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="9506d-123">Les effets secondaires courants de la minification comprennent le raccourcissement des noms variables à un seul personnage et la suppression des commentaires et de l’espace blanc inutile.</span><span class="sxs-lookup"><span data-stu-id="9506d-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="9506d-124">Considérez la fonction JavaScript suivante :</span><span class="sxs-lookup"><span data-stu-id="9506d-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="9506d-125">La minification réduit la fonction à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="9506d-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="9506d-126">En plus de supprimer les commentaires et l’espace blanc inutile, le paramètre suivant et les noms variables ont été renommés comme suit:</span><span class="sxs-lookup"><span data-stu-id="9506d-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="9506d-127">Original</span><span class="sxs-lookup"><span data-stu-id="9506d-127">Original</span></span> | <span data-ttu-id="9506d-128">Affectation d'un nouveau nom</span><span class="sxs-lookup"><span data-stu-id="9506d-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="9506d-129">Impact du regroupement et de la minification</span><span class="sxs-lookup"><span data-stu-id="9506d-129">Impact of bundling and minification</span></span>

<span data-ttu-id="9506d-130">Le tableau suivant décrit les différences entre le chargement individuel des actifs et l’utilisation du regroupement et de la minification :</span><span class="sxs-lookup"><span data-stu-id="9506d-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="9506d-131">Action</span><span class="sxs-lookup"><span data-stu-id="9506d-131">Action</span></span> | <span data-ttu-id="9506d-132">Avec B/M</span><span class="sxs-lookup"><span data-stu-id="9506d-132">With B/M</span></span> | <span data-ttu-id="9506d-133">Sans B/M</span><span class="sxs-lookup"><span data-stu-id="9506d-133">Without B/M</span></span> | <span data-ttu-id="9506d-134">Modifier</span><span class="sxs-lookup"><span data-stu-id="9506d-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="9506d-135">Demandes de fichiers</span><span class="sxs-lookup"><span data-stu-id="9506d-135">File Requests</span></span>  | <span data-ttu-id="9506d-136">7</span><span class="sxs-lookup"><span data-stu-id="9506d-136">7</span></span>   | <span data-ttu-id="9506d-137">18</span><span class="sxs-lookup"><span data-stu-id="9506d-137">18</span></span>     | <span data-ttu-id="9506d-138">157%</span><span class="sxs-lookup"><span data-stu-id="9506d-138">157%</span></span>
<span data-ttu-id="9506d-139">KB transféré</span><span class="sxs-lookup"><span data-stu-id="9506d-139">KB Transferred</span></span> | <span data-ttu-id="9506d-140">156</span><span class="sxs-lookup"><span data-stu-id="9506d-140">156</span></span> | <span data-ttu-id="9506d-141">264.68</span><span class="sxs-lookup"><span data-stu-id="9506d-141">264.68</span></span> | <span data-ttu-id="9506d-142">70 %</span><span class="sxs-lookup"><span data-stu-id="9506d-142">70%</span></span>
<span data-ttu-id="9506d-143">Temps de chargement (ms)</span><span class="sxs-lookup"><span data-stu-id="9506d-143">Load Time (ms)</span></span> | <span data-ttu-id="9506d-144">885</span><span class="sxs-lookup"><span data-stu-id="9506d-144">885</span></span> | <span data-ttu-id="9506d-145">2360</span><span class="sxs-lookup"><span data-stu-id="9506d-145">2360</span></span>   | <span data-ttu-id="9506d-146">167%</span><span class="sxs-lookup"><span data-stu-id="9506d-146">167%</span></span>

<span data-ttu-id="9506d-147">Les navigateurs sont assez verbeux en ce qui concerne les en-têtes de demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9506d-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="9506d-148">Le total des octets envoyés métrique a connu une réduction significative lors du regroupement.</span><span class="sxs-lookup"><span data-stu-id="9506d-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="9506d-149">Le temps de charge montre une amélioration significative, mais cet exemple a couru localement.</span><span class="sxs-lookup"><span data-stu-id="9506d-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="9506d-150">Des gains de performance plus importants sont réalisés lors de l’utilisation du regroupement et de la minification avec des actifs transférés sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="9506d-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="9506d-151">Choisissez une stratégie de regroupement et de minification</span><span class="sxs-lookup"><span data-stu-id="9506d-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="9506d-152">Les modèles de projet MVC et Razor Pages fournissent une solution pour le regroupement et la minification composé d’un fichier de configuration JSON.</span><span class="sxs-lookup"><span data-stu-id="9506d-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="9506d-153">Les outils tiers, tels que le coureur de tâches [Grunt,](xref:client-side/using-grunt) accomplissent les mêmes tâches avec un peu plus de complexité.</span><span class="sxs-lookup"><span data-stu-id="9506d-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="9506d-154">Un outil tiers est un grand ajustement lorsque votre flux de travail&mdash;de développement nécessite un traitement au-delà du regroupement et de la minification tels que le linting et l’optimisation d’image.</span><span class="sxs-lookup"><span data-stu-id="9506d-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="9506d-155">En utilisant le regroupement et la minification du temps de conception, les fichiers minifiés sont créés avant le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="9506d-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="9506d-156">Le regroupement et la minification avant le déploiement offrent l’avantage d’une charge de serveur réduite.</span><span class="sxs-lookup"><span data-stu-id="9506d-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="9506d-157">Cependant, il est important de reconnaître que le regroupement et la minification en temps de conception augmente la complexité de la construction et ne fonctionne qu’avec des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="9506d-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="9506d-158">Configurer le regroupement et la minification</span><span class="sxs-lookup"><span data-stu-id="9506d-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9506d-159">Dans ASP.NET Core 2.0 ou plus tôt, les modèles de projet MVC et Razor Pages fournissent un fichier de configuration *bundleconfig.json* qui définit les options pour chaque forfait :</span><span class="sxs-lookup"><span data-stu-id="9506d-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9506d-160">Dans ASP.NET Core 2.1 ou plus tard, ajoutez un nouveau fichier JSON, nommé *bundleconfig.json*, à la racine du projet MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9506d-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="9506d-161">Inclure le JSON suivant dans ce dossier comme point de départ:</span><span class="sxs-lookup"><span data-stu-id="9506d-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="9506d-162">Le *fichier bundleconfig.json* définit les options pour chaque bundle.</span><span class="sxs-lookup"><span data-stu-id="9506d-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="9506d-163">Dans l’exemple précédent, une configuration de paquet unique est définie pour les fichiers JavaScript*personnalisés (wwwroot/js/site.js*) et de la feuille de style *(wwwroot/css/site.css*).</span><span class="sxs-lookup"><span data-stu-id="9506d-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="9506d-164">Les options de configuration comprennent ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="9506d-164">Configuration options include:</span></span>

* <span data-ttu-id="9506d-165">`outputFileName`: Le nom du fichier du paquet à la sortie.</span><span class="sxs-lookup"><span data-stu-id="9506d-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="9506d-166">Peut contenir un chemin relatif à partir du fichier *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="9506d-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="9506d-167">**Obligatoire**</span><span class="sxs-lookup"><span data-stu-id="9506d-167">**required**</span></span>
* <span data-ttu-id="9506d-168">`inputFiles`: Un tableau de fichiers à regrouper.</span><span class="sxs-lookup"><span data-stu-id="9506d-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="9506d-169">Ce sont des chemins relatifs au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="9506d-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="9506d-170">**optionnel,** une valeur vide se traduit par un fichier de sortie vide.</span><span class="sxs-lookup"><span data-stu-id="9506d-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="9506d-171">les motifs [de globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) sont soutenus.</span><span class="sxs-lookup"><span data-stu-id="9506d-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="9506d-172">`minify`: Les options de minification pour le type de sortie.</span><span class="sxs-lookup"><span data-stu-id="9506d-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="9506d-173">**optionnel,** \*par défaut - `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="9506d-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="9506d-174">Les options de configuration sont disponibles par type de fichier de sortie.</span><span class="sxs-lookup"><span data-stu-id="9506d-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="9506d-175">Minificateur CSS</span><span class="sxs-lookup"><span data-stu-id="9506d-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="9506d-176">Minificateur JavaScript</span><span class="sxs-lookup"><span data-stu-id="9506d-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="9506d-177">Minificateur HTML</span><span class="sxs-lookup"><span data-stu-id="9506d-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="9506d-178">`includeInProject`: Indicateur indiquant s’il faut ajouter des fichiers générés au fichier du projet.</span><span class="sxs-lookup"><span data-stu-id="9506d-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="9506d-179">**facultatif**, *par défaut - faux*</span><span class="sxs-lookup"><span data-stu-id="9506d-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="9506d-180">`sourceMap`: Indicateur indiquant s’il convient de générer une carte source pour le fichier groupé.</span><span class="sxs-lookup"><span data-stu-id="9506d-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="9506d-181">**facultatif**, *par défaut - faux*</span><span class="sxs-lookup"><span data-stu-id="9506d-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="9506d-182">`sourceMapRootPath`: Le chemin de racine pour stocker le fichier de carte source générée.</span><span class="sxs-lookup"><span data-stu-id="9506d-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="9506d-183">Ajouter des fichiers au flux de travail</span><span class="sxs-lookup"><span data-stu-id="9506d-183">Add files to workflow</span></span>

<span data-ttu-id="9506d-184">Prenons un exemple dans lequel un fichier *custom.css* supplémentaire est ajouté ressemblant à ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="9506d-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="9506d-185">Pour minifier *custom.css* et l’empaqueter avec *site.css* dans un fichier *site.min.css,* ajoutez le chemin relatif à *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="9506d-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="9506d-186">Alternativement, le modèle de globbing suivant pourrait être employé :</span><span class="sxs-lookup"><span data-stu-id="9506d-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="9506d-187">Ce modèle de glisse correspond à tous les fichiers CSS et exclut le modèle de fichier minifié.</span><span class="sxs-lookup"><span data-stu-id="9506d-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="9506d-188">Générez l’application.</span><span class="sxs-lookup"><span data-stu-id="9506d-188">Build the application.</span></span> <span data-ttu-id="9506d-189">Ouvrez *site.min.css* et remarquez que le contenu de *custom.css* est annexé à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="9506d-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="9506d-190">Regroupement et minification basés sur l’environnement</span><span class="sxs-lookup"><span data-stu-id="9506d-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="9506d-191">Comme meilleure pratique, les fichiers groupés et minifiés de votre application doivent être utilisés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="9506d-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="9506d-192">Pendant le développement, les fichiers originaux facilitent le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9506d-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="9506d-193">Spécifiez les fichiers à inclure dans vos pages en utilisant [l’Aide à l’étiquette environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues.</span><span class="sxs-lookup"><span data-stu-id="9506d-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="9506d-194">L’Environnement Tag Helper ne rend son contenu que lorsqu’il est en cours d’exécution dans des [environnements](xref:fundamentals/environments)spécifiques .</span><span class="sxs-lookup"><span data-stu-id="9506d-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9506d-195">L’étiquette suivante `environment` rend les fichiers CSS non `Development` traités lorsqu’ils sont exécutés dans l’environnement :</span><span class="sxs-lookup"><span data-stu-id="9506d-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="9506d-196">L’étiquette suivante `environment` rend les fichiers CSS groupés et minifiés lorsqu’ils sont en cours d’exécution dans un environnement autre que `Development`.</span><span class="sxs-lookup"><span data-stu-id="9506d-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="9506d-197">Par exemple, `Production` l’exécution ou `Staging` déclenche le rendu de ces feuilles de style:</span><span class="sxs-lookup"><span data-stu-id="9506d-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="9506d-198">Consommer bundleconfig.json de Gulp</span><span class="sxs-lookup"><span data-stu-id="9506d-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="9506d-199">Il y a des cas où le flux de travail de regroupement et de minification d’une application nécessite un traitement supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="9506d-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="9506d-200">Les exemples incluent l’optimisation des images, le busting de cache, et le traitement d’actifs CDN.</span><span class="sxs-lookup"><span data-stu-id="9506d-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="9506d-201">Pour répondre à ces exigences, vous pouvez convertir le flux de travail de regroupement et de minification pour utiliser Gulp.</span><span class="sxs-lookup"><span data-stu-id="9506d-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="9506d-202">Convertir manuellement le flux de travail de regroupement et de minification pour utiliser Gulp</span><span class="sxs-lookup"><span data-stu-id="9506d-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="9506d-203">Ajouter un fichier *package.json,* avec ce qui suit, `devDependencies`à la racine du projet:</span><span class="sxs-lookup"><span data-stu-id="9506d-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="9506d-204">Le `gulp-uglify` module ne prend pas en charge ECMAScript (ES) 2015 / ES6 et plus tard.</span><span class="sxs-lookup"><span data-stu-id="9506d-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="9506d-205">Installez [le goux-terser](https://www.npmjs.com/package/gulp-terser) au lieu d’utiliser `gulp-uglify` ES2015 / ES6 ou plus tard.</span><span class="sxs-lookup"><span data-stu-id="9506d-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="9506d-206">Installez les dépendances en exécutant la commande suivante au même niveau que *package.json*:</span><span class="sxs-lookup"><span data-stu-id="9506d-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="9506d-207">Installer l’IMC Gulp comme dépendance mondiale :</span><span class="sxs-lookup"><span data-stu-id="9506d-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="9506d-208">Copiez le fichier *gulpfile.js* ci-dessous sur la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="9506d-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="9506d-209">Exécuter les tâches Gulp</span><span class="sxs-lookup"><span data-stu-id="9506d-209">Run Gulp tasks</span></span>

<span data-ttu-id="9506d-210">Pour déclencher la tâche de minification Gulp avant la construction du projet dans Visual Studio, ajoutez la [cible MSBuild](/visualstudio/msbuild/msbuild-targets) suivante au fichier '.csproj:</span><span class="sxs-lookup"><span data-stu-id="9506d-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="9506d-211">Dans cet exemple, toutes `MyPreCompileTarget` les tâches définies dans `Build` la cible s’exécutent avant la cible prédéfinie.</span><span class="sxs-lookup"><span data-stu-id="9506d-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="9506d-212">Sortie similaire à ce qui suit apparaît dans la fenêtre de sortie de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="9506d-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9506d-213">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9506d-213">Additional resources</span></span>

* [<span data-ttu-id="9506d-214">Utiliser Grunt</span><span class="sxs-lookup"><span data-stu-id="9506d-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="9506d-215">Utiliser plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="9506d-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="9506d-216">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="9506d-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
