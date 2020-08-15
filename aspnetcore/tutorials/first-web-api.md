---
title: 'Didacticiel : créer une API Web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 15e5c838e6dae824a189f170b28730a63f8c3ea7
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253640"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="dfaaa-103">Didacticiel : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dfaaa-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="dfaaa-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="dfaaa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="dfaaa-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dfaaa-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dfaaa-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-107">Create a web API project.</span></span>
> * <span data-ttu-id="dfaaa-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="dfaaa-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="dfaaa-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="dfaaa-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="dfaaa-111">Call the web API with Postman.</span></span>

<span data-ttu-id="dfaaa-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="dfaaa-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="dfaaa-113">Overview</span></span>

<span data-ttu-id="dfaaa-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="dfaaa-115">API</span><span class="sxs-lookup"><span data-stu-id="dfaaa-115">API</span></span> | <span data-ttu-id="dfaaa-116">Description</span><span class="sxs-lookup"><span data-stu-id="dfaaa-116">Description</span></span> | <span data-ttu-id="dfaaa-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="dfaaa-117">Request body</span></span> | <span data-ttu-id="dfaaa-118">Response body</span><span class="sxs-lookup"><span data-stu-id="dfaaa-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="dfaaa-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="dfaaa-119">Get all to-do items</span></span> | <span data-ttu-id="dfaaa-120">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-120">None</span></span> | <span data-ttu-id="dfaaa-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="dfaaa-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="dfaaa-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="dfaaa-122">Get an item by ID</span></span> | <span data-ttu-id="dfaaa-123">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-123">None</span></span> | <span data-ttu-id="dfaaa-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="dfaaa-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="dfaaa-125">Add a new item</span></span> | <span data-ttu-id="dfaaa-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-126">To-do item</span></span> | <span data-ttu-id="dfaaa-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="dfaaa-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="dfaaa-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-129">To-do item</span></span> | <span data-ttu-id="dfaaa-130">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-130">None</span></span> |
|<span data-ttu-id="dfaaa-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="dfaaa-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="dfaaa-133">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-133">None</span></span> | <span data-ttu-id="dfaaa-134">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-134">None</span></span>|

<span data-ttu-id="dfaaa-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="dfaaa-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dfaaa-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="dfaaa-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="dfaaa-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-147">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dfaaa-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="dfaaa-149">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="dfaaa-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="dfaaa-151">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-151">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dfaaa-154">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dfaaa-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="dfaaa-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="dfaaa-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="dfaaa-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-158">The preceding commands:</span></span>

  * <span data-ttu-id="dfaaa-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="dfaaa-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dfaaa-162">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-162">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="dfaaa-164">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="dfaaa-165">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API**application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="dfaaa-167">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="dfaaa-168">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-168">Select **Next**.</span></span>

* <span data-ttu-id="dfaaa-169">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="dfaaa-171">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="dfaaa-172">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="dfaaa-172">Test the API</span></span>

<span data-ttu-id="dfaaa-173">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="dfaaa-174">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dfaaa-176">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dfaaa-177">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="dfaaa-178">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="dfaaa-179">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dfaaa-181">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dfaaa-182">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-183">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dfaaa-184">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="dfaaa-185">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="dfaaa-186">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="dfaaa-187">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="dfaaa-188">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="dfaaa-189">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="dfaaa-189">Add a model class</span></span>

<span data-ttu-id="dfaaa-190">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="dfaaa-191">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-193">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="dfaaa-194">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dfaaa-195">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="dfaaa-196">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dfaaa-197">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="dfaaa-198">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dfaaa-200">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="dfaaa-201">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-202">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dfaaa-203">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-203">Right-click the project.</span></span> <span data-ttu-id="dfaaa-204">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dfaaa-205">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-205">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="dfaaa-207">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="dfaaa-208">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="dfaaa-209">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="dfaaa-210">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="dfaaa-211">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="dfaaa-212">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="dfaaa-212">Add a database context</span></span>

<span data-ttu-id="dfaaa-213">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="dfaaa-214">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="dfaaa-216">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="dfaaa-216">Add NuGet packages</span></span>

* <span data-ttu-id="dfaaa-217">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="dfaaa-218">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="dfaaa-219">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="dfaaa-220">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="dfaaa-221">Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="dfaaa-223">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="dfaaa-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="dfaaa-224">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dfaaa-225">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-226">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dfaaa-227">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="dfaaa-228">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="dfaaa-229">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="dfaaa-229">Register the database context</span></span>

