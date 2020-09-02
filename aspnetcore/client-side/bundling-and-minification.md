---
title: Regrouper et réduire les ressources statiques dans ASP.NET Core
author: scottaddie
description: Découvrez comment optimiser les ressources statiques dans une application Web ASP.NET Core en appliquant des techniques de regroupement et de minimisation.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: f696df0b421e5aab6f50cfaec3ca8edac894cea9
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379391"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="44dce-103">Regrouper et réduire les ressources statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44dce-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="44dce-104">Par [Scott Addie](https://twitter.com/Scott_Addie) et [David pin](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="44dce-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="44dce-105">Cet article explique les avantages de l’application du regroupement et de la minimisation, notamment la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="44dce-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="44dce-106">Qu’est-ce que le regroupement et la minimisation ?</span><span class="sxs-lookup"><span data-stu-id="44dce-106">What is bundling and minification</span></span>

<span data-ttu-id="44dce-107">Le regroupement et la minimisation sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application Web.</span><span class="sxs-lookup"><span data-stu-id="44dce-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="44dce-108">Utilisés ensemble, le regroupement et la minimisation améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des ressources statiques demandées.</span><span class="sxs-lookup"><span data-stu-id="44dce-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="44dce-109">Le regroupement et la minimisation améliorent principalement le temps de chargement de la première page de la demande.</span><span class="sxs-lookup"><span data-stu-id="44dce-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="44dce-110">Une fois qu’une page Web a été demandée, le navigateur met en cache les ressources statiques (JavaScript, CSS et images).</span><span class="sxs-lookup"><span data-stu-id="44dce-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="44dce-111">Par conséquent, le regroupement et la minimisation n’améliorent pas les performances lorsque vous demandez la même page ou les mêmes pages sur le même site demandant les mêmes ressources.</span><span class="sxs-lookup"><span data-stu-id="44dce-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="44dce-112">Si l’en-tête Expires n’est pas défini correctement sur les ressources et si le regroupement et la minimisation ne sont pas utilisés, les heuristiques d’actualisation du navigateur marquent les ressources obsolètes après quelques jours.</span><span class="sxs-lookup"><span data-stu-id="44dce-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="44dce-113">En outre, le navigateur requiert une demande de validation pour chaque ressource.</span><span class="sxs-lookup"><span data-stu-id="44dce-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="44dce-114">Dans ce cas, le regroupement et la minimisation améliorent les performances même après la première demande de page.</span><span class="sxs-lookup"><span data-stu-id="44dce-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="44dce-115">Regroupement</span><span class="sxs-lookup"><span data-stu-id="44dce-115">Bundling</span></span>

<span data-ttu-id="44dce-116">Le regroupement consiste à combiner plusieurs fichiers en un seul.</span><span class="sxs-lookup"><span data-stu-id="44dce-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="44dce-117">Le regroupement réduit le nombre de demandes de serveur nécessaires pour afficher une ressource Web, telle qu’une page Web.</span><span class="sxs-lookup"><span data-stu-id="44dce-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="44dce-118">Vous pouvez créer un nombre quelconque de regroupements individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP du navigateur vers le serveur ou à partir du service qui fournit votre application.</span><span class="sxs-lookup"><span data-stu-id="44dce-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="44dce-119">Cela a pour effet d’améliorer les performances de chargement de la première page.</span><span class="sxs-lookup"><span data-stu-id="44dce-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="44dce-120">Minimisation</span><span class="sxs-lookup"><span data-stu-id="44dce-120">Minification</span></span>

<span data-ttu-id="44dce-121">La minimisation supprime les caractères inutiles du code sans modifier la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="44dce-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="44dce-122">Le résultat est une réduction significative de la taille des ressources demandées (telles que CSS, les images et les fichiers JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44dce-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="44dce-123">Les effets secondaires communs de la minimisation incluent la réduction des noms de variables à un caractère et la suppression des commentaires et des espaces inutiles.</span><span class="sxs-lookup"><span data-stu-id="44dce-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="44dce-124">Considérons la fonction JavaScript suivante :</span><span class="sxs-lookup"><span data-stu-id="44dce-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="44dce-125">La minimisation réduit la fonction à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="44dce-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="44dce-126">En plus de supprimer les commentaires et les espaces superflus, les noms de paramètres et de variables suivants ont été renommés comme suit :</span><span class="sxs-lookup"><span data-stu-id="44dce-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="44dce-127">Original</span><span class="sxs-lookup"><span data-stu-id="44dce-127">Original</span></span> | <span data-ttu-id="44dce-128">Affectation d'un nouveau nom</span><span class="sxs-lookup"><span data-stu-id="44dce-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="44dce-129">Impact du regroupement et de la minimisation</span><span class="sxs-lookup"><span data-stu-id="44dce-129">Impact of bundling and minification</span></span>

