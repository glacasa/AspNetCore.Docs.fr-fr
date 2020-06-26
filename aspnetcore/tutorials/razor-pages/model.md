---
title: Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core
author: rick-anderson
description: Partie 2 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: 053e35e78b2bf58fded49c9a2860060d6a81c544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408108"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="aaba5-103">Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="aaba5-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="aaba5-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aaba5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="aaba5-105">Dans cette section, des classes sont ajoutées pour la gestion des films.</span><span class="sxs-lookup"><span data-stu-id="aaba5-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="aaba5-106">Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="aaba5-107">EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="aaba5-108">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="aaba5-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="aaba5-109">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="aaba5-110">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="aaba5-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-112">Cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="aaba5-113">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-113">Name the folder *Models*.</span></span>

<span data-ttu-id="aaba5-114">Cliquez avec le bouton droit sur le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-114">Right click the *Models* folder.</span></span> <span data-ttu-id="aaba5-115">Sélectionnez **Ajouter**une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="aaba5-116">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaba5-118">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="aaba5-119">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-120">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aaba5-121">Dans panneau solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="aaba5-122">Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="aaba5-123">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="aaba5-124">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="aaba5-125">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="aaba5-126">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="aaba5-127">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="aaba5-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="aaba5-128">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="aaba5-128">Scaffold the movie model</span></span>

<span data-ttu-id="aaba5-129">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="aaba5-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="aaba5-130">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="aaba5-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-132">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="aaba5-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="aaba5-133">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="aaba5-134">Nommez le dossier *Movies*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-134">Name the folder *Movies*</span></span>

<span data-ttu-id="aaba5-135">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="aaba5-137">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez \*\* Razor pages à l’aide de Entity Framework (CRUD)\*\* > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="aaba5-139">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="aaba5-140">Dans la liste déroulante **Classe de modèle**, sélectionnez **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="aaba5-141">Dans la ligne **Classe du contexte de données**, sélectionnez le signe (plus) **+** et changez le nom généré de RazorPagesMovie.**Models**.RazorPagesMovieContext en RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="aaba5-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="aaba5-142">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="aaba5-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="aaba5-143">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="aaba5-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="aaba5-144">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-144">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

<span data-ttu-id="aaba5-146">Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="aaba5-148">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="aaba5-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="aaba5-149">Installez l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="aaba5-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="aaba5-150">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="aaba5-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="aaba5-151">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="aaba5-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-152">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aaba5-153">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="aaba5-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="aaba5-154">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="aaba5-155">Nommez le dossier *Movies*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-155">Name the folder *Movies*</span></span>

<span data-ttu-id="aaba5-156">Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="aaba5-158">Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez \*\* Razor pages à l’aide de Entity Framework (CRUD)\*\* > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="aaba5-160">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="aaba5-161">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="aaba5-162">Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, RazorPagesMovie. **Données**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="aaba5-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="aaba5-163">[Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise.</span><span class="sxs-lookup"><span data-stu-id="aaba5-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="aaba5-164">Elle crée la classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="aaba5-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="aaba5-165">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-165">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="aaba5-167">Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="aaba5-168">Ajouter des outils EF</span><span class="sxs-lookup"><span data-stu-id="aaba5-168">Add EF tools</span></span>

<span data-ttu-id="aaba5-169">Exécutez la commande CLI .NET Core suivante :</span><span class="sxs-lookup"><span data-stu-id="aaba5-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="aaba5-170">La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aaba5-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="aaba5-171">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="aaba5-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-173">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="aaba5-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="aaba5-174">*Pages/Movies* : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="aaba5-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="aaba5-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="aaba5-176">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="aaba5-176">Updated</span></span>

* <span data-ttu-id="aaba5-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-177">*Startup.cs*</span></span>

<span data-ttu-id="aaba5-178">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="aaba5-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-179">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aaba5-180">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="aaba5-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="aaba5-181">*Pages/Movies* : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="aaba5-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="aaba5-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="aaba5-183">Mis à jour</span><span class="sxs-lookup"><span data-stu-id="aaba5-183">Updated</span></span>

* <span data-ttu-id="aaba5-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-184">*Startup.cs*</span></span>

