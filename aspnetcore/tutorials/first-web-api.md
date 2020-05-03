---
title: 'Didacticiel : créer une API Web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 4e205c737f606579590854b679e669cbdd0cd5ab
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727792"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="b9503-103">Didacticiel : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9503-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="b9503-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="b9503-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="b9503-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9503-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b9503-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="b9503-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9503-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-107">Create a web API project.</span></span>
> * <span data-ttu-id="b9503-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b9503-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b9503-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="b9503-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b9503-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="b9503-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b9503-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="b9503-111">Call the web API with Postman.</span></span>

<span data-ttu-id="b9503-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="b9503-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b9503-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="b9503-113">Overview</span></span>

<span data-ttu-id="b9503-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="b9503-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b9503-115">API</span><span class="sxs-lookup"><span data-stu-id="b9503-115">API</span></span> | <span data-ttu-id="b9503-116">Description</span><span class="sxs-lookup"><span data-stu-id="b9503-116">Description</span></span> | <span data-ttu-id="b9503-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="b9503-117">Request body</span></span> | <span data-ttu-id="b9503-118">Response body</span><span class="sxs-lookup"><span data-stu-id="b9503-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b9503-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="b9503-119">Get all to-do items</span></span> | <span data-ttu-id="b9503-120">None</span><span class="sxs-lookup"><span data-stu-id="b9503-120">None</span></span> | <span data-ttu-id="b9503-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="b9503-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b9503-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="b9503-122">Get an item by ID</span></span> | <span data-ttu-id="b9503-123">None</span><span class="sxs-lookup"><span data-stu-id="b9503-123">None</span></span> | <span data-ttu-id="b9503-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b9503-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="b9503-125">Add a new item</span></span> | <span data-ttu-id="b9503-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-126">To-do item</span></span> | <span data-ttu-id="b9503-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b9503-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b9503-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-129">To-do item</span></span> | <span data-ttu-id="b9503-130">None</span><span class="sxs-lookup"><span data-stu-id="b9503-130">None</span></span> |
|<span data-ttu-id="b9503-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9503-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9503-133">None</span><span class="sxs-lookup"><span data-stu-id="b9503-133">None</span></span> | <span data-ttu-id="b9503-134">None</span><span class="sxs-lookup"><span data-stu-id="b9503-134">None</span></span>|

<span data-ttu-id="b9503-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b9503-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b9503-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b9503-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="b9503-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-147">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="b9503-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b9503-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="b9503-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b9503-149">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b9503-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="b9503-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="b9503-151">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-151">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9503-154">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b9503-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b9503-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b9503-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9503-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b9503-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b9503-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="b9503-158">The preceding commands:</span></span>

  * <span data-ttu-id="b9503-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b9503-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b9503-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="b9503-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9503-162">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="b9503-162">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b9503-164">Sélectionnez **.NET Core** > **Application** > **API** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="b9503-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="b9503-166">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , sélectionnez **Framework cible** de \**.net Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="b9503-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="b9503-167">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b9503-169">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9503-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="b9503-170">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="b9503-170">Test the API</span></span>

<span data-ttu-id="b9503-171">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="b9503-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="b9503-172">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9503-174">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9503-175">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b9503-176">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b9503-177">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b9503-179">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9503-180">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="b9503-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-181">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b9503-182">Sélectionnez **exécuter** > **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b9503-183">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b9503-184">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="b9503-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b9503-185">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="b9503-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="b9503-186">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="b9503-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="b9503-187">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="b9503-187">Add a model class</span></span>

<span data-ttu-id="b9503-188">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b9503-189">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="b9503-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-191">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b9503-192">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="b9503-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9503-193">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="b9503-194">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="b9503-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9503-195">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b9503-196">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9503-198">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b9503-199">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-200">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9503-201">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-201">Right-click the project.</span></span> <span data-ttu-id="b9503-202">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="b9503-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9503-203">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-203">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b9503-205">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="b9503-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b9503-206">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="b9503-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b9503-207">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b9503-208">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="b9503-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b9503-209">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="b9503-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b9503-210">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b9503-210">Add a database context</span></span>