<span data-ttu-id="44dce-130">Le tableau suivant présente les différences entre le chargement individuel des ressources et l’utilisation du regroupement et de la minimisation :</span><span class="sxs-lookup"><span data-stu-id="44dce-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="44dce-131">Action</span><span class="sxs-lookup"><span data-stu-id="44dce-131">Action</span></span> | <span data-ttu-id="44dce-132">Avec B/M</span><span class="sxs-lookup"><span data-stu-id="44dce-132">With B/M</span></span> | <span data-ttu-id="44dce-133">Sans B/M</span><span class="sxs-lookup"><span data-stu-id="44dce-133">Without B/M</span></span> | <span data-ttu-id="44dce-134">Changement</span><span class="sxs-lookup"><span data-stu-id="44dce-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="44dce-135">Demandes de fichier</span><span class="sxs-lookup"><span data-stu-id="44dce-135">File Requests</span></span>  | <span data-ttu-id="44dce-136">7</span><span class="sxs-lookup"><span data-stu-id="44dce-136">7</span></span>   | <span data-ttu-id="44dce-137">18</span><span class="sxs-lookup"><span data-stu-id="44dce-137">18</span></span>     | <span data-ttu-id="44dce-138">157%</span><span class="sxs-lookup"><span data-stu-id="44dce-138">157%</span></span>
<span data-ttu-id="44dce-139">Ko transférés</span><span class="sxs-lookup"><span data-stu-id="44dce-139">KB Transferred</span></span> | <span data-ttu-id="44dce-140">156</span><span class="sxs-lookup"><span data-stu-id="44dce-140">156</span></span> | <span data-ttu-id="44dce-141">264,68</span><span class="sxs-lookup"><span data-stu-id="44dce-141">264.68</span></span> | <span data-ttu-id="44dce-142">70 %</span><span class="sxs-lookup"><span data-stu-id="44dce-142">70%</span></span>
<span data-ttu-id="44dce-143">Temps de chargement (MS)</span><span class="sxs-lookup"><span data-stu-id="44dce-143">Load Time (ms)</span></span> | <span data-ttu-id="44dce-144">885</span><span class="sxs-lookup"><span data-stu-id="44dce-144">885</span></span> | <span data-ttu-id="44dce-145">2360</span><span class="sxs-lookup"><span data-stu-id="44dce-145">2360</span></span>   | <span data-ttu-id="44dce-146">167%</span><span class="sxs-lookup"><span data-stu-id="44dce-146">167%</span></span>

<span data-ttu-id="44dce-147">Les navigateurs sont relativement détaillés en ce qui concerne les en-têtes de requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="44dce-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="44dce-148">La mesure Total octets envoyés a vu une réduction significative lors du regroupement.</span><span class="sxs-lookup"><span data-stu-id="44dce-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="44dce-149">Le temps de chargement montre une amélioration significative, mais cet exemple s’est exécuté localement.</span><span class="sxs-lookup"><span data-stu-id="44dce-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="44dce-150">Des gains de performances plus élevés sont réalisés lorsque vous utilisez le regroupement et la minimisation avec les ressources transférées sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="44dce-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="44dce-151">Choisir une stratégie de regroupement et de minimisation</span><span class="sxs-lookup"><span data-stu-id="44dce-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="44dce-152">Les modèles de Razor projet MVC et pages fournissent une solution pour le regroupement et la minimisation consistant en un fichier de configuration JSON.</span><span class="sxs-lookup"><span data-stu-id="44dce-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="44dce-153">Des outils tiers, tels que le testeur de tâches [grunt](xref:client-side/using-grunt) , accomplissent les mêmes tâches avec un peu plus de complexité.</span><span class="sxs-lookup"><span data-stu-id="44dce-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="44dce-154">Un outil tiers est une solution idéale lorsque votre flux de travail de développement nécessite un traitement au-delà du regroupement et de la minimisation &mdash; , tels que le tissu et l’optimisation d’image.</span><span class="sxs-lookup"><span data-stu-id="44dce-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="44dce-155">En utilisant le regroupement et la minimisation au moment du design, les fichiers minimisés sont créés avant le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="44dce-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="44dce-156">Le regroupement et le minimisation avant le déploiement offrent l’avantage de réduire la charge du serveur.</span><span class="sxs-lookup"><span data-stu-id="44dce-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="44dce-157">Toutefois, il est important de reconnaître que le regroupement et la minimisation au moment du design augmentent la complexité de la génération et ne fonctionne qu’avec les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="44dce-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="44dce-158">Configurer le regroupement et la minimisation</span><span class="sxs-lookup"><span data-stu-id="44dce-158">Configure bundling and minification</span></span>

