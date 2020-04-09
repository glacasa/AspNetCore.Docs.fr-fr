---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662476"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="2766c-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="2766c-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="2766c-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2766c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2766c-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="2766c-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2766c-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="2766c-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="2766c-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2766c-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2766c-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="2766c-108">Create a web app.</span></span>
> * <span data-ttu-id="2766c-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="2766c-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2766c-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="2766c-110">Work with a database.</span></span>
> * <span data-ttu-id="2766c-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="2766c-111">Add search and validation.</span></span>

<span data-ttu-id="2766c-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="2766c-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2766c-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="2766c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="2766c-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="2766c-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2766c-119">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="2766c-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2766c-120">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2766c-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2766c-122">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2766c-123">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="2766c-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="2766c-125">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2766c-126">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2766c-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="2766c-127">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="2766c-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2766c-128">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="2766c-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2766c-129">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="2766c-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2766c-131">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="2766c-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2766c-132">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="2766c-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2766c-133">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2766c-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2766c-134">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="2766c-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2766c-135">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2766c-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2766c-136">Une boîte de dialogue apparaît avec **les actifs nécessaires à construire et débocher sont absents de «MvcMovie». Les ajouter ?**</span><span class="sxs-lookup"><span data-stu-id="2766c-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2766c-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="2766c-137">Select **Yes**</span></span>

  * <span data-ttu-id="2766c-138">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="2766c-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2766c-139">`code -r MvcMovie`: Charge le dossier de projet *MvcMovie.csproj* dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2766c-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-140">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2766c-141">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="2766c-141">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2766c-143">Sélectionnez **.NET Core** > **Application** > **Application web (modèle-vue-contrôleur)** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2766c-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="2766c-145">Dans la **configuration de votre nouveau ASP.NET cœur du dialogue Web API,** définissez le **cadre cible** de **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="2766c-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 sélection](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="2766c-147">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2766c-148">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="2766c-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2766c-150">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="2766c-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2766c-151">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="2766c-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2766c-152">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-153">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2766c-154">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="2766c-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2766c-155">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="2766c-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2766c-156">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="2766c-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2766c-157">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="2766c-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2766c-159">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2766c-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="2766c-161">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="2766c-161">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2766c-164">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="2766c-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2766c-165">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2766c-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2766c-166">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-167">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2766c-168">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2766c-169">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="2766c-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2766c-170">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="2766c-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-172">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2766c-173">Sélectionnez **Run** > **Start Without Debugging** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="2766c-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2766c-174">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="2766c-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2766c-175">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-176">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2766c-177">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="2766c-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2766c-178">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="2766c-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2766c-179">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="2766c-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="2766c-180">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="2766c-180">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2766c-182">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="2766c-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2766c-183">Suivant</span><span class="sxs-lookup"><span data-stu-id="2766c-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2766c-184">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="2766c-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2766c-185">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="2766c-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="2766c-186">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2766c-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2766c-187">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="2766c-187">Create a web app.</span></span>
> * <span data-ttu-id="2766c-188">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="2766c-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2766c-189">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="2766c-189">Work with a database.</span></span>
> * <span data-ttu-id="2766c-190">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="2766c-190">Add search and validation.</span></span>

<span data-ttu-id="2766c-191">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="2766c-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2766c-192">Prérequis</span><span class="sxs-lookup"><span data-stu-id="2766c-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-195">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="2766c-196">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="2766c-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2766c-198">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="2766c-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2766c-199">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2766c-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2766c-201">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2766c-202">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="2766c-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="2766c-204">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2766c-205">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2766c-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="2766c-206">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="2766c-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2766c-207">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="2766c-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2766c-208">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="2766c-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2766c-210">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="2766c-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2766c-211">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="2766c-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2766c-212">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2766c-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2766c-213">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="2766c-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2766c-214">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2766c-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2766c-215">Une boîte de dialogue apparaît avec **les actifs nécessaires à construire et débocher sont absents de «MvcMovie». Les ajouter ?**</span><span class="sxs-lookup"><span data-stu-id="2766c-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2766c-216">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="2766c-216">Select **Yes**</span></span>

  * <span data-ttu-id="2766c-217">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="2766c-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2766c-218">`code -r MvcMovie`: Charge le dossier de projet *MvcMovie.csproj* dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2766c-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-219">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2766c-220">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="2766c-220">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2766c-222">Sélectionnez **.NET Core** > **Application** > **Application web (modèle-vue-contrôleur)** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2766c-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="2766c-224">Dans la **configuration de votre nouveau ASP.NET cœur du dialogue Web API,** acceptez le **cadre cible** par défaut de **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="2766c-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![sélection de .NET Core 2.2 pour macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="2766c-226">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="2766c-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2766c-227">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="2766c-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2766c-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2766c-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2766c-229">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="2766c-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2766c-230">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="2766c-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2766c-231">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-232">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2766c-233">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="2766c-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2766c-234">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="2766c-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2766c-235">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="2766c-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2766c-236">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="2766c-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2766c-238">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2766c-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="2766c-240">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="2766c-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2766c-241">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="2766c-241">This app doesn't track personal information.</span></span> <span data-ttu-id="2766c-242">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2766c-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="2766c-244">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="2766c-244">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2766c-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2766c-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2766c-247">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="2766c-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2766c-248">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2766c-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2766c-249">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-250">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2766c-251">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2766c-252">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="2766c-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2766c-253">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="2766c-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="2766c-254">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="2766c-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2766c-255">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="2766c-255">This app doesn't track personal information.</span></span> <span data-ttu-id="2766c-256">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2766c-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="2766c-258">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="2766c-258">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2766c-260">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2766c-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2766c-261">Sélectionnez **Run** > **Start Without Debugging** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="2766c-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2766c-262">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="2766c-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2766c-263">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2766c-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2766c-264">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="2766c-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2766c-265">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="2766c-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2766c-266">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="2766c-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2766c-267">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="2766c-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="2766c-268">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="2766c-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2766c-269">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="2766c-269">This app doesn't track personal information.</span></span> <span data-ttu-id="2766c-270">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2766c-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="2766c-272">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="2766c-272">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2766c-274">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="2766c-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2766c-275">Suivant</span><span class="sxs-lookup"><span data-stu-id="2766c-275">Next</span></span>](adding-controller.md)

::: moniker-end
