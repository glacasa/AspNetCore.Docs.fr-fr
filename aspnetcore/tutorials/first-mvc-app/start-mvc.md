---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 0b28e99d9c56d92e157e9010880e58ec5165196a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774983"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f5664-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f5664-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f5664-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5664-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f5664-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f5664-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f5664-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="f5664-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="f5664-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f5664-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f5664-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="f5664-108">Create a web app.</span></span>
> * <span data-ttu-id="f5664-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="f5664-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f5664-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="f5664-110">Work with a database.</span></span>
> * <span data-ttu-id="f5664-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="f5664-111">Add search and validation.</span></span>

<span data-ttu-id="f5664-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="f5664-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f5664-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f5664-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f5664-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="f5664-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f5664-119">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="f5664-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f5664-120">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f5664-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f5664-122">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f5664-123">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="f5664-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="f5664-125">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f5664-126">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5664-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f5664-127">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="f5664-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f5664-128">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="f5664-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f5664-129">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="f5664-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f5664-131">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="f5664-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f5664-132">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f5664-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f5664-133">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f5664-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f5664-134">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="f5664-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f5664-135">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f5664-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f5664-136">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="f5664-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f5664-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f5664-137">Select **Yes**</span></span>

  * <span data-ttu-id="f5664-138">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="f5664-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f5664-139">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="f5664-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-140">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f5664-141">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f5664-141">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f5664-143">Sélectionnez **.NET Core** > **Application** > **Application web (modèle-vue-contrôleur)** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f5664-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f5664-145">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , définissez le **Framework cible** de **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="f5664-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![sélection de macOS .NET Core 3,1](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="f5664-147">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f5664-148">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f5664-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f5664-150">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="f5664-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f5664-151">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="f5664-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f5664-152">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-153">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f5664-154">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f5664-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f5664-155">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f5664-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f5664-156">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="f5664-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f5664-157">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="f5664-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f5664-159">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f5664-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="f5664-161">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="f5664-161">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f5664-164">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f5664-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f5664-165">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f5664-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f5664-166">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-167">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f5664-168">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f5664-169">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f5664-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f5664-170">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="f5664-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-172">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f5664-173">Sélectionnez **exécuter** > **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f5664-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f5664-174">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f5664-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f5664-175">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-176">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f5664-177">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f5664-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f5664-178">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="f5664-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f5664-179">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="f5664-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="f5664-180">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="f5664-180">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f5664-182">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="f5664-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f5664-183">Suivant</span><span class="sxs-lookup"><span data-stu-id="f5664-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f5664-184">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f5664-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f5664-185">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="f5664-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="f5664-186">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f5664-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f5664-187">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="f5664-187">Create a web app.</span></span>
> * <span data-ttu-id="f5664-188">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="f5664-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f5664-189">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="f5664-189">Work with a database.</span></span>
> * <span data-ttu-id="f5664-190">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="f5664-190">Add search and validation.</span></span>

<span data-ttu-id="f5664-191">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="f5664-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f5664-192">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f5664-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-195">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="f5664-196">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="f5664-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f5664-198">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="f5664-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f5664-199">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f5664-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f5664-201">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f5664-202">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="f5664-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="f5664-204">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f5664-205">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5664-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f5664-206">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="f5664-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f5664-207">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="f5664-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f5664-208">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="f5664-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f5664-210">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="f5664-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f5664-211">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f5664-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f5664-212">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f5664-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f5664-213">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="f5664-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f5664-214">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f5664-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f5664-215">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="f5664-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f5664-216">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f5664-216">Select **Yes**</span></span>

  * <span data-ttu-id="f5664-217">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="f5664-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f5664-218">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="f5664-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-219">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f5664-220">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f5664-220">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f5664-222">Sélectionnez **.NET Core** > **Application** > **Application web (modèle-vue-contrôleur)** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f5664-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f5664-224">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , acceptez le **Framework cible** par défaut de **.net Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="f5664-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![sélection de .NET Core 2.2 pour macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="f5664-226">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f5664-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f5664-227">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f5664-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f5664-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f5664-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f5664-229">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="f5664-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f5664-230">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="f5664-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f5664-231">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-232">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f5664-233">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f5664-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f5664-234">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f5664-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f5664-235">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="f5664-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f5664-236">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="f5664-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f5664-238">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f5664-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="f5664-240">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="f5664-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f5664-241">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f5664-241">This app doesn't track personal information.</span></span> <span data-ttu-id="f5664-242">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f5664-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="f5664-244">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f5664-244">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f5664-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f5664-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f5664-247">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f5664-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f5664-248">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f5664-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f5664-249">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-250">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f5664-251">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f5664-252">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f5664-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f5664-253">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="f5664-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="f5664-254">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="f5664-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f5664-255">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f5664-255">This app doesn't track personal information.</span></span> <span data-ttu-id="f5664-256">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f5664-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="f5664-258">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f5664-258">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f5664-260">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f5664-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f5664-261">Sélectionnez **exécuter** > **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f5664-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f5664-262">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f5664-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f5664-263">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f5664-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f5664-264">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f5664-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f5664-265">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f5664-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f5664-266">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="f5664-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f5664-267">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="f5664-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="f5664-268">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="f5664-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f5664-269">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f5664-269">This app doesn't track personal information.</span></span> <span data-ttu-id="f5664-270">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f5664-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="f5664-272">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f5664-272">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f5664-274">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="f5664-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f5664-275">Suivant</span><span class="sxs-lookup"><span data-stu-id="f5664-275">Next</span></span>](adding-controller.md)

::: moniker-end