> [!NOTE]
> <span data-ttu-id="44dce-159">Pour que cela fonctionne, le package NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) doit être ajouté à votre projet.</span><span class="sxs-lookup"><span data-stu-id="44dce-159">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package needs to be added to your project for this to work.</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="44dce-160">Dans ASP.NET Core 2,0 ou version antérieure, les modèles de projet MVC et Razor pages fournissent un *bundleconfig.jssur* le fichier de configuration qui définit les options pour chaque Bundle :</span><span class="sxs-lookup"><span data-stu-id="44dce-160">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="44dce-161">Dans ASP.NET Core 2,1 ou version ultérieure, ajoutez un nouveau fichier JSON, nommé *bundleconfig.jssur*, à la racine du projet MVC ou Razor pages.</span><span class="sxs-lookup"><span data-stu-id="44dce-161">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="44dce-162">Incluez le code JSON suivant dans ce fichier comme point de départ :</span><span class="sxs-lookup"><span data-stu-id="44dce-162">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="44dce-163">Le *bundleconfig.jssur* le fichier définit les options pour chaque bundle.</span><span class="sxs-lookup"><span data-stu-id="44dce-163">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="44dce-164">Dans l’exemple précédent, une configuration de regroupement unique est définie pour les fichiers JavaScript personnalisés (*wwwroot/js/site.js*) et StyleSheet (*wwwroot/CSS/site. CSS*).</span><span class="sxs-lookup"><span data-stu-id="44dce-164">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="44dce-165">Les options de configuration comprennent ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="44dce-165">Configuration options include:</span></span>

* <span data-ttu-id="44dce-166">`outputFileName`: Nom du fichier de Bundle à générer.</span><span class="sxs-lookup"><span data-stu-id="44dce-166">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="44dce-167">Peut contenir un chemin d’accès relatif à partir de l' *bundleconfig.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="44dce-167">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="44dce-168">**Obligatoire**</span><span class="sxs-lookup"><span data-stu-id="44dce-168">**required**</span></span>
* <span data-ttu-id="44dce-169">`inputFiles`: Tableau de fichiers à regrouper.</span><span class="sxs-lookup"><span data-stu-id="44dce-169">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="44dce-170">Il s’agit de chemins d’accès relatifs au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="44dce-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="44dce-171">**facultatif**, \* une valeur vide génère un fichier de sortie vide.</span><span class="sxs-lookup"><span data-stu-id="44dce-171">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="44dce-172">les modèles [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="44dce-172">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="44dce-173">`minify`: Options de minimisation pour le type de sortie.</span><span class="sxs-lookup"><span data-stu-id="44dce-173">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="44dce-174">**facultatif**, *par défaut `minify: { enabled: true }` :*</span><span class="sxs-lookup"><span data-stu-id="44dce-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="44dce-175">Les options de configuration sont disponibles pour chaque type de fichier de sortie.</span><span class="sxs-lookup"><span data-stu-id="44dce-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="44dce-176">Minifier CSS</span><span class="sxs-lookup"><span data-stu-id="44dce-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="44dce-177">Minifier JavaScript</span><span class="sxs-lookup"><span data-stu-id="44dce-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="44dce-178">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="44dce-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="44dce-179">`includeInProject`: Indicateur précisant s’il faut ajouter les fichiers générés au fichier projet.</span><span class="sxs-lookup"><span data-stu-id="44dce-179">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="44dce-180">**facultatif**, *valeur par défaut-false*</span><span class="sxs-lookup"><span data-stu-id="44dce-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="44dce-181">`sourceMap`: Indicateur précisant s’il faut générer un mappage source pour le fichier groupé.</span><span class="sxs-lookup"><span data-stu-id="44dce-181">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="44dce-182">**facultatif**, *valeur par défaut-false*</span><span class="sxs-lookup"><span data-stu-id="44dce-182">**optional**, *default - false*</span></span>
* <span data-ttu-id="44dce-183">`sourceMapRootPath`: Chemin d’accès racine pour le stockage du fichier de mappage source généré.</span><span class="sxs-lookup"><span data-stu-id="44dce-183">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="44dce-184">Ajouter des fichiers au flux de travail</span><span class="sxs-lookup"><span data-stu-id="44dce-184">Add files to workflow</span></span>

<span data-ttu-id="44dce-185">Prenons l’exemple d’un fichier *. CSS personnalisé* supplémentaire qui ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="44dce-185">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="44dce-186">Pour réduire *Custom. CSS* et le regrouper avec *site. CSS* dans un fichier *site. min. CSS* , ajoutez le chemin d’accès relatif à *bundleconfig.jssur*:</span><span class="sxs-lookup"><span data-stu-id="44dce-186">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="44dce-187">Vous pouvez également utiliser le modèle globbing suivant :</span><span class="sxs-lookup"><span data-stu-id="44dce-187">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="44dce-188">Ce modèle globbing correspond à tous les fichiers CSS et exclut le modèle de fichier minimisés.</span><span class="sxs-lookup"><span data-stu-id="44dce-188">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="44dce-189">Générez l’application.</span><span class="sxs-lookup"><span data-stu-id="44dce-189">Build the application.</span></span> <span data-ttu-id="44dce-190">Ouvrez *site. min. CSS* et notez que le contenu du fichier *Custom. CSS* est ajouté à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="44dce-190">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="44dce-191">Regroupement et minimisation basés sur l’environnement</span><span class="sxs-lookup"><span data-stu-id="44dce-191">Environment-based bundling and minification</span></span>