<span data-ttu-id="b9503-211">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b9503-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b9503-212">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="b9503-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="b9503-214">Ajouter Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="b9503-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="b9503-215">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="b9503-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b9503-216">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="b9503-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="b9503-217">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="b9503-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="b9503-218">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="b9503-219">Utilisez les instructions précédentes pour ajouter le package NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="b9503-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b9503-221">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="b9503-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="b9503-222">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="b9503-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9503-223">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9503-224">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9503-225">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b9503-226">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b9503-227">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b9503-227">Register the database context</span></span>

<span data-ttu-id="b9503-228">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b9503-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9503-229">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="b9503-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="b9503-230">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b9503-231">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b9503-231">The preceding code:</span></span>

* <span data-ttu-id="b9503-232">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="b9503-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b9503-233">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b9503-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b9503-234">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b9503-235">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="b9503-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-237">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="b9503-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b9503-238">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="b9503-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b9503-239">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="b9503-240">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="b9503-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b9503-241">Sélectionnez **TodoItem (TodoApi. Models)** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="b9503-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b9503-242">Sélectionnez **TodoContext (TodoApi. Models)** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="b9503-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b9503-243">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9503-244">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b9503-245">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9503-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b9503-246">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="b9503-246">The preceding commands:</span></span>

* <span data-ttu-id="b9503-247">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b9503-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b9503-248">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="b9503-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b9503-249">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="b9503-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b9503-250">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="b9503-250">The generated code:</span></span>

* <span data-ttu-id="b9503-251">Marque la classe avec l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="b9503-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b9503-252">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b9503-253">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="b9503-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b9503-254">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b9503-255">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b9503-256">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="b9503-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b9503-257">Les contrôleurs avec `[action]` des vues sont inclus dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="b9503-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b9503-258">Les contrôleurs d' `[action]` API n’incluent pas dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="b9503-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b9503-259">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="b9503-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b9503-260">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="b9503-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="b9503-261">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="b9503-262">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="b9503-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b9503-263">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="b9503-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b9503-264">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9503-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b9503-265">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="b9503-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b9503-266">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="b9503-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="b9503-267">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="b9503-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b9503-268">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="b9503-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b9503-269">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="b9503-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b9503-270">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="b9503-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b9503-271">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="b9503-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b9503-272">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="b9503-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b9503-273">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="b9503-273">Install Postman</span></span>

<span data-ttu-id="b9503-274">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b9503-275">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="b9503-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b9503-276">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="b9503-276">Start the web app.</span></span>
* <span data-ttu-id="b9503-277">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="b9503-277">Start Postman.</span></span>
* <span data-ttu-id="b9503-278">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="b9503-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b9503-279">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="b9503-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b9503-280">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b9503-281">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="b9503-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b9503-282">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="b9503-282">Create a new request.</span></span>
* <span data-ttu-id="b9503-283">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9503-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b9503-284">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="b9503-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="b9503-285">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="b9503-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b9503-286">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="b9503-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b9503-287">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="b9503-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b9503-288">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-288">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b9503-290">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="b9503-290">Test the location header URI</span></span>

* <span data-ttu-id="b9503-291">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="b9503-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b9503-292">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="b9503-292">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="b9503-294">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="b9503-294">Set the method to GET.</span></span>
* <span data-ttu-id="b9503-295">Collez l’URI (par exemple `https://localhost:5001/api/TodoItems/1`,).</span><span class="sxs-lookup"><span data-stu-id="b9503-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b9503-296">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b9503-297">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="b9503-297">Examine the GET methods</span></span>

<span data-ttu-id="b9503-298">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="b9503-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b9503-299">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="b9503-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b9503-300">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b9503-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b9503-301">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="b9503-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b9503-302">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="b9503-302">Test Get with Postman</span></span>

