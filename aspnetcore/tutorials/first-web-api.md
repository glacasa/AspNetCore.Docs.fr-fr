---
title: 'Didacticiel : créer une API Web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212623"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="caaeb-103">Didacticiel : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="caaeb-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="caaeb-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="caaeb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="caaeb-105">Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="caaeb-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="caaeb-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="caaeb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="caaeb-107">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-107">Create a web API project.</span></span>
> * <span data-ttu-id="caaeb-108">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="caaeb-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="caaeb-109">Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.</span><span class="sxs-lookup"><span data-stu-id="caaeb-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="caaeb-110">Configurer le routage, les chemins d’URL et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="caaeb-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="caaeb-111">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="caaeb-111">Call the web API with Postman.</span></span>

<span data-ttu-id="caaeb-112">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="caaeb-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="caaeb-113">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="caaeb-113">Overview</span></span>

<span data-ttu-id="caaeb-114">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="caaeb-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="caaeb-115">API</span><span class="sxs-lookup"><span data-stu-id="caaeb-115">API</span></span> | <span data-ttu-id="caaeb-116">Description</span><span class="sxs-lookup"><span data-stu-id="caaeb-116">Description</span></span> | <span data-ttu-id="caaeb-117">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="caaeb-117">Request body</span></span> | <span data-ttu-id="caaeb-118">Response body</span><span class="sxs-lookup"><span data-stu-id="caaeb-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="caaeb-119">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="caaeb-119">Get all to-do items</span></span> | <span data-ttu-id="caaeb-120">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-120">None</span></span> | <span data-ttu-id="caaeb-121">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="caaeb-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="caaeb-122">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="caaeb-122">Get an item by ID</span></span> | <span data-ttu-id="caaeb-123">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-123">None</span></span> | <span data-ttu-id="caaeb-124">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="caaeb-125">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="caaeb-125">Add a new item</span></span> | <span data-ttu-id="caaeb-126">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-126">To-do item</span></span> | <span data-ttu-id="caaeb-127">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="caaeb-128">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="caaeb-129">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-129">To-do item</span></span> | <span data-ttu-id="caaeb-130">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-130">None</span></span> |
|<span data-ttu-id="caaeb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="caaeb-132">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="caaeb-133">Aucune</span><span class="sxs-lookup"><span data-stu-id="caaeb-133">None</span></span> | <span data-ttu-id="caaeb-134">Aucune</span><span class="sxs-lookup"><span data-stu-id="caaeb-134">None</span></span>|

<span data-ttu-id="caaeb-135">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-135">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="caaeb-141">Prérequis</span><span class="sxs-lookup"><span data-stu-id="caaeb-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-144">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="caaeb-145">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="caaeb-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-147">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="caaeb-148">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="caaeb-149">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="caaeb-150">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="caaeb-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="caaeb-151">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-151">Select the **API** template and click **Create**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="caaeb-154">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="caaeb-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="caaeb-155">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="caaeb-156">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="caaeb-157">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="caaeb-158">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-158">The preceding commands:</span></span>

  * <span data-ttu-id="caaeb-159">Créent un projet API Web et l’ouvrent dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="caaeb-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="caaeb-160">Ajoutent les packages NuGet qui sont requis dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="caaeb-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-161">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="caaeb-162">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-162">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="caaeb-164">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="caaeb-165">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API**application console  >  **suivant** .</span><span class="sxs-lookup"><span data-stu-id="caaeb-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="caaeb-167">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="caaeb-168">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-168">Select **Next**.</span></span>

* <span data-ttu-id="caaeb-169">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="caaeb-171">Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="caaeb-172">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="caaeb-172">Test the API</span></span>

<span data-ttu-id="caaeb-173">Le modèle de projet crée une API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="caaeb-174">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="caaeb-176">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="caaeb-177">Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="caaeb-178">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="caaeb-179">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="caaeb-181">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="caaeb-182">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-183">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="caaeb-184">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="caaeb-185">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="caaeb-186">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="caaeb-187">Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="caaeb-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="caaeb-188">Un code JSON similaire au suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="caaeb-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="caaeb-189">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="caaeb-189">Add a model class</span></span>

