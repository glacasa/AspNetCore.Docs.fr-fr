---
title: Créer une API web avec ASP.NET Core et MongoDB
author: prkhandelwal
description: Ce tutoriel montre comment créer une API web ASP.NET Core à l’aide d’une base de données NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 4d1c2d915c646dd1c8fcadd25bcd420a0a749dc9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774767"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="911bc-103">Créer une API web avec ASP.NET Core et MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="911bc-104">Par [Pratik Khandelwal](https://twitter.com/K2Prk) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="911bc-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="911bc-105">Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="911bc-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="911bc-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="911bc-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="911bc-107">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-107">Configure MongoDB</span></span>
> * <span data-ttu-id="911bc-108">Créer une base de données MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="911bc-109">Définir une collection et un schéma MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="911bc-110">Effectuer des opérations CRUD MongoDB à partir d’une API web</span><span class="sxs-lookup"><span data-stu-id="911bc-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="911bc-111">Personnaliser la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="911bc-111">Customize JSON serialization</span></span>

<span data-ttu-id="911bc-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="911bc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="911bc-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="911bc-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="911bc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="911bc-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="911bc-115">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="911bc-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="911bc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="911bc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="911bc-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="911bc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="911bc-119">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="911bc-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="911bc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="911bc-121">C# pour Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="911bc-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="911bc-123">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="911bc-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="911bc-124">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="911bc-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="911bc-125">Visual Studio pour Mac 7.7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="911bc-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="911bc-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="911bc-127">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-127">Configure MongoDB</span></span>

<span data-ttu-id="911bc-128">Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="911bc-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="911bc-129">Ajoutez *C :\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* à la `Path` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="911bc-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="911bc-130">Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="911bc-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="911bc-131">Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents.</span><span class="sxs-lookup"><span data-stu-id="911bc-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="911bc-132">Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="911bc-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="911bc-133">Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données.</span><span class="sxs-lookup"><span data-stu-id="911bc-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="911bc-134">Par exemple, *C:\\BooksData* sous Windows.</span><span class="sxs-lookup"><span data-stu-id="911bc-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="911bc-135">Le cas échéant, créez le répertoire.</span><span class="sxs-lookup"><span data-stu-id="911bc-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="911bc-136">L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.</span><span class="sxs-lookup"><span data-stu-id="911bc-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="911bc-137">Ouvrez un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="911bc-137">Open a command shell.</span></span> <span data-ttu-id="911bc-138">Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017.</span><span class="sxs-lookup"><span data-stu-id="911bc-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="911bc-139">N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="911bc-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="911bc-140">Ouvrez une autre instance de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="911bc-140">Open another command shell instance.</span></span> <span data-ttu-id="911bc-141">Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="911bc-142">Utilisez la ligne suivante dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="911bc-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="911bc-143">Le cas échéant, une base de données nommée *BookstoreDb* est créée.</span><span class="sxs-lookup"><span data-stu-id="911bc-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="911bc-144">Si la base de données existe déjà, sa connexion est ouverte pour les transactions.</span><span class="sxs-lookup"><span data-stu-id="911bc-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="911bc-145">Créez une collection `Books` à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="911bc-146">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="911bc-147">Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="911bc-148">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="911bc-149">Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.</span><span class="sxs-lookup"><span data-stu-id="911bc-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="911bc-150">Affichez les documents de la base de données à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="911bc-151">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="911bc-152">Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.</span><span class="sxs-lookup"><span data-stu-id="911bc-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="911bc-153">La base de données est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="911bc-153">The database is ready.</span></span> <span data-ttu-id="911bc-154">Vous pouvez commencer à créer l’API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="911bc-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="911bc-155">Créer le projet d’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="911bc-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="911bc-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="911bc-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="911bc-157">Accédez à **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="911bc-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="911bc-158">Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-159">Nommez le projet *BooksApi*, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-160">Sélectionnez le framework cible **.NET Core** et **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="911bc-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="911bc-161">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-162">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="911bc-163">Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="911bc-164">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="911bc-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="911bc-166">Exécutez les commandes suivantes dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="911bc-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="911bc-167">Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="911bc-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="911bc-168">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « BooksApi ». Ajoutez-les ?**.</span><span class="sxs-lookup"><span data-stu-id="911bc-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="911bc-169">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="911bc-169">Select **Yes**.</span></span>
1. <span data-ttu-id="911bc-170">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="911bc-171">Ouvrez **Terminal intégré** et accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="911bc-172">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="911bc-173">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="911bc-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="911bc-174">Accédez à **Fichier** > **Nouvelle solution** > **.NET Core** > **Application**.</span><span class="sxs-lookup"><span data-stu-id="911bc-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="911bc-175">Sélectionnez le modèle de projet C# **API web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-176">Sélectionnez **.NET Core 3.0** dans la liste déroulante **Framework cible**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-177">Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-178">Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="911bc-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="911bc-179">Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="911bc-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="911bc-180">Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.</span><span class="sxs-lookup"><span data-stu-id="911bc-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="911bc-181">Ajouter un modèle d’entité</span><span class="sxs-lookup"><span data-stu-id="911bc-181">Add an entity model</span></span>

