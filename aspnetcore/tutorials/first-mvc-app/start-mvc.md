---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213083"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f8bf1-103">Bien démarrer avec ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f8bf1-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f8bf1-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8bf1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f8bf1-105">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f8bf1-106">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="f8bf1-107">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8bf1-108">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-108">Create a web app.</span></span>
> * <span data-ttu-id="f8bf1-109">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f8bf1-110">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-110">Work with a database.</span></span>
> * <span data-ttu-id="f8bf1-111">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-111">Add search and validation.</span></span>

<span data-ttu-id="f8bf1-112">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f8bf1-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f8bf1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f8bf1-117">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="f8bf1-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8bf1-119">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f8bf1-120">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f8bf1-122">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f8bf1-123">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="f8bf1-125">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f8bf1-126">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8bf1-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f8bf1-127">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f8bf1-128">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f8bf1-129">Il s’agit d’un projet de démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8bf1-131">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f8bf1-132">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f8bf1-133">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f8bf1-134">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f8bf1-135">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f8bf1-136">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="f8bf1-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f8bf1-137">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-137">Select **Yes**</span></span>

  * <span data-ttu-id="f8bf1-138">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f8bf1-139">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-140">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f8bf1-141">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-141">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f8bf1-143">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f8bf1-144">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f8bf1-146">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f8bf1-147">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f8bf1-148">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 3. x.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="f8bf1-149">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-149">Select **Next**.</span></span>

* <span data-ttu-id="f8bf1-150">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f8bf1-152">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f8bf1-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8bf1-154">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f8bf1-155">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f8bf1-156">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-157">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f8bf1-158">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f8bf1-159">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f8bf1-160">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f8bf1-161">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f8bf1-163">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f8bf1-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="f8bf1-165">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-165">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8bf1-168">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f8bf1-169">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f8bf1-170">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-171">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f8bf1-172">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f8bf1-173">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f8bf1-174">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-176">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8bf1-177">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f8bf1-178">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f8bf1-179">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-180">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f8bf1-181">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f8bf1-182">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f8bf1-183">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="f8bf1-184">L’image suivante montre l’application :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-184">The following image shows the app:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f8bf1-186">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f8bf1-187">Suivant</span><span class="sxs-lookup"><span data-stu-id="f8bf1-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f8bf1-188">Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f8bf1-189">L’application gère une base de données de titres de films.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="f8bf1-190">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8bf1-191">Créez une application web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-191">Create a web app.</span></span>
> * <span data-ttu-id="f8bf1-192">Ajouter et structurer un modèle.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f8bf1-193">Utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-193">Work with a database.</span></span>
> * <span data-ttu-id="f8bf1-194">Ajouter une fonctionnalité de recherche et de validation.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-194">Add search and validation.</span></span>

<span data-ttu-id="f8bf1-195">À la fin, vous obtenez une application qui peut gérer et afficher des données de films.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f8bf1-196">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f8bf1-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-199">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="f8bf1-200">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="f8bf1-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8bf1-202">Dans Visual Studio, sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f8bf1-203">Sélectionnez **Application web ASP.NET Core**, puis **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f8bf1-205">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f8bf1-206">Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="f8bf1-208">Sélectionnez **Application web (modèle-vue-contrôleur)**, puis **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f8bf1-209">Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8bf1-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f8bf1-210">Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f8bf1-211">Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f8bf1-212">Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8bf1-214">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f8bf1-215">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f8bf1-216">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f8bf1-217">Accédez à un répertoire (`cd`) destiné à contenir le projet.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f8bf1-218">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f8bf1-219">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="f8bf1-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f8bf1-220">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-220">Select **Yes**</span></span>

  * <span data-ttu-id="f8bf1-221">`dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f8bf1-222">`code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-223">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f8bf1-224">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-224">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f8bf1-226">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f8bf1-227">Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="f8bf1-228">Dans la boîte de dialogue **configurer votre nouvelle application Web** :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f8bf1-229">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f8bf1-230">Si vous avez présenté une option permettant de sélectionner un **Framework cible**, sélectionnez la version la plus récente de 2. x.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="f8bf1-231">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-231">Select **Next**.</span></span>

* <span data-ttu-id="f8bf1-232">Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f8bf1-233">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f8bf1-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8bf1-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8bf1-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8bf1-235">Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f8bf1-236">Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f8bf1-237">Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-238">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f8bf1-239">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f8bf1-240">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f8bf1-241">Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f8bf1-242">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f8bf1-244">Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f8bf1-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="f8bf1-246">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f8bf1-247">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-247">This app doesn't track personal information.</span></span> <span data-ttu-id="f8bf1-248">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="f8bf1-250">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-250">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8bf1-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8bf1-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8bf1-253">Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f8bf1-254">Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f8bf1-255">La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-256">La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f8bf1-257">Localhost traite uniquement les requêtes web de l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f8bf1-258">Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f8bf1-259">De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="f8bf1-260">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f8bf1-261">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-261">This app doesn't track personal information.</span></span> <span data-ttu-id="f8bf1-262">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  <span data-ttu-id="f8bf1-264">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-264">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8bf1-266">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f8bf1-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8bf1-267">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f8bf1-268">Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f8bf1-269">La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8bf1-270">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f8bf1-271">Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f8bf1-272">Quand vous exécutez l’application, vous voyez un autre numéro de port.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f8bf1-273">Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="f8bf1-274">Sélectionnez **Accepter** pour consentir au suivi.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f8bf1-275">Cette application ne suit pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-275">This app doesn't track personal information.</span></span> <span data-ttu-id="f8bf1-276">Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8bf1-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="f8bf1-278">L’illustration suivante montre l’application une fois le suivi accepté :</span><span class="sxs-lookup"><span data-stu-id="f8bf1-278">The following image shows the app after accepting tracking:</span></span>

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f8bf1-280">Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="f8bf1-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f8bf1-281">Suivant</span><span class="sxs-lookup"><span data-stu-id="f8bf1-281">Next</span></span>](adding-controller.md)

::: moniker-end