<span data-ttu-id="caaeb-190">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="caaeb-191">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="caaeb-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-193">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="caaeb-194">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="caaeb-195">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="caaeb-196">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="caaeb-197">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="caaeb-198">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="caaeb-200">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="caaeb-201">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-202">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="caaeb-203">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-203">Right-click the project.</span></span> <span data-ttu-id="caaeb-204">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="caaeb-205">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-205">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="caaeb-207">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="caaeb-208">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="caaeb-209">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="caaeb-210">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="caaeb-211">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="caaeb-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="caaeb-212">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="caaeb-212">Add a database context</span></span>

<span data-ttu-id="caaeb-213">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="caaeb-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="caaeb-214">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="caaeb-216">Ajouter Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="caaeb-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="caaeb-217">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="caaeb-218">Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="caaeb-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="caaeb-219">Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="caaeb-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="caaeb-220">Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="caaeb-221">Utilisez les instructions précédentes pour ajouter le package NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="caaeb-223">Ajouter le contexte de base de données TodoContext</span><span class="sxs-lookup"><span data-stu-id="caaeb-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="caaeb-224">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="caaeb-225">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-226">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="caaeb-227">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="caaeb-228">Entrez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="caaeb-229">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="caaeb-229">Register the database context</span></span>

<span data-ttu-id="caaeb-230">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="caaeb-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="caaeb-231">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="caaeb-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="caaeb-232">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="caaeb-233">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="caaeb-233">The preceding code:</span></span>

* <span data-ttu-id="caaeb-234">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="caaeb-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="caaeb-235">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="caaeb-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="caaeb-236">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="caaeb-237">Générer automatiquement des modèles pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="caaeb-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-239">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="caaeb-240">Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="caaeb-241">Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="caaeb-242">Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="caaeb-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="caaeb-243">Sélectionnez **TodoItem (TodoApi. Models)** dans la **classe de modèle**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="caaeb-244">Sélectionnez **TodoContext (TodoApi. Models)** dans la **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="caaeb-245">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-246">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="caaeb-247">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="caaeb-248">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-248">The preceding commands:</span></span>

* <span data-ttu-id="caaeb-249">Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="caaeb-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="caaeb-250">Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="caaeb-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="caaeb-251">Génèrent automatiquement des modèles pour `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="caaeb-252">Le code généré :</span><span class="sxs-lookup"><span data-stu-id="caaeb-252">The generated code:</span></span>

* <span data-ttu-id="caaeb-253">Marque la classe avec l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="caaeb-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="caaeb-254">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="caaeb-255">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="caaeb-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="caaeb-256">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="caaeb-257">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="caaeb-258">Les modèles de ASP.NET Core pour :</span><span class="sxs-lookup"><span data-stu-id="caaeb-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="caaeb-259">Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="caaeb-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="caaeb-260">Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="caaeb-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="caaeb-261">Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="caaeb-262">Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.</span><span class="sxs-lookup"><span data-stu-id="caaeb-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="caaeb-263">Examiner la méthode de création de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="caaeb-264">Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="caaeb-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="caaeb-265">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="caaeb-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="caaeb-266">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="caaeb-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="caaeb-267">La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :</span><span class="sxs-lookup"><span data-stu-id="caaeb-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="caaeb-268">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="caaeb-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="caaeb-269">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="caaeb-270">Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="caaeb-271">L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="caaeb-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="caaeb-272">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="caaeb-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="caaeb-273">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="caaeb-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="caaeb-274">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="caaeb-275">Installer Postman</span><span class="sxs-lookup"><span data-stu-id="caaeb-275">Install Postman</span></span>

<span data-ttu-id="caaeb-276">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="caaeb-277">Installer le [poste](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="caaeb-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="caaeb-278">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-278">Start the web app.</span></span>
* <span data-ttu-id="caaeb-279">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="caaeb-279">Start Postman.</span></span>
* <span data-ttu-id="caaeb-280">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="caaeb-281">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="caaeb-282">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="caaeb-283">Tester PostTodoItem avec Postman</span><span class="sxs-lookup"><span data-stu-id="caaeb-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="caaeb-284">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="caaeb-284">Create a new request.</span></span>
* <span data-ttu-id="caaeb-285">Affectez `POST` à la méthode HTTP.</span><span class="sxs-lookup"><span data-stu-id="caaeb-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="caaeb-286">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="caaeb-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="caaeb-287">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="caaeb-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="caaeb-288">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="caaeb-289">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="caaeb-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="caaeb-290">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-290">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="caaeb-292">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="caaeb-292">Test the location header URI</span></span>

* <span data-ttu-id="caaeb-293">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="caaeb-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="caaeb-294">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="caaeb-294">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="caaeb-296">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="caaeb-296">Set the method to GET.</span></span>
* <span data-ttu-id="caaeb-297">Collez l’URI (par exemple, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="caaeb-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="caaeb-298">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="caaeb-299">Examiner les méthodes GET</span><span class="sxs-lookup"><span data-stu-id="caaeb-299">Examine the GET methods</span></span>

<span data-ttu-id="caaeb-300">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="caaeb-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="caaeb-301">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman.</span><span class="sxs-lookup"><span data-stu-id="caaeb-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="caaeb-302">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="caaeb-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="caaeb-303">Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="caaeb-304">Tester Get avec Postman</span><span class="sxs-lookup"><span data-stu-id="caaeb-304">Test Get with Postman</span></span>

* <span data-ttu-id="caaeb-305">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="caaeb-305">Create a new request.</span></span>
* <span data-ttu-id="caaeb-306">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="caaeb-307">Définissez l’URL de la requête sur `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="caaeb-308">Par exemple, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="caaeb-309">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="caaeb-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="caaeb-310">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-310">Select **Send**.</span></span>

