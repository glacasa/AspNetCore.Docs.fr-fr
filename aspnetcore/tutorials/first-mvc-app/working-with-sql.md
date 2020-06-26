---
title: 'Partie 5 : travailler avec une base de données dans une application ASP.NET Core MVC'
author: rick-anderson
description: 'Partie 5 : ajout d’un modèle à une application ASP.NET Core MVC'
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: a66359ec7ac6e5a84ae36ecc2e4f5e4bb791101a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408303"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="d8056-103">Partie 5 : travailler avec une base de données dans une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d8056-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8056-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8056-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8056-105">L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d8056-106">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d8056-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d8056-108">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="d8056-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8056-109">Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8056-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-110">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="d8056-111">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="d8056-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8056-112">Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8056-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="d8056-113">Quand l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur SQL Server de production.</span><span class="sxs-lookup"><span data-stu-id="d8056-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="d8056-114">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d8056-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d8056-116">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="d8056-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d8056-117">LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="d8056-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="d8056-118">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="d8056-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d8056-119">Par défaut, la base de données LocalDB crée des fichiers *.mdf* dans le répertoire *C:/Users/{utilisateur}*.</span><span class="sxs-lookup"><span data-stu-id="d8056-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="d8056-120">Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d8056-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](working-with-sql/_static/ssox.png)

* <span data-ttu-id="d8056-122">Cliquez avec le bouton droit sur la table `Movie`**> Concepteur de vue**.</span><span class="sxs-lookup"><span data-stu-id="d8056-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](working-with-sql/_static/dv.png)

<span data-ttu-id="d8056-125">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="d8056-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d8056-126">Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="d8056-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="d8056-127">Cliquez avec le bouton droit sur la table `Movie`**> Afficher les données**</span><span class="sxs-lookup"><span data-stu-id="d8056-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/ssox2.png)

  ![Table Movie ouverte, affichant des données de table](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-130">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="d8056-131">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="d8056-131">Seed the database</span></span>

<span data-ttu-id="d8056-132">Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*.</span><span class="sxs-lookup"><span data-stu-id="d8056-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="d8056-133">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d8056-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d8056-134">Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d8056-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d8056-135">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="d8056-135">Add the seed initializer</span></span>

<span data-ttu-id="d8056-136">Remplacez le contenu de *Program.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d8056-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="d8056-137">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="d8056-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8056-139">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-139">Delete all the records in the DB.</span></span> <span data-ttu-id="d8056-140">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.</span><span class="sxs-lookup"><span data-stu-id="d8056-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="d8056-141">Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="d8056-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d8056-142">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="d8056-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d8056-143">Pour cela, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8056-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d8056-144">Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**.</span><span class="sxs-lookup"><span data-stu-id="d8056-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icône de la barre d’état système IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="d8056-147">Si vous exécutiez Visual Studio en mode non-débogage, appuyez sur F5 pour l’exécuter en mode débogage.</span><span class="sxs-lookup"><span data-stu-id="d8056-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="d8056-148">Si vous exécutiez Visual Studio en mode débogage, arrêtez le débogueur et appuyez sur F5.</span><span class="sxs-lookup"><span data-stu-id="d8056-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-149">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d8056-150">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="d8056-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d8056-151">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="d8056-152">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="d8056-152">The app shows the seeded data.</span></span>

![Application MVC Movie ouverte dans Microsoft Edge, affichant les données relatives aux films](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="d8056-154">[Précédent](adding-model.md) 
>  [Suivant](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="d8056-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8056-155">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8056-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8056-156">L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d8056-157">Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d8056-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="d8056-159">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="d8056-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8056-160">Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8056-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-161">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="d8056-162">Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="d8056-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d8056-163">Pour un développement local, il obtient la chaîne de connexion à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8056-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="d8056-164">Quand vous déployez l’application sur un serveur de test ou de production, vous pouvez utiliser une variable d’environnement ou une autre approche pour définir un serveur SQL Server réel comme chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="d8056-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="d8056-165">Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d8056-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d8056-167">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="d8056-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d8056-168">LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblée pour le développement de programmes.</span><span class="sxs-lookup"><span data-stu-id="d8056-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="d8056-169">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="d8056-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d8056-170">Par défaut, la base de données LocalDB crée des fichiers *.mdf* dans le répertoire *C:/Users/{utilisateur}*.</span><span class="sxs-lookup"><span data-stu-id="d8056-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="d8056-171">Dans le menu **Affichage**, ouvrez **l’Explorateur d’objets SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d8056-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Affichage](working-with-sql/_static/ssox.png)

* <span data-ttu-id="d8056-173">Cliquez avec le bouton droit sur la table `Movie`**> Concepteur de vue**.</span><span class="sxs-lookup"><span data-stu-id="d8056-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](working-with-sql/_static/dv.png)

<span data-ttu-id="d8056-176">Notez l’icône de clé en regard de `ID`.</span><span class="sxs-lookup"><span data-stu-id="d8056-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d8056-177">Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="d8056-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="d8056-178">Cliquez avec le bouton droit sur la table `Movie`**> Afficher les données**</span><span class="sxs-lookup"><span data-stu-id="d8056-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/ssox2.png)

  ![Table Movie ouverte, affichant des données de table](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-181">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="d8056-182">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="d8056-182">Seed the database</span></span>

<span data-ttu-id="d8056-183">Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*.</span><span class="sxs-lookup"><span data-stu-id="d8056-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="d8056-184">Remplacez le code généré par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d8056-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d8056-185">Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d8056-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d8056-186">Ajouter l’initialiseur de valeur initiale</span><span class="sxs-lookup"><span data-stu-id="d8056-186">Add the seed initializer</span></span>

<span data-ttu-id="d8056-187">Remplacez le contenu de *Program.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d8056-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="d8056-188">Tester l'application</span><span class="sxs-lookup"><span data-stu-id="d8056-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8056-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8056-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8056-190">Supprimez tous les enregistrements de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-190">Delete all the records in the DB.</span></span> <span data-ttu-id="d8056-191">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.</span><span class="sxs-lookup"><span data-stu-id="d8056-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="d8056-192">Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute.</span><span class="sxs-lookup"><span data-stu-id="d8056-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d8056-193">Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré.</span><span class="sxs-lookup"><span data-stu-id="d8056-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d8056-194">Pour cela, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8056-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d8056-195">Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**.</span><span class="sxs-lookup"><span data-stu-id="d8056-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icône de la barre d’état système IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="d8056-198">Si vous exécutiez Visual Studio en mode non-débogage, appuyez sur F5 pour l’exécuter en mode débogage.</span><span class="sxs-lookup"><span data-stu-id="d8056-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="d8056-199">Si vous exécutiez Visual Studio en mode débogage, arrêtez le débogueur et appuyez sur F5.</span><span class="sxs-lookup"><span data-stu-id="d8056-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8056-200">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="d8056-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d8056-201">Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute).</span><span class="sxs-lookup"><span data-stu-id="d8056-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d8056-202">Arrêtez et démarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8056-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="d8056-203">L’application affiche les données de départ.</span><span class="sxs-lookup"><span data-stu-id="d8056-203">The app shows the seeded data.</span></span>

![Application MVC Movie ouverte dans Microsoft Edge, affichant les données relatives aux films](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="d8056-205">[Précédent](adding-model.md) 
>  [Suivant](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="d8056-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
