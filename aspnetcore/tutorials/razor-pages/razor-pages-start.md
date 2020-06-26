---
title: 'Didacticiel : prise en main des Razor pages dans ASP.net Core'
author: rick-anderson
description: Cette série de didacticiels montre comment utiliser Razor des pages dans ASP.net core. Découvrez comment créer un modèle, générer du code pour les Razor pages, utiliser des Entity Framework Core et des SQL Server pour l’accès aux données, ajouter des fonctionnalités de recherche, ajouter une validation d’entrée et utiliser des migrations pour mettre à jour le modèle.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405300"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="14b95-104">Didacticiel : prise en main des Razor pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="14b95-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="14b95-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14b95-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="14b95-106">Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="14b95-107">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="14b95-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="14b95-108">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="14b95-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14b95-109">Créer une Razor application Web pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="14b95-110">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="14b95-110">Run the app.</span></span>
> * <span data-ttu-id="14b95-111">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-111">Examine the project files.</span></span>

<span data-ttu-id="14b95-112">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="14b95-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="14b95-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="14b95-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14b95-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14b95-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="14b95-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14b95-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14b95-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="14b95-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="14b95-118">Créer une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="14b95-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14b95-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14b95-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14b95-120">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="14b95-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="14b95-121">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="14b95-122">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="14b95-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="14b95-123">Nommez le projet **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="14b95-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="14b95-124">Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="14b95-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="14b95-125">![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="14b95-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="14b95-126">Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="14b95-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="14b95-128">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="14b95-128">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="14b95-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14b95-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14b95-131">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="14b95-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="14b95-132">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="14b95-133">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="14b95-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="14b95-134">La `dotnet new` commande crée un Razor projet de pages dans le dossier *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="14b95-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="14b95-135">La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="14b95-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="14b95-136">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="14b95-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="14b95-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="14b95-137">Select **Yes**.</span></span>

  <span data-ttu-id="14b95-138">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14b95-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="14b95-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14b95-140">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="14b95-140">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="14b95-142">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="14b95-143">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application**console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="14b95-145">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="14b95-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="14b95-146">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="14b95-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="14b95-147">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="14b95-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="14b95-148">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-148">Select **Next**.</span></span>

  ![sélection de macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="14b95-150">Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="14b95-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="14b95-152">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="14b95-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="14b95-153">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="14b95-153">Examine the project files</span></span>

<span data-ttu-id="14b95-154">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="14b95-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="14b95-155">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="14b95-155">Pages folder</span></span>

<span data-ttu-id="14b95-156">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="14b95-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="14b95-157">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="14b95-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="14b95-158">Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="14b95-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="14b95-159">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="14b95-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="14b95-160">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="14b95-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="14b95-161">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="14b95-162">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="14b95-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="14b95-163">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="14b95-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="14b95-164">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="14b95-164">wwwroot folder</span></span>

<span data-ttu-id="14b95-165">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="14b95-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="14b95-166">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="14b95-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="14b95-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="14b95-167">appSettings.json</span></span>

<span data-ttu-id="14b95-168">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="14b95-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="14b95-169">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="14b95-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="14b95-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="14b95-170">Program.cs</span></span>

<span data-ttu-id="14b95-171">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="14b95-171">Contains the entry point for the program.</span></span> <span data-ttu-id="14b95-172">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="14b95-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="14b95-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="14b95-173">Startup.cs</span></span>

<span data-ttu-id="14b95-174">contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="14b95-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="14b95-175">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="14b95-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="14b95-176">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="14b95-176">Next steps</span></span>

<span data-ttu-id="14b95-177">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="14b95-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="14b95-178">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="14b95-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14b95-179">Ceci est le premier didacticiel d’une série.</span><span class="sxs-lookup"><span data-stu-id="14b95-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="14b95-180">[La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une Razor application Web ASP.net Core pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="14b95-181">À la fin de la série, vous disposez d’une application qui gère une base de données de films.</span><span class="sxs-lookup"><span data-stu-id="14b95-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="14b95-182">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="14b95-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14b95-183">Créer une Razor application Web pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="14b95-184">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="14b95-184">Run the app.</span></span>
> * <span data-ttu-id="14b95-185">Examiner les fichiers projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-185">Examine the project files.</span></span>

<span data-ttu-id="14b95-186">À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="14b95-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="14b95-188">Prérequis</span><span class="sxs-lookup"><span data-stu-id="14b95-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14b95-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14b95-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="14b95-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14b95-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14b95-191">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="14b95-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="14b95-192">Créer une Razor application Web pages</span><span class="sxs-lookup"><span data-stu-id="14b95-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14b95-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14b95-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14b95-194">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="14b95-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="14b95-195">Créez une application web ASP.NET Core, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="14b95-197">Nommez le projet **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="14b95-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="14b95-198">Il est important de nommer le projet *RazorPagesMovie* pour que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="14b95-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="14b95-200">Sélectionnez **ASP.NET Core 2.2** dans la liste déroulante, sélectionnez **Application web**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="14b95-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="14b95-202">Le projet de démarrage suivant est créé :</span><span class="sxs-lookup"><span data-stu-id="14b95-202">The following starter project is created:</span></span>

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="14b95-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14b95-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14b95-205">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="14b95-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="14b95-206">Accédez au répertoire (`cd`) qui contiendra le projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="14b95-207">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="14b95-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="14b95-208">La `dotnet new` commande crée un Razor projet de pages dans le dossier *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="14b95-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="14b95-209">La commande `code` ouvre le dossier *RazorPagesMovie* dans l’instance actuelle de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="14b95-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="14b95-210">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « RazorPagesMovie ». Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="14b95-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="14b95-211">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="14b95-211">Select **Yes**.</span></span>

  <span data-ttu-id="14b95-212">Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="14b95-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14b95-213">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="14b95-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14b95-214">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="14b95-214">Select **File** > **New Solution**.</span></span>

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="14b95-216">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="14b95-217">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application**console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="14b95-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="14b95-218">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , définissez **Framework cible** sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="14b95-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Sélection de .NET Core 3.0 pour macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="14b95-220">Nommez le projet **RazorPagesMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="14b95-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="14b95-222">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="14b95-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14b95-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14b95-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14b95-224">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="14b95-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="14b95-225">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="14b95-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="14b95-226">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="14b95-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="14b95-227">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="14b95-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="14b95-228">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="14b95-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="14b95-229">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="14b95-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="14b95-230">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="14b95-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="14b95-231">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="14b95-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="14b95-233">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="14b95-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="14b95-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14b95-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="14b95-236">Appuyez sur **Ctrl+F5** pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="14b95-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="14b95-237">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="14b95-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="14b95-238">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="14b95-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="14b95-239">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="14b95-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="14b95-240">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="14b95-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="14b95-241">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="14b95-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="14b95-242">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="14b95-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="14b95-244">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="14b95-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="14b95-246">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="14b95-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="14b95-247">Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="14b95-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="14b95-248">Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="14b95-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="14b95-249">Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="14b95-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="14b95-250">Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.</span><span class="sxs-lookup"><span data-stu-id="14b95-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="14b95-252">L’illustration suivante montre l’application après avoir consenti au suivi :</span><span class="sxs-lookup"><span data-stu-id="14b95-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Page d’accueil ou d’index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="14b95-254">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="14b95-254">Examine the project files</span></span>

<span data-ttu-id="14b95-255">Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.</span><span class="sxs-lookup"><span data-stu-id="14b95-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="14b95-256">Dossier Pages</span><span class="sxs-lookup"><span data-stu-id="14b95-256">Pages folder</span></span>

<span data-ttu-id="14b95-257">Contient Razor des pages et des fichiers de prise en charge.</span><span class="sxs-lookup"><span data-stu-id="14b95-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="14b95-258">Chaque Razor page est une paire de fichiers :</span><span class="sxs-lookup"><span data-stu-id="14b95-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="14b95-259">Un fichier *. cshtml* qui contient le balisage HTML avec du code C# à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="14b95-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="14b95-260">Un fichier *. cshtml.cs* qui contient du code C# gérant les événements de page.</span><span class="sxs-lookup"><span data-stu-id="14b95-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="14b95-261">Les fichiers de prise en charge ont des noms commençant par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="14b95-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="14b95-262">Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages.</span><span class="sxs-lookup"><span data-stu-id="14b95-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="14b95-263">Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page.</span><span class="sxs-lookup"><span data-stu-id="14b95-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="14b95-264">Pour plus d’informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="14b95-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="14b95-265">Dossier racine</span><span class="sxs-lookup"><span data-stu-id="14b95-265">wwwroot folder</span></span>

<span data-ttu-id="14b95-266">Contient des fichiers statiques, tels que les fichiers HTML, JavaScript et CSS.</span><span class="sxs-lookup"><span data-stu-id="14b95-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="14b95-267">Pour plus d’informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="14b95-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="14b95-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="14b95-268">appSettings.json</span></span>

<span data-ttu-id="14b95-269">Contient les données de configuration, comme les chaînes de connexion.</span><span class="sxs-lookup"><span data-stu-id="14b95-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="14b95-270">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="14b95-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="14b95-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="14b95-271">Program.cs</span></span>

<span data-ttu-id="14b95-272">Contient le point d’entrée pour le programme.</span><span class="sxs-lookup"><span data-stu-id="14b95-272">Contains the entry point for the program.</span></span> <span data-ttu-id="14b95-273">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="14b95-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="14b95-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="14b95-274">Startup.cs</span></span>

<span data-ttu-id="14b95-275">Contient le code qui configure le comportement de l’application, comme le fait qu’elle exige un consentement pour les cookies.</span><span class="sxs-lookup"><span data-stu-id="14b95-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="14b95-276">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="14b95-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14b95-277">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="14b95-277">Additional resources</span></span>

* [<span data-ttu-id="14b95-278">Version YouTube de ce didacticiel</span><span class="sxs-lookup"><span data-stu-id="14b95-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="14b95-279">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="14b95-279">Next steps</span></span>

<span data-ttu-id="14b95-280">Passez au tutoriel suivant dans la série :</span><span class="sxs-lookup"><span data-stu-id="14b95-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="14b95-281">Ajouter un modèle</span><span class="sxs-lookup"><span data-stu-id="14b95-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