<span data-ttu-id="caaeb-311">Cette application utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-311">This app uses an in-memory database.</span></span> <span data-ttu-id="caaeb-312">Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="caaeb-313">Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="caaeb-314">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="caaeb-314">Routing and URL paths</span></span>

<span data-ttu-id="caaeb-315">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="caaeb-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="caaeb-316">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="caaeb-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="caaeb-317">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="caaeb-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="caaeb-318">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="caaeb-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="caaeb-319">Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems**Controller, le nom du contrôleur est « TodoItems ».</span><span class="sxs-lookup"><span data-stu-id="caaeb-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="caaeb-320">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="caaeb-321">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="caaeb-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="caaeb-322">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-322">This sample doesn't use a template.</span></span> <span data-ttu-id="caaeb-323">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="caaeb-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="caaeb-324">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="caaeb-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="caaeb-325">Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.</span><span class="sxs-lookup"><span data-stu-id="caaeb-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="caaeb-326">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="caaeb-326">Return values</span></span>

<span data-ttu-id="caaeb-327">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="caaeb-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="caaeb-328">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="caaeb-329">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="caaeb-330">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="caaeb-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="caaeb-331">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="caaeb-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="caaeb-332">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="caaeb-333">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="caaeb-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="caaeb-334">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="caaeb-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="caaeb-335">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="caaeb-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="caaeb-336">Méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-336">The PutTodoItem method</span></span>

<span data-ttu-id="caaeb-337">Examinez la méthode `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="caaeb-338">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="caaeb-339">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="caaeb-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="caaeb-340">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="caaeb-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="caaeb-341">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="caaeb-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="caaeb-342">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="caaeb-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="caaeb-343">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="caaeb-344">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="caaeb-345">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="caaeb-346">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="caaeb-347">Mettez à jour la tâche dont l’ID = 1 et nommez-la « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="caaeb-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="caaeb-348">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="caaeb-348">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="caaeb-350">Méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="caaeb-351">Examinez la méthode `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="caaeb-352">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="caaeb-353">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="caaeb-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="caaeb-354">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="caaeb-355">Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="caaeb-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="caaeb-356">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="caaeb-357">Empêcher la post-validation</span><span class="sxs-lookup"><span data-stu-id="caaeb-357">Prevent over-posting</span></span>

<span data-ttu-id="caaeb-358">Actuellement, l’exemple d’application expose l' `TodoItem` objet entier.</span><span class="sxs-lookup"><span data-stu-id="caaeb-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="caaeb-359">En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="caaeb-360">Il existe plusieurs raisons à cela, et la sécurité est essentielle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="caaeb-361">Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="caaeb-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="caaeb-362">Le **DTO** est utilisé dans cet article.</span><span class="sxs-lookup"><span data-stu-id="caaeb-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="caaeb-363">Un DTO peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="caaeb-363">A DTO may be used to:</span></span>

