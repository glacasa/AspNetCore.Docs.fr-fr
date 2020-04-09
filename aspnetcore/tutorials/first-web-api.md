---
title: 'Tutorial: Créer une API web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417660"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="cbcdd-103">Tutorial: Créer une API web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cbcdd-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="cbcdd-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), et Mike [Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="cbcdd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="cbcdd-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbcdd-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cbcdd-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-107">Create a web API project.</span></span>
> * <span data-ttu-id="cbcdd-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="cbcdd-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="cbcdd-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="cbcdd-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="cbcdd-111">Call the web API with Postman.</span></span>

<span data-ttu-id="cbcdd-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="cbcdd-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="cbcdd-113">Overview</span></span>

<span data-ttu-id="cbcdd-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="cbcdd-115">API</span><span class="sxs-lookup"><span data-stu-id="cbcdd-115">API</span></span> | <span data-ttu-id="cbcdd-116">Description</span><span class="sxs-lookup"><span data-stu-id="cbcdd-116">Description</span></span> | <span data-ttu-id="cbcdd-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="cbcdd-117">Request body</span></span> | <span data-ttu-id="cbcdd-118">Response body</span><span class="sxs-lookup"><span data-stu-id="cbcdd-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="cbcdd-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="cbcdd-119">GET /api/TodoItems</span></span> | <span data-ttu-id="cbcdd-120">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="cbcdd-120">Get all to-do items</span></span> | <span data-ttu-id="cbcdd-121">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-121">None</span></span> | <span data-ttu-id="cbcdd-122">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="cbcdd-122">Array of to-do items</span></span>|
|<span data-ttu-id="cbcdd-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="cbcdd-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="cbcdd-124">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="cbcdd-124">Get an item by ID</span></span> | <span data-ttu-id="cbcdd-125">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-125">None</span></span> | <span data-ttu-id="cbcdd-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-126">To-do item</span></span>|
|<span data-ttu-id="cbcdd-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="cbcdd-127">POST /api/TodoItems</span></span> | <span data-ttu-id="cbcdd-128">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="cbcdd-128">Add a new item</span></span> | <span data-ttu-id="cbcdd-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-129">To-do item</span></span> | <span data-ttu-id="cbcdd-130">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-130">To-do item</span></span> |
|<span data-ttu-id="cbcdd-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="cbcdd-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="cbcdd-132">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="cbcdd-133">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-133">To-do item</span></span> | <span data-ttu-id="cbcdd-134">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-134">None</span></span> |
|<span data-ttu-id="cbcdd-135">DELETE /api/TodoItems/id &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="cbcdd-136">Supprimer un &nbsp; élément&nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="cbcdd-137">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-137">None</span></span> | <span data-ttu-id="cbcdd-138">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-138">None</span></span>|

<span data-ttu-id="cbcdd-139">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-139">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="cbcdd-145">Prérequis</span><span class="sxs-lookup"><span data-stu-id="cbcdd-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-148">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="cbcdd-149">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="cbcdd-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-151">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cbcdd-152">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="cbcdd-153">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="cbcdd-154">Dans le **Create a new ASP.NET Core Web Application** dialogue, confirmer que **.NET Core** et ASP.NET Core **3.1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="cbcdd-155">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-155">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cbcdd-158">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cbcdd-159">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="cbcdd-160">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="cbcdd-161">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="cbcdd-162">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-162">The preceding commands:</span></span>

  * <span data-ttu-id="cbcdd-163">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="cbcdd-164">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-165">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cbcdd-166">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-166">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="cbcdd-168">Sélectionnez **.NET Core** > **Application** > **API** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="cbcdd-170">Dans la **configuration de votre nouveau ASP.NET cœur du dialogue Web API,** sélectionnez **Target Framework** de *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="cbcdd-171">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="cbcdd-173">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="cbcdd-174">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="cbcdd-174">Test the API</span></span>

<span data-ttu-id="cbcdd-175">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="cbcdd-176">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cbcdd-178">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="cbcdd-179">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="cbcdd-180">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="cbcdd-181">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cbcdd-183">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="cbcdd-184">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-185">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cbcdd-186">Sélectionnez **Run** > **Start Debugging** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="cbcdd-187">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="cbcdd-188">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="cbcdd-189">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="cbcdd-190">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="cbcdd-191">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="cbcdd-191">Add a model class</span></span>