<span data-ttu-id="dfaaa-230">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dfaaa-231">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="dfaaa-232">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="dfaaa-233">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-233">The preceding code:</span></span>

* <span data-ttu-id="dfaaa-234">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="dfaaa-235">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="dfaaa-236">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="dfaaa-237">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="dfaaa-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-239">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="dfaaa-240">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="dfaaa-241">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="dfaaa-242">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="dfaaa-243">Sélectionnez **TodoItem (TodoApi. Models)** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="dfaaa-244">Sélectionnez **TodoContext (TodoApi. Models)** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="dfaaa-245">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-246">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dfaaa-247">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet-aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="dfaaa-248">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-248">The preceding commands:</span></span>

* <span data-ttu-id="dfaaa-249">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="dfaaa-250">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="dfaaa-251">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="dfaaa-252">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-252">The generated code:</span></span>

* <span data-ttu-id="dfaaa-253">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="dfaaa-254">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="dfaaa-255">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="dfaaa-256">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="dfaaa-257">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="dfaaa-258">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="dfaaa-259">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="dfaaa-260">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="dfaaa-261">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="dfaaa-262">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="dfaaa-263">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="dfaaa-264">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="dfaaa-265">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="dfaaa-266">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="dfaaa-267">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dfaaa-268">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="dfaaa-269">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="dfaaa-270">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="dfaaa-271">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="dfaaa-272">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="dfaaa-273">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="dfaaa-274">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="dfaaa-275">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="dfaaa-276">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="dfaaa-276">Install Postman</span></span>

<span data-ttu-id="dfaaa-277">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="dfaaa-278">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="dfaaa-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="dfaaa-279">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-279">Start the web app.</span></span>
* <span data-ttu-id="dfaaa-280">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-280">Start Postman.</span></span>
* <span data-ttu-id="dfaaa-281">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="dfaaa-282">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="dfaaa-283">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="dfaaa-284">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="dfaaa-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="dfaaa-285">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-285">Create a new request.</span></span>
* <span data-ttu-id="dfaaa-286">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="dfaaa-287">Définissez l’URI sur `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-287">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="dfaaa-288">Par exemple : `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-288">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="dfaaa-289">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="dfaaa-290">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="dfaaa-291">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="dfaaa-292">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="dfaaa-293">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-293">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="dfaaa-295">Tester l’URI d’en-tête d’emplacement avec le poster</span><span class="sxs-lookup"><span data-stu-id="dfaaa-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="dfaaa-296">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="dfaaa-297">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-297">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="dfaaa-299">Affectez `GET` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="dfaaa-300">Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="dfaaa-301">Par exemple : `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="dfaaa-302">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="dfaaa-303">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="dfaaa-303">Examine the GET methods</span></span>

<span data-ttu-id="dfaaa-304">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="dfaaa-305">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="dfaaa-306">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="dfaaa-307">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="dfaaa-308">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="dfaaa-308">Test Get with Postman</span></span>

* <span data-ttu-id="dfaaa-309">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-309">Create a new request.</span></span>
* <span data-ttu-id="dfaaa-310">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="dfaaa-311">Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="dfaaa-312">Par exemple : `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="dfaaa-313">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="dfaaa-314">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-314">Select **Send**.</span></span>

<span data-ttu-id="dfaaa-315">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-315">This app uses an in-memory database.</span></span> <span data-ttu-id="dfaaa-316">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="dfaaa-317">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="dfaaa-318">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="dfaaa-318">Routing and URL paths</span></span>

<span data-ttu-id="dfaaa-319">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="dfaaa-320">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="dfaaa-321">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="dfaaa-322">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="dfaaa-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="dfaaa-323">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems**Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="dfaaa-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="dfaaa-324">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="dfaaa-325">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="dfaaa-326">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-326">This sample doesn't use a template.</span></span> <span data-ttu-id="dfaaa-327">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dfaaa-328">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="dfaaa-329">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="dfaaa-330">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="dfaaa-330">Return values</span></span>

<span data-ttu-id="dfaaa-331">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="dfaaa-332">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="dfaaa-333">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="dfaaa-334">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="dfaaa-335">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="dfaaa-336">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="dfaaa-337">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="dfaaa-338">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="dfaaa-339">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="dfaaa-340">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-340">The PutTodoItem method</span></span>