* <span data-ttu-id="b9503-303">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="b9503-303">Create a new request.</span></span>
* <span data-ttu-id="b9503-304">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="b9503-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b9503-305">Définissez l’URL de la requête sur `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="b9503-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b9503-306">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="b9503-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b9503-307">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="b9503-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b9503-308">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-308">Select **Send**.</span></span>

<span data-ttu-id="b9503-309">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-309">This app uses an in-memory database.</span></span> <span data-ttu-id="b9503-310">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="b9503-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b9503-311">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b9503-312">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="b9503-312">Routing and URL paths</span></span>

<span data-ttu-id="b9503-313">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="b9503-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b9503-314">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="b9503-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b9503-315">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="b9503-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b9503-316">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="b9503-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b9503-317">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems**Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="b9503-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b9503-318">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b9503-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b9503-319">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="b9503-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b9503-320">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="b9503-320">This sample doesn't use a template.</span></span> <span data-ttu-id="b9503-321">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="b9503-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9503-322">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="b9503-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b9503-323">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="b9503-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b9503-324">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="b9503-324">Return values</span></span>

<span data-ttu-id="b9503-325">Le type de retour des méthodes `GetTodoItems` et `GetTodoItem` est [type ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="b9503-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b9503-326">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="b9503-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b9503-327">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="b9503-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b9503-328">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="b9503-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b9503-329">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9503-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b9503-330">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="b9503-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b9503-331">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="b9503-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b9503-332">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="b9503-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b9503-333">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="b9503-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b9503-334">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-334">The PutTodoItem method</span></span>

<span data-ttu-id="b9503-335">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="b9503-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b9503-336">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b9503-337">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="b9503-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b9503-338">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="b9503-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b9503-339">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="b9503-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b9503-340">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="b9503-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b9503-341">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="b9503-342">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="b9503-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b9503-343">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b9503-344">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b9503-345">Mettez à jour la tâche dont l’ID = 1 et nommez-la « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="b9503-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b9503-346">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="b9503-346">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b9503-348">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="b9503-349">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="b9503-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b9503-350">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b9503-351">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="b9503-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b9503-352">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="b9503-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b9503-353">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="b9503-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b9503-354">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b9503-355">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="b9503-355">Prevent over-posting</span></span>

<span data-ttu-id="b9503-356">Actuellement, l’exemple d’application expose `TodoItem` l’objet entier.</span><span class="sxs-lookup"><span data-stu-id="b9503-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b9503-357">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="b9503-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b9503-358">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="b9503-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b9503-359">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="b9503-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b9503-360">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="b9503-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="b9503-361">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="b9503-361">A DTO may be used to:</span></span>

* <span data-ttu-id="b9503-362">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="b9503-362">Prevent over-posting.</span></span>
* <span data-ttu-id="b9503-363">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="b9503-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b9503-364">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="b9503-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b9503-365">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="b9503-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b9503-366">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="b9503-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b9503-367">Pour illustrer l’approche DTO, mettez `TodoItem` à jour la classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="b9503-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="b9503-368">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="b9503-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b9503-369">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="b9503-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b9503-370">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="b9503-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b9503-371">Mettez à `TodoItemsController` jour le `TodoItemDTO`pour utiliser :</span><span class="sxs-lookup"><span data-stu-id="b9503-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b9503-372">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="b9503-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b9503-373">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="b9503-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="b9503-374">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="b9503-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b9503-375">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="b9503-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9503-376">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-376">Create a web API project.</span></span>
> * <span data-ttu-id="b9503-377">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b9503-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b9503-378">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="b9503-378">Add a controller.</span></span>
> * <span data-ttu-id="b9503-379">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="b9503-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="b9503-380">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="b9503-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="b9503-381">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="b9503-381">Specify return values.</span></span>
> * <span data-ttu-id="b9503-382">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="b9503-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="b9503-383">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b9503-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="b9503-384">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="b9503-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="b9503-385">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="b9503-385">Overview</span></span>

<span data-ttu-id="b9503-386">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="b9503-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b9503-387">API</span><span class="sxs-lookup"><span data-stu-id="b9503-387">API</span></span> | <span data-ttu-id="b9503-388">Description</span><span class="sxs-lookup"><span data-stu-id="b9503-388">Description</span></span> | <span data-ttu-id="b9503-389">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="b9503-389">Request body</span></span> | <span data-ttu-id="b9503-390">Response body</span><span class="sxs-lookup"><span data-stu-id="b9503-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b9503-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b9503-391">GET /api/TodoItems</span></span> | <span data-ttu-id="b9503-392">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="b9503-392">Get all to-do items</span></span> | <span data-ttu-id="b9503-393">None</span><span class="sxs-lookup"><span data-stu-id="b9503-393">None</span></span> | <span data-ttu-id="b9503-394">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="b9503-394">Array of to-do items</span></span>|
|<span data-ttu-id="b9503-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b9503-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b9503-396">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="b9503-396">Get an item by ID</span></span> | <span data-ttu-id="b9503-397">None</span><span class="sxs-lookup"><span data-stu-id="b9503-397">None</span></span> | <span data-ttu-id="b9503-398">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-398">To-do item</span></span>|
|<span data-ttu-id="b9503-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b9503-399">POST /api/TodoItems</span></span> | <span data-ttu-id="b9503-400">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="b9503-400">Add a new item</span></span> | <span data-ttu-id="b9503-401">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-401">To-do item</span></span> | <span data-ttu-id="b9503-402">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-402">To-do item</span></span> |
|<span data-ttu-id="b9503-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b9503-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b9503-404">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b9503-405">Tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-405">To-do item</span></span> | <span data-ttu-id="b9503-406">None</span><span class="sxs-lookup"><span data-stu-id="b9503-406">None</span></span> |
|<span data-ttu-id="b9503-407">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9503-408">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9503-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9503-409">None</span><span class="sxs-lookup"><span data-stu-id="b9503-409">None</span></span> | <span data-ttu-id="b9503-410">None</span><span class="sxs-lookup"><span data-stu-id="b9503-410">None</span></span>|

<span data-ttu-id="b9503-411">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-411">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b9503-417">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b9503-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-420">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b9503-421">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="b9503-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-423">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="b9503-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b9503-424">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="b9503-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b9503-425">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b9503-426">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="b9503-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="b9503-427">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b9503-428">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="b9503-428">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9503-431">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b9503-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b9503-432">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b9503-433">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9503-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="b9503-434">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="b9503-435">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-436">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9503-437">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="b9503-437">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b9503-439">Sélectionnez **.NET Core** > **Application** > **API** > **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="b9503-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS - Boîte de dialogue Nouveau projet](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="b9503-441">Dans la boîte de dialogue **Configurer votre nouvelle API web ASP.NET Core**, acceptez la valeur par défaut \**.NET Core 2.2* pour **Framework cible**.</span><span class="sxs-lookup"><span data-stu-id="b9503-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="b9503-442">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="b9503-444">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="b9503-444">Test the API</span></span>

<span data-ttu-id="b9503-445">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="b9503-445">The project template creates a `values` API.</span></span> <span data-ttu-id="b9503-446">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9503-448">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9503-449">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b9503-450">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b9503-451">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b9503-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b9503-453">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9503-454">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="b9503-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-455">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b9503-456">Sélectionnez **exécuter** > **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b9503-457">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b9503-458">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="b9503-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b9503-459">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="b9503-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="b9503-460">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="b9503-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b9503-461">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="b9503-461">Add a model class</span></span>

<span data-ttu-id="b9503-462">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="b9503-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b9503-463">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="b9503-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-465">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b9503-466">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="b9503-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9503-467">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="b9503-468">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="b9503-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9503-469">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b9503-470">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9503-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9503-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9503-472">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b9503-473">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9503-474">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9503-475">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-475">Right-click the project.</span></span> <span data-ttu-id="b9503-476">Sélectionnez **Ajouter** > **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="b9503-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9503-477">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-477">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b9503-479">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="b9503-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b9503-480">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="b9503-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b9503-481">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b9503-482">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="b9503-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b9503-483">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="b9503-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b9503-484">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b9503-484">Add a database context</span></span>

<span data-ttu-id="b9503-485">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b9503-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b9503-486">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="b9503-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-488">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="b9503-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9503-489">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9503-490">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9503-491">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="b9503-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b9503-492">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b9503-493">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b9503-493">Register the database context</span></span>

<span data-ttu-id="b9503-494">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b9503-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9503-495">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="b9503-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="b9503-496">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="b9503-497">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b9503-497">The preceding code:</span></span>

* <span data-ttu-id="b9503-498">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="b9503-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b9503-499">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b9503-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b9503-500">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="b9503-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b9503-501">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="b9503-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-503">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="b9503-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b9503-504">Sélectionnez **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="b9503-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="b9503-505">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="b9503-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="b9503-506">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b9503-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9503-508">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9503-509">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="b9503-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="b9503-510">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="b9503-511">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b9503-511">The preceding code:</span></span>

* <span data-ttu-id="b9503-512">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="b9503-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b9503-513">Marque la classe avec l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="b9503-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b9503-514">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b9503-515">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="b9503-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b9503-516">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b9503-517">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="b9503-518">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="b9503-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="b9503-519">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="b9503-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="b9503-520">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="b9503-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="b9503-521">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="b9503-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="b9503-522">Ajouter des méthodes Get</span><span class="sxs-lookup"><span data-stu-id="b9503-522">Add Get methods</span></span>

<span data-ttu-id="b9503-523">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="b9503-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="b9503-524">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="b9503-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="b9503-525">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="b9503-525">Stop the app if it's still running.</span></span> <span data-ttu-id="b9503-526">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="b9503-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="b9503-527">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="b9503-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="b9503-528">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b9503-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="b9503-529">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="b9503-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="b9503-530">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="b9503-530">Routing and URL paths</span></span>

<span data-ttu-id="b9503-531">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="b9503-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b9503-532">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="b9503-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b9503-533">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="b9503-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="b9503-534">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="b9503-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b9503-535">Pour cet exemple, le nom de classe du contrôleur étant **Todo**Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="b9503-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="b9503-536">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b9503-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b9503-537">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="b9503-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b9503-538">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="b9503-538">This sample doesn't use a template.</span></span> <span data-ttu-id="b9503-539">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="b9503-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9503-540">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="b9503-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b9503-541">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="b9503-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b9503-542">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="b9503-542">Return values</span></span>

<span data-ttu-id="b9503-543">Le type de retour des méthodes `GetTodoItems` et `GetTodoItem` est [type ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="b9503-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b9503-544">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="b9503-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b9503-545">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="b9503-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b9503-546">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="b9503-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b9503-547">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9503-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b9503-548">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="b9503-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b9503-549">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="b9503-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b9503-550">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="b9503-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b9503-551">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="b9503-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="b9503-552">Tester la méthode GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="b9503-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="b9503-553">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b9503-554">Installez le [poste de publication](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="b9503-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="b9503-555">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="b9503-555">Start the web app.</span></span>
* <span data-ttu-id="b9503-556">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="b9503-556">Start Postman.</span></span>
* <span data-ttu-id="b9503-557">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="b9503-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9503-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9503-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9503-559">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="b9503-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9503-560">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b9503-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9503-561">Dans **Postman** > **Préférences** de publication (onglet**général** ), désactivez la **vérification de certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="b9503-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="b9503-562">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="b9503-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="b9503-563">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b9503-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="b9503-564">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="b9503-564">Create a new request.</span></span>
  * <span data-ttu-id="b9503-565">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="b9503-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="b9503-566">Définissez l’URL de la requête sur `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="b9503-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="b9503-567">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="b9503-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="b9503-568">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="b9503-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b9503-569">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-569">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="b9503-571">Ajouter une méthode Create</span><span class="sxs-lookup"><span data-stu-id="b9503-571">Add a Create method</span></span>