<span data-ttu-id="cbcdd-192">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="cbcdd-193">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-195">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="cbcdd-196">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="cbcdd-197">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="cbcdd-198">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="cbcdd-199">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="cbcdd-200">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cbcdd-202">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="cbcdd-203">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-204">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cbcdd-205">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-205">Right-click the project.</span></span> <span data-ttu-id="cbcdd-206">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="cbcdd-207">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-207">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="cbcdd-209">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="cbcdd-210">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="cbcdd-211">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="cbcdd-212">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="cbcdd-213">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="cbcdd-214">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="cbcdd-214">Add a database context</span></span>

<span data-ttu-id="cbcdd-215">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="cbcdd-216">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="cbcdd-218">Ajouter Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="cbcdd-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="cbcdd-219">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="cbcdd-220">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="cbcdd-221">Sélectionnez **Microsoft.EntityFrameworkCore.SqlServer** dans la vitre gauche.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="cbcdd-222">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="cbcdd-223">Utilisez les instructions précédentes pour ajouter le package NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="cbcdd-225">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="cbcdd-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="cbcdd-226">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="cbcdd-227">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-228">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="cbcdd-229">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="cbcdd-230">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="cbcdd-231">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="cbcdd-231">Register the database context</span></span>

<span data-ttu-id="cbcdd-232">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cbcdd-233">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="cbcdd-234">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="cbcdd-235">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-235">The preceding code:</span></span>

* <span data-ttu-id="cbcdd-236">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="cbcdd-237">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="cbcdd-238">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="cbcdd-239">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="cbcdd-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-241">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="cbcdd-242">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="cbcdd-243">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="cbcdd-244">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="cbcdd-245">Sélectionnez **TodoItem (TodoApi.Models)** dans la **classe Modèle**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="cbcdd-246">Sélectionnez **TodoContext (TodoApi.Models)** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="cbcdd-247">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-248">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cbcdd-249">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="cbcdd-250">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-250">The preceding commands:</span></span>

* <span data-ttu-id="cbcdd-251">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="cbcdd-252">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="cbcdd-253">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="cbcdd-254">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-254">The generated code:</span></span>