* <span data-ttu-id="caaeb-364">Empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="caaeb-364">Prevent over-posting.</span></span>
* <span data-ttu-id="caaeb-365">Masquer les propriétés que les clients ne sont pas censés afficher.</span><span class="sxs-lookup"><span data-stu-id="caaeb-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="caaeb-366">Omettez certaines propriétés afin de réduire la taille de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="caaeb-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="caaeb-367">Aplatir les graphiques d’objets qui contiennent des objets imbriqués.</span><span class="sxs-lookup"><span data-stu-id="caaeb-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="caaeb-368">Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.</span><span class="sxs-lookup"><span data-stu-id="caaeb-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="caaeb-369">Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :</span><span class="sxs-lookup"><span data-stu-id="caaeb-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="caaeb-370">Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.</span><span class="sxs-lookup"><span data-stu-id="caaeb-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="caaeb-371">Vérifiez que vous pouvez poster et recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="caaeb-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="caaeb-372">Créer un modèle DTO :</span><span class="sxs-lookup"><span data-stu-id="caaeb-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="caaeb-373">Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="caaeb-374">Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.</span><span class="sxs-lookup"><span data-stu-id="caaeb-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="caaeb-375">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="caaeb-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="caaeb-376">Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="caaeb-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="caaeb-377">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="caaeb-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="caaeb-378">Créer un projet d’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-378">Create a web API project.</span></span>
> * <span data-ttu-id="caaeb-379">Ajouter une classe de modèle et un contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="caaeb-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="caaeb-380">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="caaeb-380">Add a controller.</span></span>
> * <span data-ttu-id="caaeb-381">Ajouter les méthodes CRUD</span><span class="sxs-lookup"><span data-stu-id="caaeb-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="caaeb-382">Configurer le routage et les chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="caaeb-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="caaeb-383">Spécifier des valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="caaeb-383">Specify return values.</span></span>
> * <span data-ttu-id="caaeb-384">Appeler l’API web avec Postman</span><span class="sxs-lookup"><span data-stu-id="caaeb-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="caaeb-385">Appeler l’API web avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="caaeb-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="caaeb-386">À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="caaeb-387">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="caaeb-387">Overview</span></span>

<span data-ttu-id="caaeb-388">Ce didacticiel crée l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="caaeb-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="caaeb-389">API</span><span class="sxs-lookup"><span data-stu-id="caaeb-389">API</span></span> | <span data-ttu-id="caaeb-390">Description</span><span class="sxs-lookup"><span data-stu-id="caaeb-390">Description</span></span> | <span data-ttu-id="caaeb-391">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="caaeb-391">Request body</span></span> | <span data-ttu-id="caaeb-392">Response body</span><span class="sxs-lookup"><span data-stu-id="caaeb-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="caaeb-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="caaeb-393">GET /api/TodoItems</span></span> | <span data-ttu-id="caaeb-394">Obtenir toutes les tâches</span><span class="sxs-lookup"><span data-stu-id="caaeb-394">Get all to-do items</span></span> | <span data-ttu-id="caaeb-395">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-395">None</span></span> | <span data-ttu-id="caaeb-396">Tableau de tâches</span><span class="sxs-lookup"><span data-stu-id="caaeb-396">Array of to-do items</span></span>|
|<span data-ttu-id="caaeb-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="caaeb-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="caaeb-398">Obtenir un élément par ID</span><span class="sxs-lookup"><span data-stu-id="caaeb-398">Get an item by ID</span></span> | <span data-ttu-id="caaeb-399">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-399">None</span></span> | <span data-ttu-id="caaeb-400">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-400">To-do item</span></span>|
|<span data-ttu-id="caaeb-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="caaeb-401">POST /api/TodoItems</span></span> | <span data-ttu-id="caaeb-402">Ajouter un nouvel élément</span><span class="sxs-lookup"><span data-stu-id="caaeb-402">Add a new item</span></span> | <span data-ttu-id="caaeb-403">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-403">To-do item</span></span> | <span data-ttu-id="caaeb-404">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-404">To-do item</span></span> |
|<span data-ttu-id="caaeb-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="caaeb-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="caaeb-406">Mettre à jour un élément existant &nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="caaeb-407">Tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-407">To-do item</span></span> | <span data-ttu-id="caaeb-408">Aucun</span><span class="sxs-lookup"><span data-stu-id="caaeb-408">None</span></span> |
|<span data-ttu-id="caaeb-409">SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="caaeb-410">Supprimer un élément &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="caaeb-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="caaeb-411">Aucune</span><span class="sxs-lookup"><span data-stu-id="caaeb-411">None</span></span> | <span data-ttu-id="caaeb-412">Aucune</span><span class="sxs-lookup"><span data-stu-id="caaeb-412">None</span></span>|