<span data-ttu-id="aaba5-185">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="aaba5-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaba5-187">Le processus de génération de modèles automatique crée les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="aaba5-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="aaba5-188">*Pages/Movies* : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="aaba5-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="aaba5-189">Les fichiers créés sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="aaba5-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="aaba5-190">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="aaba5-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-192">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="aaba5-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="aaba5-193">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-193">Add an initial migration.</span></span>
* <span data-ttu-id="aaba5-194">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="aaba5-195">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="aaba5-197">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaba5-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-199">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="aaba5-200">Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ».</span><span class="sxs-lookup"><span data-stu-id="aaba5-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="aaba5-201">Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut.</span><span class="sxs-lookup"><span data-stu-id="aaba5-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="aaba5-202">Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »</span><span class="sxs-lookup"><span data-stu-id="aaba5-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="aaba5-203">Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="aaba5-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="aaba5-204">La commande migrations génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="aaba5-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="aaba5-205">Le schéma est basé sur le modèle spécifié dans `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="aaba5-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="aaba5-206">L’argument `InitialCreate` est utilisé pour nommer les migrations.</span><span class="sxs-lookup"><span data-stu-id="aaba5-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="aaba5-207">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="aaba5-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="aaba5-208">La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées.</span><span class="sxs-lookup"><span data-stu-id="aaba5-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="aaba5-209">Dans ce cas, `update` exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="aaba5-211">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="aaba5-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="aaba5-212">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="aaba5-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aaba5-213">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="aaba5-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aaba5-214">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="aaba5-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aaba5-215">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="aaba5-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aaba5-216">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="aaba5-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="aaba5-217">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="aaba5-218">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="aaba5-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="aaba5-219">`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="aaba5-220">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="aaba5-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aaba5-221">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="aaba5-222">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="aaba5-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="aaba5-223">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="aaba5-224">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="aaba5-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aaba5-225">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="aaba5-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aaba5-226">Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaba5-228">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-229">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aaba5-230">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="aaba5-231">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="aaba5-231">Test the app</span></span>

* <span data-ttu-id="aaba5-232">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="aaba5-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="aaba5-233">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="aaba5-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="aaba5-234">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="aaba5-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="aaba5-235">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-235">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="aaba5-237">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="aaba5-238">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="aaba5-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="aaba5-239">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="aaba5-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="aaba5-240">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="aaba5-241">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="aaba5-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaba5-242">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="aaba5-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="aaba5-243">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="aaba5-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aaba5-244">Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="aaba5-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="aaba5-245">Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="aaba5-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="aaba5-246">Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="aaba5-247">EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="aaba5-248">Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core.</span><span class="sxs-lookup"><span data-stu-id="aaba5-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="aaba5-249">Elles définissent les propriétés des données stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="aaba5-250">Ajouter un modèle de données</span><span class="sxs-lookup"><span data-stu-id="aaba5-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-252">Cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="aaba5-253">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-253">Name the folder *Models*.</span></span>

<span data-ttu-id="aaba5-254">Cliquez avec le bouton droit sur le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-254">Right click the *Models* folder.</span></span> <span data-ttu-id="aaba5-255">Sélectionnez **Ajouter**une  >  **classe**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="aaba5-256">Nommez la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaba5-258">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="aaba5-259">Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-260">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aaba5-261">Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie**, puis sélectionnez **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="aaba5-262">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="aaba5-263">Cliquez avec le bouton droit sur le dossier *Modèles*, puis sélectionnez **Ajouter** > **Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="aaba5-264">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="aaba5-265">Dans le volet gauche, sélectionnez **Général**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="aaba5-266">Dans le volet central, sélectionnez **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="aaba5-267">Nommez la classe **Movie**, puis sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="aaba5-268">Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.</span><span class="sxs-lookup"><span data-stu-id="aaba5-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="aaba5-269">Générer automatiquement le modèle de film</span><span class="sxs-lookup"><span data-stu-id="aaba5-269">Scaffold the movie model</span></span>

<span data-ttu-id="aaba5-270">Dans cette section, le modèle de film est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="aaba5-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="aaba5-271">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.</span><span class="sxs-lookup"><span data-stu-id="aaba5-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-273">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="aaba5-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="aaba5-274">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="aaba5-275">Nommez le dossier *Movies*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-275">Name the folder *Movies*</span></span>

<span data-ttu-id="aaba5-276">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/sca.png)

<span data-ttu-id="aaba5-278">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez \*\* Razor pages à l’aide de Entity Framework (CRUD)\*\* > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