<span data-ttu-id="dfaaa-341">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="dfaaa-342">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="dfaaa-343">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="dfaaa-344">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="dfaaa-345">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="dfaaa-346">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="dfaaa-347">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="dfaaa-348">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="dfaaa-349">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="dfaaa-350">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="dfaaa-351">Mettez à jour la tâche dont l’ID = 1 et nommez-la « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="dfaaa-352">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-352">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="dfaaa-354">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="dfaaa-355">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="dfaaa-356">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="dfaaa-357">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="dfaaa-358">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="dfaaa-359">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="dfaaa-360">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="dfaaa-361">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="dfaaa-361">Prevent over-posting</span></span>

<span data-ttu-id="dfaaa-362">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="dfaaa-363">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="dfaaa-364">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="dfaaa-365">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="dfaaa-366">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="dfaaa-367">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-367">A DTO may be used to:</span></span>

* <span data-ttu-id="dfaaa-368">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-368">Prevent over-posting.</span></span>
* <span data-ttu-id="dfaaa-369">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="dfaaa-370">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="dfaaa-371">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="dfaaa-372">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="dfaaa-373">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="dfaaa-374">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="dfaaa-375">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="dfaaa-376">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="dfaaa-377">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="dfaaa-378">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="dfaaa-379">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="dfaaa-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="dfaaa-380">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dfaaa-381">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dfaaa-382">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-382">Create a web API project.</span></span>
> * <span data-ttu-id="dfaaa-383">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="dfaaa-384">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="dfaaa-384">Add a controller.</span></span>
> * <span data-ttu-id="dfaaa-385">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="dfaaa-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="dfaaa-386">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="dfaaa-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="dfaaa-387">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="dfaaa-387">Specify return values.</span></span>
> * <span data-ttu-id="dfaaa-388">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="dfaaa-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="dfaaa-389">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="dfaaa-390">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="dfaaa-391">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="dfaaa-391">Overview</span></span>

<span data-ttu-id="dfaaa-392">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="dfaaa-393">API</span><span class="sxs-lookup"><span data-stu-id="dfaaa-393">API</span></span> | <span data-ttu-id="dfaaa-394">Description</span><span class="sxs-lookup"><span data-stu-id="dfaaa-394">Description</span></span> | <span data-ttu-id="dfaaa-395">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="dfaaa-395">Request body</span></span> | <span data-ttu-id="dfaaa-396">Response body</span><span class="sxs-lookup"><span data-stu-id="dfaaa-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="dfaaa-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dfaaa-397">GET /api/TodoItems</span></span> | <span data-ttu-id="dfaaa-398">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="dfaaa-398">Get all to-do items</span></span> | <span data-ttu-id="dfaaa-399">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-399">None</span></span> | <span data-ttu-id="dfaaa-400">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="dfaaa-400">Array of to-do items</span></span>|
|<span data-ttu-id="dfaaa-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dfaaa-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="dfaaa-402">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="dfaaa-402">Get an item by ID</span></span> | <span data-ttu-id="dfaaa-403">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-403">None</span></span> | <span data-ttu-id="dfaaa-404">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-404">To-do item</span></span>|
|<span data-ttu-id="dfaaa-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dfaaa-405">POST /api/TodoItems</span></span> | <span data-ttu-id="dfaaa-406">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="dfaaa-406">Add a new item</span></span> | <span data-ttu-id="dfaaa-407">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-407">To-do item</span></span> | <span data-ttu-id="dfaaa-408">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-408">To-do item</span></span> |
|<span data-ttu-id="dfaaa-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dfaaa-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="dfaaa-410">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="dfaaa-411">Tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-411">To-do item</span></span> | <span data-ttu-id="dfaaa-412">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-412">None</span></span> |
|<span data-ttu-id="dfaaa-413">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="dfaaa-414">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="dfaaa-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="dfaaa-415">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-415">None</span></span> | <span data-ttu-id="dfaaa-416">None</span><span class="sxs-lookup"><span data-stu-id="dfaaa-416">None</span></span>|

<span data-ttu-id="dfaaa-417">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-417">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="dfaaa-423">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dfaaa-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-426">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="dfaaa-427">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="dfaaa-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-429">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dfaaa-430">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="dfaaa-431">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="dfaaa-432">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="dfaaa-433">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="dfaaa-434">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-434">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dfaaa-437">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dfaaa-438">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="dfaaa-439">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="dfaaa-440">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="dfaaa-441">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-442">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dfaaa-443">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-443">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="dfaaa-445">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="dfaaa-446">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API**application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="dfaaa-447">Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="dfaaa-448">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-448">Select **Next**.</span></span>

