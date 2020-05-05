---
title: commande dotnet aspnet-codegenerator
author: rick-anderson
description: La commande dotnet aspnet-codegenerator structure des projets ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 58f7aa30d3e916307437d56c61e80765ac0c21cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766470"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="aa1c0-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="aa1c0-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="aa1c0-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aa1c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aa1c0-105">`dotnet aspnet-codegenerator` - Exécute le moteur de génération de modèles automatique ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="aa1c0-106">`dotnet aspnet-codegenerator` étant uniquement requis pour générer automatiquement des modèles à partir de la ligne de commande, il n’est pas nécessaire d’utiliser la génération de modèles automatique avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="aa1c0-107">Cet article s’applique au [SDK .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) et ultérieur.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="aa1c0-108">Installation d’aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="aa1c0-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="aa1c0-109">`dotnet-aspnet-codegenerator` est un [outil global](/dotnet/core/tools/global-tools) qui doit être installé.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="aa1c0-110">La commande suivante installe la dernière version stable de l’outil `dotnet-aspnet-codegenerator` :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="aa1c0-111">La commande suivante met à jour `dotnet-aspnet-codegenerator` vers la dernière version stable disponible à partir du SDK .NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="aa1c0-112">Synopsis</span><span class="sxs-lookup"><span data-stu-id="aa1c0-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="aa1c0-113">Description</span><span class="sxs-lookup"><span data-stu-id="aa1c0-113">Description</span></span>

<span data-ttu-id="aa1c0-114">La commande globale `dotnet aspnet-codegenerator` exécute le générateur de code ASP.NET Core et le moteur de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="aa1c0-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="aa1c0-115">Arguments</span></span>

`generator`

<span data-ttu-id="aa1c0-116">Le générateur de code à effectuer.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-116">The code generator to run.</span></span> <span data-ttu-id="aa1c0-117">Les générateurs suivants sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-117">The following generators are available:</span></span>

