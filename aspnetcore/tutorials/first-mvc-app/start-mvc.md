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
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452278"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d88a7-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d88a7-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d88a7-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d88a7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d88a7-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d88a7-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d88a7-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="d88a7-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="d88a7-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="d88a7-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d88a7-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-108">Create a web app.</span></span>
> * <span data-ttu-id="d88a7-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="d88a7-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d88a7-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="d88a7-110">Work with a database.</span></span>
> * <span data-ttu-id="d88a7-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="d88a7-111">Add search and validation.</span></span>

<span data-ttu-id="d88a7-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="d88a7-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d88a7-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d88a7-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d88a7-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="d88a7-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d88a7-119">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d88a7-120">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d88a7-122">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d88a7-123">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="d88a7-125">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d88a7-126">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d88a7-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d88a7-127">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="d88a7-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d88a7-128">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="d88a7-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d88a7-129">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="d88a7-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d88a7-131">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d88a7-132">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="d88a7-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d88a7-133">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d88a7-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d88a7-134">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="d88a7-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d88a7-135">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d88a7-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d88a7-136">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="d88a7-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d88a7-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-137">Select **Yes**</span></span>

  * <span data-ttu-id="d88a7-138">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d88a7-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d88a7-139">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-140">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d88a7-141">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-141">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d88a7-143">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d88a7-144">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d88a7-146">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d88a7-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="d88a7-147">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="d88a7-148">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="d88a7-149">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-149">Select **Next**.</span></span>

  ![sélection de macOS .NET Core 3,1](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="d88a7-151">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d88a7-153">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="d88a7-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d88a7-155">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="d88a7-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d88a7-156">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="d88a7-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d88a7-157">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-158">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d88a7-159">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d88a7-160">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d88a7-161">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="d88a7-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d88a7-162">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="d88a7-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d88a7-164">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d88a7-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d88a7-166">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="d88a7-166">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d88a7-169">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="d88a7-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d88a7-170">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d88a7-171">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-172">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d88a7-173">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d88a7-174">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d88a7-175">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="d88a7-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-177">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d88a7-178">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="d88a7-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d88a7-179">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="d88a7-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d88a7-180">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-181">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d88a7-182">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d88a7-183">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="d88a7-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d88a7-184">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d88a7-185">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="d88a7-185">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d88a7-187">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d88a7-188">Suivant</span><span class="sxs-lookup"><span data-stu-id="d88a7-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d88a7-189">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d88a7-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d88a7-190">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="d88a7-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="d88a7-191">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="d88a7-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d88a7-192">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-192">Create a web app.</span></span>
> * <span data-ttu-id="d88a7-193">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="d88a7-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d88a7-194">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="d88a7-194">Work with a database.</span></span>
> * <span data-ttu-id="d88a7-195">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="d88a7-195">Add search and validation.</span></span>

<span data-ttu-id="d88a7-196">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="d88a7-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d88a7-197">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d88a7-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-200">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="d88a7-201">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="d88a7-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d88a7-203">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d88a7-204">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d88a7-206">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d88a7-207">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="d88a7-209">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d88a7-210">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d88a7-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="d88a7-211">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="d88a7-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d88a7-212">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="d88a7-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d88a7-213">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="d88a7-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d88a7-215">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d88a7-216">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="d88a7-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d88a7-217">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d88a7-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d88a7-218">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="d88a7-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d88a7-219">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d88a7-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d88a7-220">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="d88a7-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d88a7-221">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-221">Select **Yes**</span></span>

  * <span data-ttu-id="d88a7-222">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d88a7-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d88a7-223">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-224">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d88a7-225">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-225">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d88a7-227">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d88a7-228">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="d88a7-229">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , acceptez le **Framework cible** par défaut de **.net Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![sélection de .NET Core 2.2 pour macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="d88a7-231">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="d88a7-232">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="d88a7-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d88a7-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d88a7-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d88a7-234">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="d88a7-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d88a7-235">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="d88a7-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d88a7-236">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-237">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d88a7-238">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d88a7-239">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d88a7-240">De nombreux développeurs préfèrent utiliser le mode non-débogage pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="d88a7-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d88a7-241">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="d88a7-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d88a7-243">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d88a7-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="d88a7-245">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="d88a7-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d88a7-246">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="d88a7-246">This app doesn't track personal information.</span></span> <span data-ttu-id="d88a7-247">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d88a7-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="d88a7-249">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="d88a7-249">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d88a7-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d88a7-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d88a7-252">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="d88a7-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d88a7-253">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d88a7-254">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-255">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d88a7-256">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d88a7-257">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d88a7-258">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="d88a7-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="d88a7-259">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="d88a7-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d88a7-260">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="d88a7-260">This app doesn't track personal information.</span></span> <span data-ttu-id="d88a7-261">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d88a7-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="d88a7-263">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="d88a7-263">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d88a7-265">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d88a7-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d88a7-266">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="d88a7-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d88a7-267">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="d88a7-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d88a7-268">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d88a7-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d88a7-269">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="d88a7-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d88a7-270">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="d88a7-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d88a7-271">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="d88a7-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d88a7-272">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="d88a7-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="d88a7-273">Sélectionnez **Accepter** pour accepter le suivi.</span><span class="sxs-lookup"><span data-stu-id="d88a7-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d88a7-274">Cette application n’effectue pas le suivi d’informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="d88a7-274">This app doesn't track personal information.</span></span> <span data-ttu-id="d88a7-275">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d88a7-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="d88a7-277">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="d88a7-277">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d88a7-279">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="d88a7-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d88a7-280">Suivant</span><span class="sxs-lookup"><span data-stu-id="d88a7-280">Next</span></span>](adding-controller.md)

::: moniker-end