<span data-ttu-id="aaba5-280">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="aaba5-281">Dans la liste déroulante **Classe de modèle**, sélectionnez **Film (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="aaba5-282">Dans la ligne **Classe du contexte de données**, sélectionnez le signe (plus) **+** et acceptez le nom généré **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="aaba5-283">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-283">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arp.png)

<span data-ttu-id="aaba5-285">Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="aaba5-287">Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="aaba5-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="aaba5-288">**Pour Windows**: exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="aaba5-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="aaba5-289">**Pour macOS et Linux**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="aaba5-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-290">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aaba5-291">Créer un dossier *Pages/Movies* :</span><span class="sxs-lookup"><span data-stu-id="aaba5-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="aaba5-292">Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="aaba5-293">Nommez le dossier *Movies*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-293">Name the folder *Movies*</span></span>

<span data-ttu-id="aaba5-294">Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

<span data-ttu-id="aaba5-296">Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez \*\* Razor pages à l’aide de Entity Framework (CRUD)\*\* > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="aaba5-298">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="aaba5-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="aaba5-299">Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="aaba5-300">Dans la ligne de la **classe de contexte de données** , tapez Select the **RazorPagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct.</span><span class="sxs-lookup"><span data-stu-id="aaba5-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="aaba5-301">Dans ce cas, il s’agit de **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="aaba5-302">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-302">Select **Add**.</span></span>

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

<span data-ttu-id="aaba5-304">Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="aaba5-305">Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="aaba5-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="aaba5-306">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="aaba5-306">Files created</span></span>

* <span data-ttu-id="aaba5-307">*Pages/Movies* : Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="aaba5-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="aaba5-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="aaba5-309">Fichier mis à jour</span><span class="sxs-lookup"><span data-stu-id="aaba5-309">File updated</span></span>

* <span data-ttu-id="aaba5-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="aaba5-310">*Startup.cs*</span></span>

<span data-ttu-id="aaba5-311">Les fichiers créés et mis à jour sont expliqués dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="aaba5-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="aaba5-312">Migration initiale</span><span class="sxs-lookup"><span data-stu-id="aaba5-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaba5-314">Dans cette section, la console du gestionnaire de package est utilisée pour :</span><span class="sxs-lookup"><span data-stu-id="aaba5-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="aaba5-315">Ajoutez une migration initiale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-315">Add an initial migration.</span></span>
* <span data-ttu-id="aaba5-316">Mettez à jour la base de données avec la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="aaba5-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="aaba5-317">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="aaba5-319">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="aaba5-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="aaba5-320">La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.</span><span class="sxs-lookup"><span data-stu-id="aaba5-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="aaba5-321">Le schéma est basé sur le modèle spécifié dans `DbContext` (dans le fichier *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="aaba5-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="aaba5-322">L' `InitialCreate` argument est utilisé pour nommer la migration.</span><span class="sxs-lookup"><span data-stu-id="aaba5-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="aaba5-323">Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé.</span><span class="sxs-lookup"><span data-stu-id="aaba5-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="aaba5-324">Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="aaba5-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="aaba5-325">La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="aaba5-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="aaba5-326">La méthode `Up` crée la base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-328">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="aaba5-329">Les commandes précédentes génèrent l’avertissement suivant : «*aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne tiennent pas dans la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server qui peut prendre en charge toutes les valeurs à l’aide de’HasColumnType () '.* Vous pouvez ignorer cet avertissement. il sera corrigé dans un didacticiel ultérieur.</span><span class="sxs-lookup"><span data-stu-id="aaba5-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaba5-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaba5-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="aaba5-331">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="aaba5-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="aaba5-332">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="aaba5-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aaba5-333">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="aaba5-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aaba5-334">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="aaba5-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aaba5-335">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="aaba5-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aaba5-336">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="aaba5-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="aaba5-337">Examinez la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="aaba5-338">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="aaba5-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="aaba5-339">`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="aaba5-340">Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="aaba5-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aaba5-341">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="aaba5-342">Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="aaba5-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="aaba5-343">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="aaba5-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="aaba5-344">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="aaba5-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aaba5-345">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="aaba5-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aaba5-346">Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aaba5-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaba5-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaba5-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaba5-348">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaba5-349">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="aaba5-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aaba5-350">Examinez la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="aaba5-351">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="aaba5-351">Test the app</span></span>

* <span data-ttu-id="aaba5-352">Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="aaba5-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="aaba5-353">Si vous obtenez cette erreur :</span><span class="sxs-lookup"><span data-stu-id="aaba5-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="aaba5-354">Vous avez manqué [l’étape des migrations](#pmc).</span><span class="sxs-lookup"><span data-stu-id="aaba5-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="aaba5-355">Testez le lien **Créer**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-355">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="aaba5-357">Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`.</span><span class="sxs-lookup"><span data-stu-id="aaba5-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="aaba5-358">Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée.</span><span class="sxs-lookup"><span data-stu-id="aaba5-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="aaba5-359">Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="aaba5-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="aaba5-360">Testez les liens **Edit**, **Details** et **Delete**.</span><span class="sxs-lookup"><span data-stu-id="aaba5-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="aaba5-361">Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="aaba5-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaba5-362">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="aaba5-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="aaba5-363">[Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="aaba5-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
