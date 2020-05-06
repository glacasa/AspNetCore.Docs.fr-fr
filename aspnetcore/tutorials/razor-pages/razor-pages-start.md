---
title: 'Didacticiel : prise en main Razor des Pages dans ASP.net Core'
author: rick-anderson
description: Cette série de didacticiels montre comment utiliser Razor des Pages dans ASP.net core. Découvrez comment créer un modèle, générer du code pour Razor les pages, utiliser des Entity Framework Core et des SQL Server pour l’accès aux données, ajouter des fonctionnalités de recherche, ajouter une validation d’entrée et utiliser des migrations pour mettre à jour le modèle.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774871"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="e7754-104">Tutoriel : Bien démarrer avec Razor Pages dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7754-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="e7754-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7754-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="e7754-106">C’est le premier d’une série de tutoriels, qui décrit les principes fondamentaux liés à la génération d’une application web de Razor Pages dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7754-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="e7754-107">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="e7754-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="e7754-108">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="e7754-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e7754-109">Créer une application web Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e7754-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="e7754-110">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e7754-110">Run the app.</span></span>
> * <span data-ttu-id="e7754-111">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-111">Examine the project files.</span></span>

<span data-ttu-id="e7754-112">À la fin de ce didacticiel, vous disposez d’une application web Razor Pages fonctionnelle et générée dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="e7754-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="e7754-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e7754-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e7754-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7754-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e7754-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7754-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e7754-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e7754-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="e7754-118">Créer une application web Pages Razor</span><span class="sxs-lookup"><span data-stu-id="e7754-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e7754-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7754-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7754-120">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="e7754-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e7754-121">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e7754-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="e7754-122">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="e7754-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="e7754-123">Nommez le projet **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e7754-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="e7754-124">Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="e7754-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="e7754-125">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="e7754-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="e7754-126">Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="e7754-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="e7754-128">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="e7754-128">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e7754-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7754-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e7754-131">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e7754-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="e7754-132">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="e7754-133">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7754-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="e7754-134">La commande `dotnet new` crée un nouveau projet Razor Pages dans le dossier *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="e7754-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="e7754-135">La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e7754-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="e7754-136">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="e7754-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="e7754-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="e7754-137">Select **Yes**.</span></span>

  <span data-ttu-id="e7754-138">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e7754-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e7754-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e7754-140">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="e7754-140">Select **File** > **New Solution**.</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="e7754-142">Sélectionnez **.NET Core** > **Application** > **Application web** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e7754-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="e7754-144">Dans la boîte de dialogue **configurer votre nouvelle application Web** , définissez **Framework cible** sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="e7754-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![sélection de macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="e7754-146">Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="e7754-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="e7754-148">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="e7754-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="e7754-149">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="e7754-149">Examine the project files</span></span>

<span data-ttu-id="e7754-150">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="e7754-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="e7754-151">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="e7754-151">Pages folder</span></span>

<span data-ttu-id="e7754-152">Contient les pages Razor et les fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="e7754-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="e7754-153">Chaque page Razor est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e7754-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="e7754-154">Un fichier *.cshtml* qui contient le balisage HTML avec du code C# en utilisant la syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="e7754-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="e7754-155">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="e7754-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="e7754-156">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="e7754-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="e7754-157">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="e7754-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="e7754-158">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="e7754-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="e7754-159">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="e7754-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="e7754-160">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="e7754-160">wwwroot folder</span></span>

<span data-ttu-id="e7754-161">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="e7754-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="e7754-162">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="e7754-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="e7754-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="e7754-163">appSettings.json</span></span>

<span data-ttu-id="e7754-164">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="e7754-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="e7754-165">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e7754-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="e7754-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="e7754-166">Program.cs</span></span>

<span data-ttu-id="e7754-167">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="e7754-167">Contains the entry point for the program.</span></span> <span data-ttu-id="e7754-168">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="e7754-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="e7754-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="e7754-169">Startup.cs</span></span>

<span data-ttu-id="e7754-170">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7754-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="e7754-171">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e7754-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7754-172">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e7754-172">Next steps</span></span>

<span data-ttu-id="e7754-173">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="e7754-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e7754-174">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="e7754-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7754-175">Ceci est le premier didacticiel d’une série.</span><span class="sxs-lookup"><span data-stu-id="e7754-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="e7754-176">[Cette série](xref:tutorials/razor-pages/index) décrit les principes fondamentaux liés à la génération d’une application web de pages Razor dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7754-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="e7754-177">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="e7754-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="e7754-178">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="e7754-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e7754-179">Créer une application web Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e7754-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="e7754-180">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e7754-180">Run the app.</span></span>
> * <span data-ttu-id="e7754-181">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-181">Examine the project files.</span></span>

<span data-ttu-id="e7754-182">À la fin de ce didacticiel, vous disposez d’une application web Razor Pages fonctionnelle et générée dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="e7754-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="e7754-184">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e7754-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e7754-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7754-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e7754-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7754-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e7754-187">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e7754-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="e7754-188">Créer une application web Pages Razor</span><span class="sxs-lookup"><span data-stu-id="e7754-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e7754-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7754-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7754-190">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="e7754-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="e7754-191">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e7754-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="e7754-193">Nommez le projet **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e7754-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="e7754-194">Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="e7754-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="e7754-196">Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="e7754-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="e7754-198">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="e7754-198">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e7754-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7754-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e7754-201">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e7754-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="e7754-202">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="e7754-203">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7754-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="e7754-204">La commande `dotnet new` crée un nouveau projet Razor Pages dans le dossier *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="e7754-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="e7754-205">La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e7754-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="e7754-206">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="e7754-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="e7754-207">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="e7754-207">Select **Yes**.</span></span>

  <span data-ttu-id="e7754-208">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="e7754-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e7754-209">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e7754-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e7754-210">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="e7754-210">Select **File** > **New Solution**.</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="e7754-212">Sélectionnez **.NET Core** > **Application** > **Application web** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e7754-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="e7754-214">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , définissez **Framework cible** sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="e7754-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Sélection de .NET Core 3.0 pour macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="e7754-216">Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="e7754-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="e7754-218">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="e7754-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e7754-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7754-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7754-220">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="e7754-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="e7754-221">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="e7754-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="e7754-222">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="e7754-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e7754-223">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="e7754-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="e7754-224">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="e7754-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="e7754-225">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="e7754-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="e7754-226">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="e7754-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e7754-227">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="e7754-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="e7754-229">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="e7754-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="e7754-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7754-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="e7754-232">Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="e7754-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="e7754-233">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e7754-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="e7754-234">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="e7754-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e7754-235">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="e7754-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="e7754-236">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="e7754-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="e7754-237">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="e7754-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e7754-238">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="e7754-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="e7754-240">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="e7754-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e7754-242">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e7754-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="e7754-243">Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="e7754-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="e7754-244">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e7754-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="e7754-245">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="e7754-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e7754-246">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="e7754-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="e7754-248">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="e7754-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="e7754-250">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="e7754-250">Examine the project files</span></span>

<span data-ttu-id="e7754-251">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="e7754-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="e7754-252">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="e7754-252">Pages folder</span></span>

<span data-ttu-id="e7754-253">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="e7754-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="e7754-254">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e7754-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="e7754-255">Un fichier *. cshtml* qui contient le balisage HTML avec du Razor code C# à l’aide de la syntaxe.</span><span class="sxs-lookup"><span data-stu-id="e7754-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="e7754-256">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="e7754-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="e7754-257">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="e7754-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="e7754-258">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="e7754-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="e7754-259">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="e7754-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="e7754-260">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="e7754-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="e7754-261">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="e7754-261">wwwroot folder</span></span>

<span data-ttu-id="e7754-262">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="e7754-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="e7754-263">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="e7754-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="e7754-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="e7754-264">appSettings.json</span></span>

<span data-ttu-id="e7754-265">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="e7754-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="e7754-266">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e7754-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="e7754-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="e7754-267">Program.cs</span></span>

<span data-ttu-id="e7754-268">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="e7754-268">Contains the entry point for the program.</span></span> <span data-ttu-id="e7754-269">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="e7754-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="e7754-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="e7754-270">Startup.cs</span></span>

<span data-ttu-id="e7754-271">Contient le code qui configure le comportement de l’application, comme le fait qu’elle exige un consentement pour les cookies.</span><span class="sxs-lookup"><span data-stu-id="e7754-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="e7754-272">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e7754-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7754-273">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7754-273">Additional resources</span></span>

* [<span data-ttu-id="e7754-274">Version YouTube de ce didacticiel</span><span class="sxs-lookup"><span data-stu-id="e7754-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="e7754-275">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e7754-275">Next steps</span></span>

<span data-ttu-id="e7754-276">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="e7754-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e7754-277">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="e7754-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