<span data-ttu-id="b9503-572">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="b9503-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="b9503-573">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="b9503-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b9503-574">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9503-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b9503-575">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="b9503-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="b9503-576">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="b9503-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="b9503-577">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="b9503-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b9503-578">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="b9503-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="b9503-579">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="b9503-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="b9503-580">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="b9503-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b9503-581">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="b9503-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b9503-582">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="b9503-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="b9503-583">Tester la méthode PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="b9503-584">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-584">Build the project.</span></span>
* <span data-ttu-id="b9503-585">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="b9503-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b9503-586">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="b9503-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="b9503-587">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="b9503-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b9503-588">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="b9503-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b9503-589">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="b9503-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b9503-590">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-590">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="b9503-592">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="b9503-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b9503-593">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="b9503-593">Test the location header URI</span></span>

* <span data-ttu-id="b9503-594">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="b9503-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b9503-595">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="b9503-595">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="b9503-597">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="b9503-597">Set the method to GET.</span></span>
* <span data-ttu-id="b9503-598">Collez l’URI (par exemple `https://localhost:5001/api/Todo/2`,).</span><span class="sxs-lookup"><span data-stu-id="b9503-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="b9503-599">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="b9503-600">Ajouter une méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="b9503-601">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="b9503-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="b9503-602">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b9503-603">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="b9503-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b9503-604">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="b9503-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b9503-605">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="b9503-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b9503-606">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="b9503-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b9503-607">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="b9503-608">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="b9503-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b9503-609">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b9503-610">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="b9503-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b9503-611">Mettez à jour la tâche dont l’id est 1 en définissant son nom sur « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="b9503-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b9503-612">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="b9503-612">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="b9503-614">Ajouter une méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="b9503-615">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="b9503-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="b9503-616">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="b9503-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b9503-617">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9503-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b9503-618">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="b9503-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b9503-619">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="b9503-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b9503-620">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="b9503-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="b9503-621">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="b9503-621">Select **Send**.</span></span>