| <span data-ttu-id="aa1c0-118">Générateur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-118">Generator</span></span> | <span data-ttu-id="aa1c0-119">Opération</span><span class="sxs-lookup"><span data-stu-id="aa1c0-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="aa1c0-120">superficie</span><span class="sxs-lookup"><span data-stu-id="aa1c0-120">area</span></span>      | [<span data-ttu-id="aa1c0-121">Génération de modèles automatique pour une zone</span><span class="sxs-lookup"><span data-stu-id="aa1c0-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="aa1c0-122">contrôleur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-122">controller</span></span>| [<span data-ttu-id="aa1c0-123">Génération de modèles automatique pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="aa1c0-124">identité</span><span class="sxs-lookup"><span data-stu-id="aa1c0-124">identity</span></span>  | [<span data-ttu-id="aa1c0-125">Génération de modèles automatique pour une identité</span><span class="sxs-lookup"><span data-stu-id="aa1c0-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="aa1c0-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="aa1c0-126">razorpage</span></span> | [<span data-ttu-id="aa1c0-127">Génération de modèles automatique pour Razor Pages</span><span class="sxs-lookup"><span data-stu-id="aa1c0-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="aa1c0-128">vue</span><span class="sxs-lookup"><span data-stu-id="aa1c0-128">view</span></span>      | [<span data-ttu-id="aa1c0-129">Génération de modèles automatique pour une vue</span><span class="sxs-lookup"><span data-stu-id="aa1c0-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="aa1c0-130">Options</span><span class="sxs-lookup"><span data-stu-id="aa1c0-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="aa1c0-131">Spécifie le répertoire du package NuGet.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="aa1c0-132">Définit la configuration de build.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-132">Defines the build configuration.</span></span> <span data-ttu-id="aa1c0-133">La valeur par défaut est `Debug`.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="aa1c0-134">[Framework](/dotnet/standard/frameworks) cible à utiliser.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="aa1c0-135">Par exemple : `net46`.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="aa1c0-136">Le chemin de base de génération.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="aa1c0-137">Affiche une aide brève pour la commande.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="aa1c0-138">Ne génère pas le projet avant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-138">Doesn't build the project before running.</span></span> <span data-ttu-id="aa1c0-139">L’indicateur `--no-restore` est également défini implicitement.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="aa1c0-140">Spécifie le chemin du fichier projet à exécuter (nom de dossier ou chemin complet).</span><span class="sxs-lookup"><span data-stu-id="aa1c0-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="aa1c0-141">Si aucune valeur n’est spécifiée, le répertoire actif est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="aa1c0-142">Options du générateur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-142">Generator options</span></span>

<span data-ttu-id="aa1c0-143">Les sections suivantes décrivent en détail les options disponibles pour les générateurs pris en charge :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="aa1c0-144">Domaine</span><span class="sxs-lookup"><span data-stu-id="aa1c0-144">Area</span></span>
* <span data-ttu-id="aa1c0-145">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-145">Controller</span></span>
* <span data-ttu-id="aa1c0-146">Identité</span><span class="sxs-lookup"><span data-stu-id="aa1c0-146">Identity</span></span>  
* <span data-ttu-id="aa1c0-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="aa1c0-147">Razorpage</span></span>
* <span data-ttu-id="aa1c0-148">Affichage</span><span class="sxs-lookup"><span data-stu-id="aa1c0-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="aa1c0-149">Options de zone</span><span class="sxs-lookup"><span data-stu-id="aa1c0-149">Area options</span></span>

<span data-ttu-id="aa1c0-150">Cet outil est conçu pour les projets web ASP.NET Core avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="aa1c0-151">Il n’est pas destinée aux applications Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="aa1c0-152">Utilisation : `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="aa1c0-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="aa1c0-153">La commande précédente génère les dossiers suivants :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="aa1c0-154">*Zones (Areas)*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-154">*Areas*</span></span>
  * <span data-ttu-id="aa1c0-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="aa1c0-156">*Contrôleurs*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-156">*Controllers*</span></span>
    * <span data-ttu-id="aa1c0-157">*Données*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-157">*Data*</span></span>
    * <span data-ttu-id="aa1c0-158">*Modèles*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-158">*Models*</span></span>
    * <span data-ttu-id="aa1c0-159">*Views*</span><span class="sxs-lookup"><span data-stu-id="aa1c0-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="aa1c0-160">Options de contrôleur</span><span class="sxs-lookup"><span data-stu-id="aa1c0-160">Controller options</span></span>

<span data-ttu-id="aa1c0-161">Le tableau suivant répertorie les `aspnet-codegenerator` `controller` options `razorpage`pour et :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="aa1c0-162">Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="aa1c0-163">Option</span><span class="sxs-lookup"><span data-stu-id="aa1c0-163">Option</span></span>               | <span data-ttu-id="aa1c0-164">Description</span><span class="sxs-lookup"><span data-stu-id="aa1c0-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="aa1c0-165">--controllerName ou -name</span><span class="sxs-lookup"><span data-stu-id="aa1c0-165">--controllerName or -name</span></span> | <span data-ttu-id="aa1c0-166">Nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-166">Name of the controller.</span></span> |
| <span data-ttu-id="aa1c0-167">--useAsyncActions ou -async</span><span class="sxs-lookup"><span data-stu-id="aa1c0-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="aa1c0-168">Générer des actions asynchrones du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="aa1c0-169">--noViews ou -nv</span><span class="sxs-lookup"><span data-stu-id="aa1c0-169">--noViews or -nv</span></span> | <span data-ttu-id="aa1c0-170">Ne générer **aucune** vue.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-170">Generate **no** views.</span></span> |
| <span data-ttu-id="aa1c0-171">--restWithNoViews ou -api</span><span class="sxs-lookup"><span data-stu-id="aa1c0-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="aa1c0-172">Générer un contrôleur avec l’API de style REST.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="aa1c0-173">`noViews` est supposé et les options associées à la vue sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="aa1c0-174">--readWriteActions ou -actions</span><span class="sxs-lookup"><span data-stu-id="aa1c0-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="aa1c0-175">Générer un contrôleur avec actions en lecture/écriture sans modèle.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="aa1c0-176">Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="aa1c0-177">Consultez [Générer automatiquement le modèle de film](/aspnet/core/tutorials/razor-pages/model) pour obtenir un exemple de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="aa1c0-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="aa1c0-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="aa1c0-179">Les Razor Pages peuvent être structurées individuellement en spécifiant le nom de la nouvelle page et le modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="aa1c0-180">Les modèles pris en charge sont :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="aa1c0-181">Par exemple, la commande suivante utilise le modèle de modification pour générer *MyEdit.cshtml* et *MyEdit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="aa1c0-182">En règle générale, le modèle et le nom de fichier générés ne sont pas spécifiés, et les modèles suivants sont créés :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="aa1c0-183">Le tableau suivant répertorie les `aspnet-codegenerator` `razorpage` options `controller`pour et :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="aa1c0-184">Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="aa1c0-185">Option</span><span class="sxs-lookup"><span data-stu-id="aa1c0-185">Option</span></span>               | <span data-ttu-id="aa1c0-186">Description</span><span class="sxs-lookup"><span data-stu-id="aa1c0-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="aa1c0-187">--namespaceName ou -namespace</span><span class="sxs-lookup"><span data-stu-id="aa1c0-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="aa1c0-188">Nom de l’espace de noms à utiliser pour le PageModel généré</span><span class="sxs-lookup"><span data-stu-id="aa1c0-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="aa1c0-189">--partialView ou -partial</span><span class="sxs-lookup"><span data-stu-id="aa1c0-189">--partialView or -partial</span></span> | <span data-ttu-id="aa1c0-190">Générer une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-190">Generate a partial view.</span></span> <span data-ttu-id="aa1c0-191">Les options de mise en page -l et -udl sont ignorées si ceci est spécifié.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="aa1c0-192">--noPageModel ou -npm</span><span class="sxs-lookup"><span data-stu-id="aa1c0-192">--noPageModel or -npm</span></span> | <span data-ttu-id="aa1c0-193">Choisir de ne pas générer une classe PageModel pour le modèle vide</span><span class="sxs-lookup"><span data-stu-id="aa1c0-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="aa1c0-194">Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="aa1c0-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="aa1c0-195">Consultez [Générer automatiquement le modèle de film](/aspnet/core/tutorials/razor-pages/model) pour obtenir un exemple de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="aa1c0-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="aa1c0-196">Voir [l' Identity échafaudage](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="aa1c0-196">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