1. <span data-ttu-id="911bc-182">Ajoutez un répertoire *Models* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="911bc-183">Ajoutez une classe `Book` au répertoire *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="911bc-184">Dans la classe précédente, la propriété `Id` :</span><span class="sxs-lookup"><span data-stu-id="911bc-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="911bc-185">Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="911bc-186">Est annoté avec [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété comme clé primaire du document.</span><span class="sxs-lookup"><span data-stu-id="911bc-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="911bc-187">Est annoté avec [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour permettre la transmission du paramètre en tant `string` que type au lieu d’une structure [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="911bc-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="911bc-188">Mongo gère la conversion de `string` en `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="911bc-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="911bc-189">La `BookName` propriété est annotée avec [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) l’attribut.</span><span class="sxs-lookup"><span data-stu-id="911bc-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="911bc-190">La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="911bc-191">Ajouter un modèle de configuration</span><span class="sxs-lookup"><span data-stu-id="911bc-191">Add a configuration model</span></span>

1. <span data-ttu-id="911bc-192">Ajoutez les valeurs de configuration de base de données suivantes à *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="911bc-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="911bc-193">Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="911bc-194">La classe `BookstoreDatabaseSettings` précédente est utilisée pour stocker les valeurs de propriété `BookstoreDatabaseSettings` du fichier \* appsettings.json\*.</span><span class="sxs-lookup"><span data-stu-id="911bc-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="911bc-195">Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.</span><span class="sxs-lookup"><span data-stu-id="911bc-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="911bc-196">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="911bc-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="911bc-197">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="911bc-197">In the preceding code:</span></span>

   * <span data-ttu-id="911bc-198">L’instance de configuration à laquelle la section `BookstoreDatabaseSettings` du fichier *appsettings.json* est liée est inscrite dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="911bc-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="911bc-199">Par exemple, la propriété `ConnectionString` d’un objet `BookstoreDatabaseSettings` est peuplée avec la propriété `BookstoreDatabaseSettings:ConnectionString` dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="911bc-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="911bc-200">L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton.</span><span class="sxs-lookup"><span data-stu-id="911bc-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="911bc-201">Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="911bc-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="911bc-202">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="911bc-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="911bc-203">Ajouter un service d’opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="911bc-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="911bc-204">Ajoutez un répertoire *Services* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="911bc-205">Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="911bc-206">Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="911bc-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="911bc-207">Cette technique permet d’accéder aux valeurs de configuration d’*appsettings.json*, qui ont été ajoutées à la section [Ajouter un modèle de configuration](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="911bc-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="911bc-208">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="911bc-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="911bc-209">Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices.</span><span class="sxs-lookup"><span data-stu-id="911bc-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="911bc-210">La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="911bc-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="911bc-211">Selon les [instructions de réutilisation du client Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)officielles, `MongoClient` doit être inscrit dans di avec une durée de vie de service Singleton.</span><span class="sxs-lookup"><span data-stu-id="911bc-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="911bc-212">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :</span><span class="sxs-lookup"><span data-stu-id="911bc-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="911bc-213">La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="911bc-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="911bc-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Lit l’instance de serveur qui permet d’effectuer des opérations de base de données.</span><span class="sxs-lookup"><span data-stu-id="911bc-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="911bc-215">Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="911bc-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Représente la base de données Mongo qui permet d’effectuer des opérations.</span><span class="sxs-lookup"><span data-stu-id="911bc-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="911bc-217">Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique.</span><span class="sxs-lookup"><span data-stu-id="911bc-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="911bc-218">Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="911bc-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="911bc-219">Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :</span><span class="sxs-lookup"><span data-stu-id="911bc-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="911bc-220">`collection` représente le nom de la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="911bc-221">`TDocument` représente le type d’objet CLR stocké dans la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="911bc-222">`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="911bc-223">Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :</span><span class="sxs-lookup"><span data-stu-id="911bc-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="911bc-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Supprime un seul document correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="911bc-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="911bc-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Retourne tous les documents de la collection correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="911bc-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="911bc-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Insère l’objet fourni en tant que nouveau document dans la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="911bc-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Remplace le seul document correspondant aux critères de recherche fournis par l’objet indiqué.</span><span class="sxs-lookup"><span data-stu-id="911bc-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="911bc-228">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="911bc-228">Add a controller</span></span>

<span data-ttu-id="911bc-229">Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="911bc-230">Le contrôleur d’API web précédent :</span><span class="sxs-lookup"><span data-stu-id="911bc-230">The preceding web API controller:</span></span>

* <span data-ttu-id="911bc-231">Utilise la classe `BookService` pour effectuer des opérations CRUD.</span><span class="sxs-lookup"><span data-stu-id="911bc-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="911bc-232">Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="911bc-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="911bc-233">Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="911bc-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="911bc-234">Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="911bc-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="911bc-235">`CreatedAtRoute` ajoute également un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="911bc-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="911bc-236">L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="911bc-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="911bc-237">Tester l’API web</span><span class="sxs-lookup"><span data-stu-id="911bc-237">Test the web API</span></span>

1. <span data-ttu-id="911bc-238">Générez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="911bc-238">Build and run the app.</span></span>

1. <span data-ttu-id="911bc-239">Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="911bc-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="911bc-240">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="911bc-240">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="911bc-241">Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="911bc-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="911bc-242">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="911bc-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="911bc-243">Configurer les options de sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="911bc-243">Configure JSON serialization options</span></span>

<span data-ttu-id="911bc-244">Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="911bc-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="911bc-245">Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="911bc-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="911bc-246">La propriété `bookName` doit être retournée sous la forme `Name`.</span><span class="sxs-lookup"><span data-stu-id="911bc-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="911bc-247">Pour satisfaire les exigences précédentes, apportez les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="911bc-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="911bc-248">JSON.NET a été supprimé du framework partagé ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="911bc-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="911bc-249">Ajoutez une référence de package à [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="911bc-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="911bc-250">Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddControllers` :</span><span class="sxs-lookup"><span data-stu-id="911bc-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="911bc-251">À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="911bc-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="911bc-252">Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.</span><span class="sxs-lookup"><span data-stu-id="911bc-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="911bc-253">Dans *Models/Book. cs*, annotez la `BookName` propriété avec [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) l’attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="911bc-254">La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.</span><span class="sxs-lookup"><span data-stu-id="911bc-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="911bc-255">Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :</span><span class="sxs-lookup"><span data-stu-id="911bc-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="911bc-256">Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="911bc-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="911bc-257">Notez la différence des noms de propriétés JSON.</span><span class="sxs-lookup"><span data-stu-id="911bc-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="911bc-258">Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="911bc-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="911bc-259">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="911bc-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="911bc-260">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-260">Configure MongoDB</span></span>
> * <span data-ttu-id="911bc-261">Créer une base de données MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="911bc-262">Définir une collection et un schéma MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="911bc-263">Effectuer des opérations CRUD MongoDB à partir d’une API web</span><span class="sxs-lookup"><span data-stu-id="911bc-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="911bc-264">Personnaliser la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="911bc-264">Customize JSON serialization</span></span>

<span data-ttu-id="911bc-265">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="911bc-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="911bc-266">Prérequis</span><span class="sxs-lookup"><span data-stu-id="911bc-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="911bc-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="911bc-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="911bc-268">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="911bc-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="911bc-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="911bc-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="911bc-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="911bc-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="911bc-272">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="911bc-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="911bc-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="911bc-274">C# pour Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="911bc-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="911bc-276">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="911bc-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="911bc-277">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="911bc-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="911bc-278">Visual Studio pour Mac 7.7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="911bc-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="911bc-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="911bc-280">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="911bc-280">Configure MongoDB</span></span>

<span data-ttu-id="911bc-281">Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="911bc-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="911bc-282">Ajoutez *C :\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* à la `Path` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="911bc-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="911bc-283">Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="911bc-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="911bc-284">Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents.</span><span class="sxs-lookup"><span data-stu-id="911bc-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="911bc-285">Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="911bc-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="911bc-286">Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données.</span><span class="sxs-lookup"><span data-stu-id="911bc-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="911bc-287">Par exemple, *C:\\BooksData* sous Windows.</span><span class="sxs-lookup"><span data-stu-id="911bc-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="911bc-288">Le cas échéant, créez le répertoire.</span><span class="sxs-lookup"><span data-stu-id="911bc-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="911bc-289">L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.</span><span class="sxs-lookup"><span data-stu-id="911bc-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="911bc-290">Ouvrez un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="911bc-290">Open a command shell.</span></span> <span data-ttu-id="911bc-291">Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017.</span><span class="sxs-lookup"><span data-stu-id="911bc-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="911bc-292">N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="911bc-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="911bc-293">Ouvrez une autre instance de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="911bc-293">Open another command shell instance.</span></span> <span data-ttu-id="911bc-294">Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="911bc-295">Utilisez la ligne suivante dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="911bc-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="911bc-296">Le cas échéant, une base de données nommée *BookstoreDb* est créée.</span><span class="sxs-lookup"><span data-stu-id="911bc-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="911bc-297">Si la base de données existe déjà, sa connexion est ouverte pour les transactions.</span><span class="sxs-lookup"><span data-stu-id="911bc-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="911bc-298">Créez une collection `Books` à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="911bc-299">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="911bc-300">Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="911bc-301">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-301">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="911bc-302">Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.</span><span class="sxs-lookup"><span data-stu-id="911bc-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="911bc-303">Affichez les documents de la base de données à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="911bc-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="911bc-304">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="911bc-304">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="911bc-305">Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.</span><span class="sxs-lookup"><span data-stu-id="911bc-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="911bc-306">La base de données est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="911bc-306">The database is ready.</span></span> <span data-ttu-id="911bc-307">Vous pouvez commencer à créer l’API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="911bc-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="911bc-308">Créer le projet d’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="911bc-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="911bc-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="911bc-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="911bc-310">Accédez à **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="911bc-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="911bc-311">Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-312">Nommez le projet *BooksApi*, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-313">Sélectionnez la version cible de .NET Framework **.NET Core** et **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="911bc-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="911bc-314">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-315">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="911bc-316">Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="911bc-317">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="911bc-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="911bc-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="911bc-319">Exécutez les commandes suivantes dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="911bc-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="911bc-320">Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="911bc-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="911bc-321">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « BooksApi ». Ajoutez-les ?**.</span><span class="sxs-lookup"><span data-stu-id="911bc-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="911bc-322">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="911bc-322">Select **Yes**.</span></span>
1. <span data-ttu-id="911bc-323">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="911bc-324">Ouvrez **Terminal intégré** et accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="911bc-325">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="911bc-326">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="911bc-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="911bc-327">Accédez à **Fichier** > **Nouvelle solution** > **.NET Core** > **Application**.</span><span class="sxs-lookup"><span data-stu-id="911bc-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="911bc-328">Sélectionnez le modèle de projet C# **API web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-329">Sélectionnez **.NET Core 2.2** dans la liste déroulante **Framework cible**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="911bc-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="911bc-330">Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="911bc-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="911bc-331">Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="911bc-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="911bc-332">Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="911bc-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="911bc-333">Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.</span><span class="sxs-lookup"><span data-stu-id="911bc-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="911bc-334">Ajouter un modèle d’entité</span><span class="sxs-lookup"><span data-stu-id="911bc-334">Add an entity model</span></span>

1. <span data-ttu-id="911bc-335">Ajoutez un répertoire *Models* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="911bc-336">Ajoutez une classe `Book` au répertoire *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="911bc-337">Dans la classe précédente, la propriété `Id` :</span><span class="sxs-lookup"><span data-stu-id="911bc-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="911bc-338">Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="911bc-339">Est annoté avec [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété comme clé primaire du document.</span><span class="sxs-lookup"><span data-stu-id="911bc-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="911bc-340">Est annoté avec [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour permettre la transmission du paramètre en tant `string` que type au lieu d’une structure [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="911bc-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="911bc-341">Mongo gère la conversion de `string` en `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="911bc-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="911bc-342">La `BookName` propriété est annotée avec [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) l’attribut.</span><span class="sxs-lookup"><span data-stu-id="911bc-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="911bc-343">La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="911bc-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="911bc-344">Ajouter un modèle de configuration</span><span class="sxs-lookup"><span data-stu-id="911bc-344">Add a configuration model</span></span>

1. <span data-ttu-id="911bc-345">Ajoutez les valeurs de configuration de base de données suivantes à *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="911bc-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="911bc-346">Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="911bc-347">La classe `BookstoreDatabaseSettings` précédente est utilisée pour stocker les valeurs de propriété `BookstoreDatabaseSettings` du fichier \* appsettings.json\*.</span><span class="sxs-lookup"><span data-stu-id="911bc-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="911bc-348">Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.</span><span class="sxs-lookup"><span data-stu-id="911bc-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="911bc-349">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="911bc-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="911bc-350">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="911bc-350">In the preceding code:</span></span>

   * <span data-ttu-id="911bc-351">L’instance de configuration à laquelle la section `BookstoreDatabaseSettings` du fichier *appsettings.json* est liée est inscrite dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="911bc-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="911bc-352">Par exemple, la propriété `ConnectionString` d’un objet `BookstoreDatabaseSettings` est peuplée avec la propriété `BookstoreDatabaseSettings:ConnectionString` dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="911bc-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="911bc-353">L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton.</span><span class="sxs-lookup"><span data-stu-id="911bc-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="911bc-354">Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="911bc-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="911bc-355">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="911bc-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="911bc-356">Ajouter un service d’opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="911bc-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="911bc-357">Ajoutez un répertoire *Services* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="911bc-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="911bc-358">Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="911bc-359">Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="911bc-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="911bc-360">Cette technique permet d’accéder aux valeurs de configuration d’*appsettings.json*, qui ont été ajoutées à la section [Ajouter un modèle de configuration](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="911bc-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="911bc-361">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="911bc-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="911bc-362">Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices.</span><span class="sxs-lookup"><span data-stu-id="911bc-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="911bc-363">La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="911bc-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="911bc-364">Selon les [instructions de réutilisation du client Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)officielles, `MongoClient` doit être inscrit dans di avec une durée de vie de service Singleton.</span><span class="sxs-lookup"><span data-stu-id="911bc-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="911bc-365">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :</span><span class="sxs-lookup"><span data-stu-id="911bc-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="911bc-366">La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="911bc-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="911bc-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Lit l’instance de serveur qui permet d’effectuer des opérations de base de données.</span><span class="sxs-lookup"><span data-stu-id="911bc-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="911bc-368">Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :</span><span class="sxs-lookup"><span data-stu-id="911bc-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="911bc-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Représente la base de données Mongo qui permet d’effectuer des opérations.</span><span class="sxs-lookup"><span data-stu-id="911bc-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="911bc-370">Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique.</span><span class="sxs-lookup"><span data-stu-id="911bc-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="911bc-371">Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="911bc-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="911bc-372">Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :</span><span class="sxs-lookup"><span data-stu-id="911bc-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="911bc-373">`collection` représente le nom de la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="911bc-374">`TDocument` représente le type d’objet CLR stocké dans la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="911bc-375">`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="911bc-376">Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :</span><span class="sxs-lookup"><span data-stu-id="911bc-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="911bc-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Supprime un seul document correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="911bc-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="911bc-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Retourne tous les documents de la collection correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="911bc-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="911bc-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Insère l’objet fourni en tant que nouveau document dans la collection.</span><span class="sxs-lookup"><span data-stu-id="911bc-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="911bc-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Remplace le seul document correspondant aux critères de recherche fournis par l’objet indiqué.</span><span class="sxs-lookup"><span data-stu-id="911bc-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="911bc-381">Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="911bc-381">Add a controller</span></span>

<span data-ttu-id="911bc-382">Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="911bc-383">Le contrôleur d’API web précédent :</span><span class="sxs-lookup"><span data-stu-id="911bc-383">The preceding web API controller:</span></span>

* <span data-ttu-id="911bc-384">Utilise la classe `BookService` pour effectuer des opérations CRUD.</span><span class="sxs-lookup"><span data-stu-id="911bc-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="911bc-385">Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="911bc-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="911bc-386">Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="911bc-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="911bc-387">Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="911bc-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="911bc-388">`CreatedAtRoute` ajoute également un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="911bc-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="911bc-389">L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="911bc-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="911bc-390">Tester l’API web</span><span class="sxs-lookup"><span data-stu-id="911bc-390">Test the web API</span></span>

1. <span data-ttu-id="911bc-391">Générez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="911bc-391">Build and run the app.</span></span>

1. <span data-ttu-id="911bc-392">Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="911bc-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="911bc-393">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="911bc-393">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="911bc-394">Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="911bc-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="911bc-395">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="911bc-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="911bc-396">Configurer les options de sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="911bc-396">Configure JSON serialization options</span></span>

<span data-ttu-id="911bc-397">Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="911bc-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="911bc-398">Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="911bc-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="911bc-399">La propriété `bookName` doit être retournée sous la forme `Name`.</span><span class="sxs-lookup"><span data-stu-id="911bc-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="911bc-400">Pour satisfaire les exigences précédentes, apportez les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="911bc-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="911bc-401">Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddMvc` :</span><span class="sxs-lookup"><span data-stu-id="911bc-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="911bc-402">À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="911bc-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="911bc-403">Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.</span><span class="sxs-lookup"><span data-stu-id="911bc-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="911bc-404">Dans *Models/Book. cs*, annotez la `BookName` propriété avec [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) l’attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="911bc-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="911bc-405">La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.</span><span class="sxs-lookup"><span data-stu-id="911bc-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="911bc-406">Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :</span><span class="sxs-lookup"><span data-stu-id="911bc-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="911bc-407">Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="911bc-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="911bc-408">Notez la différence des noms de propriétés JSON.</span><span class="sxs-lookup"><span data-stu-id="911bc-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="911bc-409">Ajouter la prise en charge de l’authentification à une API Web</span><span class="sxs-lookup"><span data-stu-id="911bc-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="911bc-410">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="911bc-410">Next steps</span></span>

<span data-ttu-id="911bc-411">Pour plus d’informations sur la création d’API web ASP.NET Core, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="911bc-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="911bc-412">Version YouTube de cet article</span><span class="sxs-lookup"><span data-stu-id="911bc-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