* <span data-ttu-id="dfaaa-449">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="dfaaa-451">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="dfaaa-451">Test the API</span></span>

<span data-ttu-id="dfaaa-452">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-452">The project template creates a `values` API.</span></span> <span data-ttu-id="dfaaa-453">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dfaaa-455">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dfaaa-456">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="dfaaa-457">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="dfaaa-458">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dfaaa-460">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dfaaa-461">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-462">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dfaaa-463">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="dfaaa-464">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="dfaaa-465">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="dfaaa-466">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="dfaaa-467">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="dfaaa-468">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="dfaaa-468">Add a model class</span></span>

<span data-ttu-id="dfaaa-469">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="dfaaa-470">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-472">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="dfaaa-473">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dfaaa-474">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="dfaaa-475">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dfaaa-476">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="dfaaa-477">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dfaaa-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dfaaa-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dfaaa-479">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="dfaaa-480">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-481">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dfaaa-482">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-482">Right-click the project.</span></span> <span data-ttu-id="dfaaa-483">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dfaaa-484">Nommez le dossier *modèles*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-484">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="dfaaa-486">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="dfaaa-487">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="dfaaa-488">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="dfaaa-489">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="dfaaa-490">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="dfaaa-491">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="dfaaa-491">Add a database context</span></span>

<span data-ttu-id="dfaaa-492">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="dfaaa-493">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-495">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dfaaa-496">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-497">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dfaaa-498">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="dfaaa-499">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="dfaaa-500">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="dfaaa-500">Register the database context</span></span>

<span data-ttu-id="dfaaa-501">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dfaaa-502">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="dfaaa-503">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="dfaaa-504">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-504">The preceding code:</span></span>

* <span data-ttu-id="dfaaa-505">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="dfaaa-506">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="dfaaa-507">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="dfaaa-508">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="dfaaa-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-510">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="dfaaa-511">Sélectionnez **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="dfaaa-512">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="dfaaa-513">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-515">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dfaaa-516">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="dfaaa-517">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="dfaaa-518">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-518">The preceding code:</span></span>

* <span data-ttu-id="dfaaa-519">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="dfaaa-520">Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="dfaaa-521">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="dfaaa-522">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="dfaaa-523">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="dfaaa-524">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="dfaaa-525">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="dfaaa-526">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="dfaaa-527">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="dfaaa-528">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="dfaaa-529">Ajouter des méthodes Get</span><span class="sxs-lookup"><span data-stu-id="dfaaa-529">Add Get methods</span></span>

<span data-ttu-id="dfaaa-530">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="dfaaa-531">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="dfaaa-532">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-532">Stop the app if it's still running.</span></span> <span data-ttu-id="dfaaa-533">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="dfaaa-534">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="dfaaa-535">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="dfaaa-536">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="dfaaa-537">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="dfaaa-537">Routing and URL paths</span></span>

<span data-ttu-id="dfaaa-538">L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="dfaaa-539">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="dfaaa-540">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="dfaaa-541">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="dfaaa-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="dfaaa-542">Pour cet exemple, le nom de classe du contrôleur étant **Todo**Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="dfaaa-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="dfaaa-543">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="dfaaa-544">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="dfaaa-545">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-545">This sample doesn't use a template.</span></span> <span data-ttu-id="dfaaa-546">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dfaaa-547">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="dfaaa-548">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="dfaaa-549">Valeurs retournées</span><span class="sxs-lookup"><span data-stu-id="dfaaa-549">Return values</span></span>