* <span data-ttu-id="cbcdd-255">Marque la classe [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="cbcdd-256">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="cbcdd-257">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="cbcdd-258">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="cbcdd-259">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="cbcdd-260">Les modèles ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="cbcdd-261">Les contrôleurs `[action]` avec vue incluent dans le modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="cbcdd-262">Les contrôleurs API `[action]` n’incluent pas dans le modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="cbcdd-263">Lorsque `[action]` le jeton n’est pas dans le modèle d’itinéraire, le nom [d’action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="cbcdd-264">Autrement dit, le nom de méthode associé de l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="cbcdd-265">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="cbcdd-266">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="cbcdd-267">Le code précédent est une méthode HTTP [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) POST, comme indiqué par l’attribut.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="cbcdd-268">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="cbcdd-269">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="cbcdd-270">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="cbcdd-271">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="cbcdd-272">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="cbcdd-273">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="cbcdd-274">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="cbcdd-275">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="cbcdd-276">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="cbcdd-277">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="cbcdd-277">Install Postman</span></span>

<span data-ttu-id="cbcdd-278">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="cbcdd-279">Installer [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="cbcdd-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="cbcdd-280">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-280">Start the web app.</span></span>
* <span data-ttu-id="cbcdd-281">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-281">Start Postman.</span></span>
* <span data-ttu-id="cbcdd-282">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="cbcdd-283">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="cbcdd-284">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="cbcdd-285">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="cbcdd-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="cbcdd-286">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-286">Create a new request.</span></span>
* <span data-ttu-id="cbcdd-287">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="cbcdd-288">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="cbcdd-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="cbcdd-289">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="cbcdd-290">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="cbcdd-291">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="cbcdd-292">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-292">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="cbcdd-294">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="cbcdd-294">Test the location header URI</span></span>

* <span data-ttu-id="cbcdd-295">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="cbcdd-296">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-296">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="cbcdd-298">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-298">Set the method to GET.</span></span>
* <span data-ttu-id="cbcdd-299">Coller l’URI (par `https://localhost:5001/api/TodoItems/1`exemple, ).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="cbcdd-300">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="cbcdd-301">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="cbcdd-301">Examine the GET methods</span></span>

<span data-ttu-id="cbcdd-302">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="cbcdd-303">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="cbcdd-304">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="cbcdd-305">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="cbcdd-306">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="cbcdd-306">Test Get with Postman</span></span>

* <span data-ttu-id="cbcdd-307">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-307">Create a new request.</span></span>
* <span data-ttu-id="cbcdd-308">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="cbcdd-309">Définissez l’URL de la requête sur `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="cbcdd-310">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="cbcdd-311">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="cbcdd-312">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-312">Select **Send**.</span></span>

<span data-ttu-id="cbcdd-313">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-313">This app uses an in-memory database.</span></span> <span data-ttu-id="cbcdd-314">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="cbcdd-315">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="cbcdd-316">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="cbcdd-316">Routing and URL paths</span></span>

<span data-ttu-id="cbcdd-317">L’attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) désigne une méthode qui répond à une demande HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="cbcdd-318">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="cbcdd-319">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="cbcdd-320">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="cbcdd-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="cbcdd-321">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems**Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="cbcdd-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="cbcdd-322">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="cbcdd-323">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="cbcdd-324">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-324">This sample doesn't use a template.</span></span> <span data-ttu-id="cbcdd-325">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="cbcdd-326">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="cbcdd-327">Lorsqu’elle `GetTodoItem` est invoquée, la valeur de l’URL `"{id}"` est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="cbcdd-328">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="cbcdd-328">Return values</span></span>

<span data-ttu-id="cbcdd-329">Le type de retour des méthodes `GetTodoItems` et `GetTodoItem` est [type ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="cbcdd-330">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="cbcdd-331">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="cbcdd-332">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="cbcdd-333">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="cbcdd-334">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="cbcdd-335">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="cbcdd-336">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="cbcdd-337">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="cbcdd-338">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-338">The PutTodoItem method</span></span>

<span data-ttu-id="cbcdd-339">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="cbcdd-340">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="cbcdd-341">La réponse est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="cbcdd-342">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="cbcdd-343">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="cbcdd-344">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="cbcdd-345">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="cbcdd-346">Cet exemple utilise une base de données en mémoire qui doit être parascisée chaque fois que l’application est lancée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="cbcdd-347">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="cbcdd-348">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="cbcdd-349">Mettez à jour la tâche dont l’ID = 1 et nommez-la « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="cbcdd-350">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-350">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="cbcdd-352">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="cbcdd-353">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="cbcdd-354">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="cbcdd-355">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="cbcdd-356">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="cbcdd-357">Définir l’URI de l’objet `https://localhost:5001/api/TodoItems/1`à supprimer (par exemple ).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="cbcdd-358">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="cbcdd-359">Prévenir la sur-affichage</span><span class="sxs-lookup"><span data-stu-id="cbcdd-359">Prevent over-posting</span></span>

<span data-ttu-id="cbcdd-360">Actuellement, l’application d’échantillon expose l’objet entier. `TodoItem`</span><span class="sxs-lookup"><span data-stu-id="cbcdd-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="cbcdd-361">Les applications Productions limitent généralement les données qui sont saisies et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="cbcdd-362">Il y a plusieurs raisons derrière cela et la sécurité est majeure.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="cbcdd-363">Le sous-ensemble d’un modèle est généralement appelé objet de transfert de données (DTO), modèle d’entrée ou modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="cbcdd-364">**DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="cbcdd-365">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-365">A DTO may be used to:</span></span>

* <span data-ttu-id="cbcdd-366">Empêcher la sur-affichage.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-366">Prevent over-posting.</span></span>
* <span data-ttu-id="cbcdd-367">Masquer les propriétés que les clients ne sont pas censés voir.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="cbcdd-368">Omettre certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="cbcdd-369">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="cbcdd-370">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="cbcdd-371">Pour démontrer l’approche DTO, mettre à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="cbcdd-372">Le champ secret doit être caché de cette application, mais une application administrative pourrait choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="cbcdd-373">Vérifiez que vous pouvez poster et obtenir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="cbcdd-374">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="cbcdd-375">Mettre `TodoItemsController` à `TodoItemDTO`jour le à utiliser :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="cbcdd-376">Vérifiez que vous ne pouvez pas poster ou obtenir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="cbcdd-377">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="cbcdd-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="cbcdd-378">Voir [Tutorial: Appelez une API web ASP.NET Core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cbcdd-379">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cbcdd-380">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-380">Create a web API project.</span></span>
> * <span data-ttu-id="cbcdd-381">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="cbcdd-382">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="cbcdd-382">Add a controller.</span></span>
> * <span data-ttu-id="cbcdd-383">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="cbcdd-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="cbcdd-384">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="cbcdd-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="cbcdd-385">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="cbcdd-385">Specify return values.</span></span>
> * <span data-ttu-id="cbcdd-386">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="cbcdd-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="cbcdd-387">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="cbcdd-388">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="cbcdd-389">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="cbcdd-389">Overview</span></span>

<span data-ttu-id="cbcdd-390">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="cbcdd-391">API</span><span class="sxs-lookup"><span data-stu-id="cbcdd-391">API</span></span> | <span data-ttu-id="cbcdd-392">Description</span><span class="sxs-lookup"><span data-stu-id="cbcdd-392">Description</span></span> | <span data-ttu-id="cbcdd-393">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="cbcdd-393">Request body</span></span> | <span data-ttu-id="cbcdd-394">Response body</span><span class="sxs-lookup"><span data-stu-id="cbcdd-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="cbcdd-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="cbcdd-395">GET /api/TodoItems</span></span> | <span data-ttu-id="cbcdd-396">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="cbcdd-396">Get all to-do items</span></span> | <span data-ttu-id="cbcdd-397">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-397">None</span></span> | <span data-ttu-id="cbcdd-398">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="cbcdd-398">Array of to-do items</span></span>|
|<span data-ttu-id="cbcdd-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="cbcdd-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="cbcdd-400">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="cbcdd-400">Get an item by ID</span></span> | <span data-ttu-id="cbcdd-401">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-401">None</span></span> | <span data-ttu-id="cbcdd-402">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-402">To-do item</span></span>|
|<span data-ttu-id="cbcdd-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="cbcdd-403">POST /api/TodoItems</span></span> | <span data-ttu-id="cbcdd-404">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="cbcdd-404">Add a new item</span></span> | <span data-ttu-id="cbcdd-405">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-405">To-do item</span></span> | <span data-ttu-id="cbcdd-406">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-406">To-do item</span></span> |
|<span data-ttu-id="cbcdd-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="cbcdd-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="cbcdd-408">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="cbcdd-409">Tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-409">To-do item</span></span> | <span data-ttu-id="cbcdd-410">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-410">None</span></span> |
|<span data-ttu-id="cbcdd-411">DELETE /api/TodoItems/id &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="cbcdd-412">Supprimer un &nbsp; élément&nbsp;</span><span class="sxs-lookup"><span data-stu-id="cbcdd-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="cbcdd-413">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-413">None</span></span> | <span data-ttu-id="cbcdd-414">None</span><span class="sxs-lookup"><span data-stu-id="cbcdd-414">None</span></span>|

<span data-ttu-id="cbcdd-415">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-415">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="cbcdd-421">Prérequis</span><span class="sxs-lookup"><span data-stu-id="cbcdd-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-424">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="cbcdd-425">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="cbcdd-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-427">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cbcdd-428">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="cbcdd-429">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="cbcdd-430">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="cbcdd-431">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="cbcdd-432">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-432">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cbcdd-435">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cbcdd-436">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="cbcdd-437">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="cbcdd-438">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="cbcdd-439">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-440">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cbcdd-441">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-441">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="cbcdd-443">Sélectionnez **.NET Core** > **Application** > **API** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="cbcdd-445">Dans la boîte de dialogue **Configurer votre nouvelle API web ASP.NET Core**, acceptez la valeur par défaut \**.NET Core 2.2* pour **Framework cible**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="cbcdd-446">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="cbcdd-448">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="cbcdd-448">Test the API</span></span>

<span data-ttu-id="cbcdd-449">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-449">The project template creates a `values` API.</span></span> <span data-ttu-id="cbcdd-450">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cbcdd-452">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="cbcdd-453">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="cbcdd-454">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="cbcdd-455">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cbcdd-457">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="cbcdd-458">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-459">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cbcdd-460">Sélectionnez **Run** > **Start Debugging** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="cbcdd-461">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="cbcdd-462">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="cbcdd-463">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="cbcdd-464">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="cbcdd-465">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="cbcdd-465">Add a model class</span></span>

<span data-ttu-id="cbcdd-466">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="cbcdd-467">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-469">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="cbcdd-470">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="cbcdd-471">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="cbcdd-472">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="cbcdd-473">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="cbcdd-474">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cbcdd-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cbcdd-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cbcdd-476">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="cbcdd-477">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-478">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cbcdd-479">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-479">Right-click the project.</span></span> <span data-ttu-id="cbcdd-480">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="cbcdd-481">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-481">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="cbcdd-483">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="cbcdd-484">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="cbcdd-485">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="cbcdd-486">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="cbcdd-487">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="cbcdd-488">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="cbcdd-488">Add a database context</span></span>

<span data-ttu-id="cbcdd-489">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="cbcdd-490">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-492">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="cbcdd-493">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-494">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="cbcdd-495">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="cbcdd-496">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="cbcdd-497">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="cbcdd-497">Register the database context</span></span>

<span data-ttu-id="cbcdd-498">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cbcdd-499">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="cbcdd-500">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="cbcdd-501">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-501">The preceding code:</span></span>

* <span data-ttu-id="cbcdd-502">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="cbcdd-503">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="cbcdd-504">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="cbcdd-505">Ajout d'un contrôleur</span><span class="sxs-lookup"><span data-stu-id="cbcdd-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-507">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="cbcdd-508">Sélectionnez **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="cbcdd-509">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="cbcdd-510">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-512">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="cbcdd-513">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="cbcdd-514">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="cbcdd-515">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-515">The preceding code:</span></span>

* <span data-ttu-id="cbcdd-516">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="cbcdd-517">Marque la classe [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="cbcdd-518">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="cbcdd-519">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="cbcdd-520">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="cbcdd-521">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="cbcdd-522">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="cbcdd-523">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="cbcdd-524">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="cbcdd-525">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="cbcdd-526">Ajouter des méthodes Get</span><span class="sxs-lookup"><span data-stu-id="cbcdd-526">Add Get methods</span></span>

<span data-ttu-id="cbcdd-527">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="cbcdd-528">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="cbcdd-529">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-529">Stop the app if it's still running.</span></span> <span data-ttu-id="cbcdd-530">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="cbcdd-531">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="cbcdd-532">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="cbcdd-533">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="cbcdd-534">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="cbcdd-534">Routing and URL paths</span></span>

<span data-ttu-id="cbcdd-535">L’attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) désigne une méthode qui répond à une demande HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="cbcdd-536">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="cbcdd-537">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="cbcdd-538">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="cbcdd-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="cbcdd-539">Pour cet exemple, le nom de classe du contrôleur étant **Todo**Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="cbcdd-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="cbcdd-540">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="cbcdd-541">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="cbcdd-542">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-542">This sample doesn't use a template.</span></span> <span data-ttu-id="cbcdd-543">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="cbcdd-544">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="cbcdd-545">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="cbcdd-546">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="cbcdd-546">Return values</span></span>

<span data-ttu-id="cbcdd-547">Le type de retour des méthodes `GetTodoItems` et `GetTodoItem` est [type ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="cbcdd-548">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="cbcdd-549">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="cbcdd-550">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="cbcdd-551">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="cbcdd-552">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="cbcdd-553">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="cbcdd-554">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="cbcdd-555">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="cbcdd-556">Tester la méthode GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="cbcdd-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="cbcdd-557">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="cbcdd-558">Installer [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="cbcdd-559">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-559">Start the web app.</span></span>
* <span data-ttu-id="cbcdd-560">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-560">Start Postman.</span></span>
* <span data-ttu-id="cbcdd-561">Désactiver la **vérification des certificats SSL**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cbcdd-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbcdd-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cbcdd-563">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cbcdd-564">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="cbcdd-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="cbcdd-565">De **Postman** > **Preferences** (onglet**général),** désactiver la vérification des **certificats SSL**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="cbcdd-566">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="cbcdd-567">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="cbcdd-568">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-568">Create a new request.</span></span>
  * <span data-ttu-id="cbcdd-569">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="cbcdd-570">Définissez l’URL de la requête sur `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="cbcdd-571">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="cbcdd-572">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="cbcdd-573">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-573">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="cbcdd-575">Ajouter une méthode Create</span><span class="sxs-lookup"><span data-stu-id="cbcdd-575">Add a Create method</span></span>

<span data-ttu-id="cbcdd-576">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="cbcdd-577">Le code précédent est une méthode HTTP [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) POST, comme indiqué par l’attribut.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="cbcdd-578">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="cbcdd-579">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="cbcdd-580">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="cbcdd-581">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="cbcdd-582">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="cbcdd-583">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="cbcdd-584">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="cbcdd-585">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="cbcdd-586">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="cbcdd-587">Tester la méthode PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="cbcdd-588">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-588">Build the project.</span></span>
* <span data-ttu-id="cbcdd-589">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="cbcdd-590">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="cbcdd-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="cbcdd-591">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="cbcdd-592">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="cbcdd-593">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="cbcdd-594">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-594">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="cbcdd-596">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="cbcdd-597">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="cbcdd-597">Test the location header URI</span></span>

* <span data-ttu-id="cbcdd-598">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="cbcdd-599">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-599">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="cbcdd-601">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-601">Set the method to GET.</span></span>
* <span data-ttu-id="cbcdd-602">Coller l’URI (par `https://localhost:5001/api/Todo/2`exemple, ).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="cbcdd-603">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="cbcdd-604">Ajouter une méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="cbcdd-605">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="cbcdd-606">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="cbcdd-607">La réponse est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="cbcdd-608">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="cbcdd-609">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="cbcdd-610">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="cbcdd-611">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="cbcdd-612">Cet exemple utilise une base de données en mémoire qui doit être parascisée chaque fois que l’application est lancée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="cbcdd-613">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="cbcdd-614">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="cbcdd-615">Mettez à jour la tâche dont l’id est 1 en définissant son nom sur « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="cbcdd-616">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-616">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="cbcdd-618">Ajouter une méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="cbcdd-619">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="cbcdd-620">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="cbcdd-621">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="cbcdd-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="cbcdd-622">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="cbcdd-623">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="cbcdd-624">Définissez l’URI de l’objet `https://localhost:5001/api/todo/1`à supprimer (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="cbcdd-625">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-625">Select **Send**.</span></span>

<span data-ttu-id="cbcdd-626">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="cbcdd-627">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="cbcdd-628">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="cbcdd-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="cbcdd-629">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="cbcdd-630">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-630">jQuery initiates the request.</span></span> <span data-ttu-id="cbcdd-631">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="cbcdd-632">Configurez l’application pour [traiter les fichiers statiques](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [activer le mappage de fichier par défaut](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="cbcdd-633">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="cbcdd-634">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="cbcdd-635">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="cbcdd-636">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="cbcdd-637">Remplacez son contenu par le code ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="cbcdd-638">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="cbcdd-639">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="cbcdd-640">Supprimer `launchUrl` la propriété pour forcer l’application à ouvrir à *index.html*&mdash;le fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="cbcdd-641">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="cbcdd-642">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="cbcdd-643">Obtenir une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="cbcdd-643">Get a list of to-do items</span></span>

<span data-ttu-id="cbcdd-644">jQuery envoie une demande HTTP GET à l’API web, qui renvoie JSON représentant un tableau d’articles à faire.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="cbcdd-645">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="cbcdd-646">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="cbcdd-647">Ajouter une tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-647">Add a to-do item</span></span>

<span data-ttu-id="cbcdd-648">jQuery envoie une demande HTTP POST avec l’élément à faire dans le corps de demande.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="cbcdd-649">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="cbcdd-650">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="cbcdd-651">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="cbcdd-652">Mettre à jour une tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-652">Update a to-do item</span></span>

<span data-ttu-id="cbcdd-653">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="cbcdd-654">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="cbcdd-655">Supprimer une tâche</span><span class="sxs-lookup"><span data-stu-id="cbcdd-655">Delete a to-do item</span></span>

<span data-ttu-id="cbcdd-656">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="cbcdd-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="cbcdd-657">Ajouter un support d’authentification à une API web</span><span class="sxs-lookup"><span data-stu-id="cbcdd-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="cbcdd-658">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cbcdd-658">Additional resources</span></span>

<span data-ttu-id="cbcdd-659">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="cbcdd-660">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="cbcdd-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="cbcdd-661">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbcdd-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="cbcdd-662">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="cbcdd-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