<span data-ttu-id="caaeb-413">Le diagramme suivant illustre la conception de l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-413">The following diagram shows the design of the app.</span></span>

![Le client est représenté par une zone située à gauche.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="caaeb-419">Prérequis</span><span class="sxs-lookup"><span data-stu-id="caaeb-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-422">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="caaeb-423">Créer un projet web</span><span class="sxs-lookup"><span data-stu-id="caaeb-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-425">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="caaeb-426">Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="caaeb-427">Nommez le projet *TodoApi* et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="caaeb-428">Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="caaeb-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="caaeb-429">Sélectionnez le modèle **API** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="caaeb-430">Ne sélectionnez **pas\*\*\*\*Activer la prise en charge de Docker**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-430">**Don't** select **Enable Docker Support**.</span></span>

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="caaeb-433">Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="caaeb-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="caaeb-434">Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="caaeb-435">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="caaeb-436">Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="caaeb-437">Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-438">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="caaeb-439">Sélectionnez **Fichier** > **Nouvelle solution**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-439">Select **File** > **New Solution**.</span></span>

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="caaeb-441">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="caaeb-442">Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API**application console  >  **suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="caaeb-443">Dans la boîte de dialogue **configurer votre nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="caaeb-444">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-444">Select **Next**.</span></span>

* <span data-ttu-id="caaeb-445">Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="caaeb-447">Tester l’API</span><span class="sxs-lookup"><span data-stu-id="caaeb-447">Test the API</span></span>

<span data-ttu-id="caaeb-448">Le modèle de projet crée une API `values`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-448">The project template creates a `values` API.</span></span> <span data-ttu-id="caaeb-449">Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="caaeb-451">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="caaeb-452">Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="caaeb-453">Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="caaeb-454">Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="caaeb-456">Appuyez sur Ctrl+F5 pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="caaeb-457">Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-458">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="caaeb-459">Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="caaeb-460">Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="caaeb-461">Une erreur HTTP 404 (introuvable) est retournée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="caaeb-462">Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="caaeb-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="caaeb-463">Le code JSON suivant est retourné :</span><span class="sxs-lookup"><span data-stu-id="caaeb-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="caaeb-464">Ajouter une classe de modèle</span><span class="sxs-lookup"><span data-stu-id="caaeb-464">Add a model class</span></span>

<span data-ttu-id="caaeb-465">Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application.</span><span class="sxs-lookup"><span data-stu-id="caaeb-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="caaeb-466">Le modèle pour cette application est une classe `TodoItem` unique.</span><span class="sxs-lookup"><span data-stu-id="caaeb-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-468">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="caaeb-469">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="caaeb-470">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="caaeb-471">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="caaeb-472">Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="caaeb-473">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="caaeb-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="caaeb-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="caaeb-475">Ajoutez un dossier nommé *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="caaeb-476">Ajoutez une classe `TodoItem` au dossier *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-477">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="caaeb-478">Cliquez avec le bouton droit sur le projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-478">Right-click the project.</span></span> <span data-ttu-id="caaeb-479">Sélectionnez **Ajouter**  >  **un nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="caaeb-480">Nommez le dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-480">Name the folder *Models*.</span></span>

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="caaeb-482">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Nouveau fichier** > **Général** > **Classe vide**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="caaeb-483">Nommez la classe *TodoItem* et cliquez sur **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="caaeb-484">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="caaeb-485">La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="caaeb-486">Vous pouvez placer des classes de modèle n’importe où dans le projet, mais le dossier *Models* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="caaeb-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="caaeb-487">Ajouter un contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="caaeb-487">Add a database context</span></span>

<span data-ttu-id="caaeb-488">Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données.</span><span class="sxs-lookup"><span data-stu-id="caaeb-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="caaeb-489">Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-491">Cliquez avec le bouton droit sur le dossier *Models* et sélectionnez **Ajouter** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="caaeb-492">Nommez la classe *TodoContext* et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-493">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="caaeb-494">Ajoutez une classe `TodoContext` au dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="caaeb-495">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="caaeb-496">Inscrire le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="caaeb-496">Register the database context</span></span>

<span data-ttu-id="caaeb-497">Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="caaeb-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="caaeb-498">Le conteneur fournit le service aux contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="caaeb-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="caaeb-499">Mettez à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="caaeb-500">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="caaeb-500">The preceding code:</span></span>

* <span data-ttu-id="caaeb-501">Supprime les déclarations `using` inutilisées.</span><span class="sxs-lookup"><span data-stu-id="caaeb-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="caaeb-502">Ajoute le contexte de base de données au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="caaeb-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="caaeb-503">Spécifie que le contexte de base de données utilise une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="caaeb-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="caaeb-504">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="caaeb-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-506">Cliquez avec le bouton droit sur le dossier *Contrôleurs*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="caaeb-507">Sélectionnez **Ajouter** > **Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="caaeb-508">Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="caaeb-509">Nommez la classe *TodoController* et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-511">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="caaeb-512">Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="caaeb-513">Remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="caaeb-514">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="caaeb-514">The preceding code:</span></span>

* <span data-ttu-id="caaeb-515">Définit une classe de contrôleur d’API sans méthodes.</span><span class="sxs-lookup"><span data-stu-id="caaeb-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="caaeb-516">Marque la classe avec l' [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="caaeb-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="caaeb-517">Cet attribut indique que le contrôleur répond aux requêtes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="caaeb-518">Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="caaeb-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="caaeb-519">Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="caaeb-520">Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="caaeb-521">Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide.</span><span class="sxs-lookup"><span data-stu-id="caaeb-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="caaeb-522">Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe.</span><span class="sxs-lookup"><span data-stu-id="caaeb-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="caaeb-523">Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API.</span><span class="sxs-lookup"><span data-stu-id="caaeb-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="caaeb-524">Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.</span><span class="sxs-lookup"><span data-stu-id="caaeb-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="caaeb-525">Ajouter des méthodes Get</span><span class="sxs-lookup"><span data-stu-id="caaeb-525">Add Get methods</span></span>

<span data-ttu-id="caaeb-526">Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="caaeb-527">Ces méthodes implémentent deux points de terminaison GET :</span><span class="sxs-lookup"><span data-stu-id="caaeb-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="caaeb-528">Arrêtez l’application si elle est toujours en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="caaeb-528">Stop the app if it's still running.</span></span> <span data-ttu-id="caaeb-529">Ensuite, réexécutez-la pour inclure les dernières modifications.</span><span class="sxs-lookup"><span data-stu-id="caaeb-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="caaeb-530">Testez l’application en appelant les deux points de terminaison à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="caaeb-531">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="caaeb-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="caaeb-532">La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="caaeb-533">Routage et chemins d’URL</span><span class="sxs-lookup"><span data-stu-id="caaeb-533">Routing and URL paths</span></span>

<span data-ttu-id="caaeb-534">L' [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribut désigne une méthode qui répond à une requête http.</span><span class="sxs-lookup"><span data-stu-id="caaeb-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="caaeb-535">Le chemin d’URL pour chaque méthode est construit comme suit :</span><span class="sxs-lookup"><span data-stu-id="caaeb-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="caaeb-536">Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="caaeb-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="caaeb-537">Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ».</span><span class="sxs-lookup"><span data-stu-id="caaeb-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="caaeb-538">Pour cet exemple, le nom de classe du contrôleur étant **Todo**Controller, le nom du contrôleur est « todo ».</span><span class="sxs-lookup"><span data-stu-id="caaeb-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="caaeb-539">Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="caaeb-540">Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin.</span><span class="sxs-lookup"><span data-stu-id="caaeb-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="caaeb-541">Cet exemple n’utilise pas de modèle.</span><span class="sxs-lookup"><span data-stu-id="caaeb-541">This sample doesn't use a template.</span></span> <span data-ttu-id="caaeb-542">Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="caaeb-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="caaeb-543">Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche.</span><span class="sxs-lookup"><span data-stu-id="caaeb-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="caaeb-544">Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="caaeb-545">Valeurs de retour</span><span class="sxs-lookup"><span data-stu-id="caaeb-545">Return values</span></span>

<span data-ttu-id="caaeb-546">Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="caaeb-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="caaeb-547">ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="caaeb-548">Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="caaeb-549">Les exceptions non gérées sont converties en erreurs 5xx.</span><span class="sxs-lookup"><span data-stu-id="caaeb-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="caaeb-550">Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP.</span><span class="sxs-lookup"><span data-stu-id="caaeb-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="caaeb-551">Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="caaeb-552">Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur 404 [introuvable](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="caaeb-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="caaeb-553">Sinon, la méthode retourne 200 avec un corps de réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="caaeb-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="caaeb-554">Le retour de `item` entraîne une réponse HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="caaeb-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="caaeb-555">Tester la méthode GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="caaeb-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="caaeb-556">Ce tutoriel utilise Postman pour tester l’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="caaeb-557">Installez le [poste de publication](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="caaeb-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="caaeb-558">Démarrez l’application web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-558">Start the web app.</span></span>
* <span data-ttu-id="caaeb-559">Démarrez Postman.</span><span class="sxs-lookup"><span data-stu-id="caaeb-559">Start Postman.</span></span>
* <span data-ttu-id="caaeb-560">Désactivez la **vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="caaeb-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="caaeb-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="caaeb-562">À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="caaeb-563">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="caaeb-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="caaeb-564">Dans **Postman**  >  **Préférences** de publication (onglet**général** ), désactivez la vérification de **certificat SSL**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="caaeb-565">Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.</span><span class="sxs-lookup"><span data-stu-id="caaeb-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="caaeb-566">Réactivez la vérification du certificat SSL après avoir testé le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="caaeb-567">Créez une requête.</span><span class="sxs-lookup"><span data-stu-id="caaeb-567">Create a new request.</span></span>
  * <span data-ttu-id="caaeb-568">Définissez la méthode HTTP sur **GET**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="caaeb-569">Définissez l’URL de la requête sur `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="caaeb-570">Par exemple, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="caaeb-571">Définissez l’**affichage à deux volets** dans Postman.</span><span class="sxs-lookup"><span data-stu-id="caaeb-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="caaeb-572">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-572">Select **Send**.</span></span>

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="caaeb-574">Ajouter une méthode Create</span><span class="sxs-lookup"><span data-stu-id="caaeb-574">Add a Create method</span></span>

<span data-ttu-id="caaeb-575">Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* :</span><span class="sxs-lookup"><span data-stu-id="caaeb-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="caaeb-576">Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="caaeb-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="caaeb-577">La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="caaeb-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="caaeb-578">La méthode `CreatedAtAction` :</span><span class="sxs-lookup"><span data-stu-id="caaeb-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="caaeb-579">Retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="caaeb-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="caaeb-580">HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="caaeb-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="caaeb-581">Ajoute un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="caaeb-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="caaeb-582">L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="caaeb-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="caaeb-583">Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="caaeb-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="caaeb-584">Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="caaeb-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="caaeb-585">Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="caaeb-586">Tester la méthode PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="caaeb-587">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-587">Build the project.</span></span>
* <span data-ttu-id="caaeb-588">Dans Postman, définissez la méthode HTTP sur `POST`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="caaeb-589">Sélectionnez l’onglet **Corps** .</span><span class="sxs-lookup"><span data-stu-id="caaeb-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="caaeb-590">Sélectionnez la case d’option **raw** (données brutes).</span><span class="sxs-lookup"><span data-stu-id="caaeb-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="caaeb-591">Définissez le type sur **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="caaeb-592">Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :</span><span class="sxs-lookup"><span data-stu-id="caaeb-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="caaeb-593">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-593">Select **Send**.</span></span>

  ![Postman avec requête de création](first-web-api/_static/create.png)

  <span data-ttu-id="caaeb-595">Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="caaeb-596">Tester l’URI de l’en-tête d’emplacement</span><span class="sxs-lookup"><span data-stu-id="caaeb-596">Test the location header URI</span></span>

* <span data-ttu-id="caaeb-597">Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).</span><span class="sxs-lookup"><span data-stu-id="caaeb-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="caaeb-598">Copiez la valeur d’en-tête **Location** (Emplacement) :</span><span class="sxs-lookup"><span data-stu-id="caaeb-598">Copy the **Location** header value:</span></span>

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="caaeb-600">Définissez la méthode sur GET.</span><span class="sxs-lookup"><span data-stu-id="caaeb-600">Set the method to GET.</span></span>
* <span data-ttu-id="caaeb-601">Collez l’URI (par exemple, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="caaeb-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="caaeb-602">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="caaeb-603">Ajouter une méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="caaeb-604">Ajoutez la méthode `PutTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="caaeb-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="caaeb-605">`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="caaeb-606">La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="caaeb-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="caaeb-607">D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements.</span><span class="sxs-lookup"><span data-stu-id="caaeb-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="caaeb-608">Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="caaeb-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="caaeb-609">Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.</span><span class="sxs-lookup"><span data-stu-id="caaeb-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="caaeb-610">Tester la méthode PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="caaeb-611">Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="caaeb-612">La base de données doit contenir un élément avant que vous ne passiez un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="caaeb-613">Appelez GET pour vérifier qu’un élément existe dans la base de données avant d’effectuer un appel PUT.</span><span class="sxs-lookup"><span data-stu-id="caaeb-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="caaeb-614">Mettez à jour la tâche dont l’id est 1 en définissant son nom sur « feed fish » :</span><span class="sxs-lookup"><span data-stu-id="caaeb-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="caaeb-615">L’image suivante montre la mise à jour Postman :</span><span class="sxs-lookup"><span data-stu-id="caaeb-615">The following image shows the Postman update:</span></span>

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="caaeb-617">Ajouter une méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="caaeb-618">Ajoutez la méthode `DeleteTodoItem` suivante :</span><span class="sxs-lookup"><span data-stu-id="caaeb-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="caaeb-619">La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="caaeb-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="caaeb-620">Tester la méthode DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="caaeb-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="caaeb-621">Utilisez Postman pour supprimer une tâche :</span><span class="sxs-lookup"><span data-stu-id="caaeb-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="caaeb-622">Définissez la méthode sur `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="caaeb-623">Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="caaeb-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="caaeb-624">Sélectionnez **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="caaeb-624">Select **Send**.</span></span>

<span data-ttu-id="caaeb-625">L’exemple d’application vous permet de supprimer tous les éléments.</span><span class="sxs-lookup"><span data-stu-id="caaeb-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="caaeb-626">Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="caaeb-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="caaeb-627">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="caaeb-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="caaeb-628">Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="caaeb-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="caaeb-629">jQuery lance la demande.</span><span class="sxs-lookup"><span data-stu-id="caaeb-629">jQuery initiates the request.</span></span> <span data-ttu-id="caaeb-630">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="caaeb-631">Configurez l’application pour [traiter les fichiers statiques](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [activer le mappage de fichier par défaut](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="caaeb-632">Créez un dossier *wwwroot* dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="caaeb-633">Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="caaeb-634">Remplacez son contenu par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="caaeb-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="caaeb-635">Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="caaeb-636">Remplacez son contenu par le code ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="caaeb-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="caaeb-637">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="caaeb-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="caaeb-638">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="caaeb-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="caaeb-639">Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="caaeb-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="caaeb-640">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="caaeb-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="caaeb-641">Les explications suivantes traitent des appels à l’API.</span><span class="sxs-lookup"><span data-stu-id="caaeb-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="caaeb-642">Obtenir une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="caaeb-642">Get a list of to-do items</span></span>

<span data-ttu-id="caaeb-643">jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="caaeb-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="caaeb-644">La fonction de rappel `success` est appelée si la requête réussit.</span><span class="sxs-lookup"><span data-stu-id="caaeb-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="caaeb-645">Dans le rappel, le DOM est mis à jour avec les informations des tâches.</span><span class="sxs-lookup"><span data-stu-id="caaeb-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="caaeb-646">Ajouter une tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-646">Add a to-do item</span></span>

<span data-ttu-id="caaeb-647">jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="caaeb-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="caaeb-648">Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="caaeb-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="caaeb-649">La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="caaeb-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="caaeb-650">Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="caaeb-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="caaeb-651">Mettre à jour une tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-651">Update a to-do item</span></span>

<span data-ttu-id="caaeb-652">La mise à jour d’une tâche est similaire à l’ajout d’une tâche.</span><span class="sxs-lookup"><span data-stu-id="caaeb-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="caaeb-653">L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.</span><span class="sxs-lookup"><span data-stu-id="caaeb-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="caaeb-654">Supprimer une tâche</span><span class="sxs-lookup"><span data-stu-id="caaeb-654">Delete a to-do item</span></span>

<span data-ttu-id="caaeb-655">Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="caaeb-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="caaeb-656">Ajouter la prise en charge de l’authentification à une API Web</span><span class="sxs-lookup"><span data-stu-id="caaeb-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="caaeb-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="caaeb-657">Server4.md)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="caaeb-658">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="caaeb-658">Additional resources</span></span>

<span data-ttu-id="caaeb-659">[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="caaeb-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="caaeb-660">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="caaeb-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="caaeb-661">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="caaeb-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="caaeb-662">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="caaeb-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