<span data-ttu-id="dfaaa-550">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="dfaaa-551">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="dfaaa-552">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="dfaaa-553">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="dfaaa-554">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="dfaaa-555">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="dfaaa-556">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="dfaaa-557">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="dfaaa-558">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="dfaaa-559">Tester la méthode GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="dfaaa-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="dfaaa-560">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="dfaaa-561">Installez le [poste de publication](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="dfaaa-562">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-562">Start the web app.</span></span>
* <span data-ttu-id="dfaaa-563">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-563">Start Postman.</span></span>
* <span data-ttu-id="dfaaa-564">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dfaaa-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfaaa-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dfaaa-566">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dfaaa-567">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dfaaa-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dfaaa-568">Dans **Postman**  >  **Préférences** de publication (onglet**général** ), désactivez la vérification de **certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="dfaaa-569">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="dfaaa-570">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="dfaaa-571">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-571">Create a new request.</span></span>
  * <span data-ttu-id="dfaaa-572">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="dfaaa-573">Définissez l’URI de la demande sur `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="dfaaa-574">Par exemple : `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="dfaaa-575">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="dfaaa-576">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-576">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="dfaaa-578">Ajouter une méthode Create</span><span class="sxs-lookup"><span data-stu-id="dfaaa-578">Add a Create method</span></span>

<span data-ttu-id="dfaaa-579">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="dfaaa-580">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="dfaaa-581">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="dfaaa-582">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="dfaaa-583">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="dfaaa-584">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="dfaaa-585">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="dfaaa-586">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="dfaaa-587">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="dfaaa-588">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="dfaaa-589">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="dfaaa-590">Tester la méthode PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="dfaaa-591">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-591">Build the project.</span></span>
* <span data-ttu-id="dfaaa-592">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="dfaaa-593">Définissez l’URI sur `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="dfaaa-594">Par exemple : `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="dfaaa-595">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="dfaaa-596">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="dfaaa-597">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="dfaaa-598">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="dfaaa-599">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-599">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="dfaaa-601">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="dfaaa-602">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="dfaaa-602">Test the location header URI</span></span>

* <span data-ttu-id="dfaaa-603">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="dfaaa-604">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-604">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="dfaaa-606">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-606">Set the method to GET.</span></span>
<span data-ttu-id="dfaaa-607">\* Définissez l’URI sur  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="dfaaa-608">Par exemple,  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="dfaaa-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="dfaaa-609">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="dfaaa-610">Ajouter une méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="dfaaa-611">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="dfaaa-612">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="dfaaa-613">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="dfaaa-614">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="dfaaa-615">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="dfaaa-616">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="dfaaa-617">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="dfaaa-618">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="dfaaa-619">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="dfaaa-620">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="dfaaa-621">Mettez à jour la tâche dont l’id est 1 en définissant son nom sur « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="dfaaa-622">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-622">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="dfaaa-624">Ajouter une méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="dfaaa-625">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="dfaaa-626">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="dfaaa-627">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dfaaa-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="dfaaa-628">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="dfaaa-629">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="dfaaa-630">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="dfaaa-631">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-631">Select **Send**.</span></span>

<span data-ttu-id="dfaaa-632">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="dfaaa-633">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="dfaaa-634">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="dfaaa-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="dfaaa-635">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="dfaaa-636">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-636">jQuery initiates the request.</span></span> <span data-ttu-id="dfaaa-637">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="dfaaa-638">Configurez l’application pour [traiter les fichiers statiques](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) et [activer le mappage de fichier par défaut](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="dfaaa-639">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="dfaaa-640">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="dfaaa-641">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="dfaaa-642">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="dfaaa-643">Remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="dfaaa-644">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="dfaaa-645">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="dfaaa-646">Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="dfaaa-647">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="dfaaa-648">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="dfaaa-649">Obtenir une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="dfaaa-649">Get a list of to-do items</span></span>

<span data-ttu-id="dfaaa-650">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="dfaaa-651">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="dfaaa-652">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="dfaaa-653">Ajouter une tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-653">Add a to-do item</span></span>

<span data-ttu-id="dfaaa-654">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="dfaaa-655">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="dfaaa-656">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="dfaaa-657">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="dfaaa-658">Mettre à jour une tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-658">Update a to-do item</span></span>

<span data-ttu-id="dfaaa-659">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="dfaaa-660">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="dfaaa-661">Supprimer une tâche</span><span class="sxs-lookup"><span data-stu-id="dfaaa-661">Delete a to-do item</span></span>

<span data-ttu-id="dfaaa-662">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="dfaaa-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="dfaaa-663">Ajouter la prise en charge de l’authentification à une API Web</span><span class="sxs-lookup"><span data-stu-id="dfaaa-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="dfaaa-664">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dfaaa-664">Additional resources</span></span>

<span data-ttu-id="dfaaa-665">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="dfaaa-666">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="dfaaa-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="dfaaa-667">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="dfaaa-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="dfaaa-668">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="dfaaa-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