<span data-ttu-id="b9503-622">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="b9503-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="b9503-623">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="b9503-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b9503-624">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="b9503-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="b9503-625">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="b9503-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="b9503-626">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="b9503-626">jQuery initiates the request.</span></span> <span data-ttu-id="b9503-627">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="b9503-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="b9503-628">Configurez l’application pour [traiter les fichiers statiques](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [activer le mappage de fichier par défaut](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="b9503-629">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="b9503-630">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b9503-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="b9503-631">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="b9503-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="b9503-632">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b9503-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="b9503-633">Remplacez son contenu par le code ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="b9503-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="b9503-634">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="b9503-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="b9503-635">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b9503-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="b9503-636">Supprimez `launchUrl` la propriété pour forcer l’ouverture de l’application à *index. html*&mdash;, le fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="b9503-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="b9503-637">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="b9503-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="b9503-638">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="b9503-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="b9503-639">Obtenir une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="b9503-639">Get a list of to-do items</span></span>

<span data-ttu-id="b9503-640">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="b9503-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="b9503-641">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="b9503-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="b9503-642">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="b9503-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="b9503-643">Ajouter une tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-643">Add a to-do item</span></span>

<span data-ttu-id="b9503-644">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="b9503-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="b9503-645">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="b9503-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="b9503-646">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="b9503-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="b9503-647">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="b9503-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="b9503-648">Mettre à jour une tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-648">Update a to-do item</span></span>

<span data-ttu-id="b9503-649">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="b9503-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="b9503-650">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="b9503-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="b9503-651">Supprimer une tâche</span><span class="sxs-lookup"><span data-stu-id="b9503-651">Delete a to-do item</span></span>

<span data-ttu-id="b9503-652">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="b9503-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="b9503-653">Ajouter la prise en charge de l’authentification à une API Web</span><span class="sxs-lookup"><span data-stu-id="b9503-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="b9503-654">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b9503-654">Additional resources</span></span>

<span data-ttu-id="b9503-655">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="b9503-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="b9503-656">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b9503-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b9503-657">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9503-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="b9503-658">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="b9503-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