<span data-ttu-id="44dce-192">Il est recommandé d’utiliser les fichiers regroupés et minimisés de votre application dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="44dce-192">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="44dce-193">Pendant le développement, les fichiers d’origine facilitent le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="44dce-193">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="44dce-194">Spécifiez les fichiers à inclure dans vos pages à l’aide du [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues.</span><span class="sxs-lookup"><span data-stu-id="44dce-194">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="44dce-195">Le tag Helper d’environnement affiche uniquement son contenu lorsqu’il s’exécute dans des [environnements](xref:fundamentals/environments)spécifiques.</span><span class="sxs-lookup"><span data-stu-id="44dce-195">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="44dce-196">La `environment` balise suivante affiche les fichiers CSS non traités lors de l’exécution dans l' `Development` environnement :</span><span class="sxs-lookup"><span data-stu-id="44dce-196">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="44dce-197">La `environment` balise suivante affiche les fichiers CSS regroupés et minimisés lorsqu’ils s’exécutent dans un environnement autre que `Development` .</span><span class="sxs-lookup"><span data-stu-id="44dce-197">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="44dce-198">Par exemple, l’exécution de dans `Production` ou `Staging` déclenche le rendu de ces feuilles de style :</span><span class="sxs-lookup"><span data-stu-id="44dce-198">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="44dce-199">Utiliser bundleconfig.jsà partir de Gulp</span><span class="sxs-lookup"><span data-stu-id="44dce-199">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="44dce-200">Dans certains cas, le flux de travail de regroupement et de minimisation d’une application nécessite un traitement supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="44dce-200">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="44dce-201">Les exemples incluent l’optimisation des images, la combustion du cache et le traitement des ressources CDN.</span><span class="sxs-lookup"><span data-stu-id="44dce-201">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="44dce-202">Pour répondre à ces exigences, vous pouvez convertir le flux de travail de regroupement et de minimisation pour utiliser Gulp.</span><span class="sxs-lookup"><span data-stu-id="44dce-202">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="44dce-203">Convertir manuellement le flux de travail de regroupement et de minimisation pour utiliser Gulp</span><span class="sxs-lookup"><span data-stu-id="44dce-203">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="44dce-204">Ajoutez un *package.jssur* le fichier, avec les éléments suivants `devDependencies` , à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="44dce-204">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="44dce-205">Le `gulp-uglify` module ne prend pas en charge ECMAScript (es) 2015/ES6 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="44dce-205">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="44dce-206">Installez [Gulp-terser](https://www.npmjs.com/package/gulp-terser) au lieu de `gulp-uglify` pour utiliser ES2015/ES6 ou une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="44dce-206">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="44dce-207">Installez les dépendances en exécutant la commande suivante au même niveau que *package.jssur*:</span><span class="sxs-lookup"><span data-stu-id="44dce-207">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="44dce-208">Installez Gulp CLI en tant que dépendance globale :</span><span class="sxs-lookup"><span data-stu-id="44dce-208">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="44dce-209">Copiez le fichier *gulpfile.js* ci-dessous sur la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="44dce-209">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="44dce-210">Exécuter des tâches Gulp</span><span class="sxs-lookup"><span data-stu-id="44dce-210">Run Gulp tasks</span></span>

<span data-ttu-id="44dce-211">Pour déclencher la tâche de minimisation Gulp avant la génération du projet dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="44dce-211">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="44dce-212">Installez le package NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="44dce-212">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="44dce-213">Ajoutez la [cible MSBuild](/visualstudio/msbuild/msbuild-targets) suivante au fichier projet :</span><span class="sxs-lookup"><span data-stu-id="44dce-213">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="44dce-214">Dans cet exemple, toutes les tâches définies dans la `MyPreCompileTarget` cible s’exécutent avant la cible prédéfinie `Build` .</span><span class="sxs-lookup"><span data-stu-id="44dce-214">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="44dce-215">Une sortie similaire à ce qui suit apparaît dans la fenêtre sortie de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="44dce-215">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="44dce-216">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="44dce-216">Additional resources</span></span>

* [<span data-ttu-id="44dce-217">Utiliser Grunt</span><span class="sxs-lookup"><span data-stu-id="44dce-217">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="44dce-218">Utiliser plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="44dce-218">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="44dce-219">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="44dce-219">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
