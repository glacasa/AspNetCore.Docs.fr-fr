---
title: Ajouter un nouveau champ à une Razor Page dans ASP.net Core
author: rick-anderson
description: Montre comment ajouter un nouveau champ à une Razor Page avec Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 683d6718f4dcdb73c45cbcf94f6ac4f477b71bcd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769732"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="54b94-103">Ajouter un nouveau champ à une page Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54b94-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="54b94-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54b94-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="54b94-105">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="54b94-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="54b94-106">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-106">Add a new field to the model.</span></span>
* <span data-ttu-id="54b94-107">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="54b94-108">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="54b94-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="54b94-109">Ajoute une `__EFMigrationsHistory` table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="54b94-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="54b94-110">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="54b94-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="54b94-111">La vérification automatique de la synchronisation du schéma et du modèle facilite la détection des problèmes d’incohérence et de code de base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="54b94-112">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="54b94-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="54b94-113">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="54b94-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="54b94-114">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="54b94-114">Build the app.</span></span>

<span data-ttu-id="54b94-115">Modifiez *Pages/Movies/Index.cshtml*et ajoutez un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="54b94-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="54b94-116">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="54b94-116">Update the following pages:</span></span>

* <span data-ttu-id="54b94-117">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="54b94-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="54b94-118">Mettez à jour [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="54b94-119">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="54b94-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="54b94-120">L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="54b94-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="54b94-121">L’exécution de l’application sans mettre à jour la base `SqlException`de données lève une exception :</span><span class="sxs-lookup"><span data-stu-id="54b94-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="54b94-122">L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="54b94-123">(Il n’existe pas de colonne `Rating` dans la table de base de données.)</span><span class="sxs-lookup"><span data-stu-id="54b94-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="54b94-124">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="54b94-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="54b94-125">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="54b94-126">Cette approche est très utile au début du cycle de développement. Elle permet de faire évoluer rapidement le modèle et le schéma de base de données ensemble.</span><span class="sxs-lookup"><span data-stu-id="54b94-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="54b94-127">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="54b94-128">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="54b94-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="54b94-129">La suppression de la base de données lors des changements de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen efficace pour développer une application.</span><span class="sxs-lookup"><span data-stu-id="54b94-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="54b94-130">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="54b94-131">L’avantage de cette approche est que vous conservez vos données.</span><span class="sxs-lookup"><span data-stu-id="54b94-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="54b94-132">Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="54b94-133">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="54b94-134">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="54b94-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="54b94-135">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="54b94-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="54b94-136">Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque bloc `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="54b94-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="54b94-137">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="54b94-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="54b94-138">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="54b94-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54b94-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54b94-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="54b94-140">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="54b94-140">Add a migration for the rating field</span></span>

<span data-ttu-id="54b94-141">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="54b94-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="54b94-142">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="54b94-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="54b94-143">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="54b94-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="54b94-144">Comparer le modèle `Movie` au schéma de base de données `Movie`</span><span class="sxs-lookup"><span data-stu-id="54b94-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="54b94-145">Créer du code pour migrer le schéma de base de données vers le nouveau modèle</span><span class="sxs-lookup"><span data-stu-id="54b94-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="54b94-146">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="54b94-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="54b94-147">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="54b94-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="54b94-148">La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="54b94-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="54b94-149">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="54b94-150">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="54b94-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="54b94-151">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="54b94-152">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="54b94-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="54b94-153">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="54b94-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="54b94-154">Cliquez avec le bouton droit sur la base de données, puis sélectionnez *Supprimer*.</span><span class="sxs-lookup"><span data-stu-id="54b94-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="54b94-155">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="54b94-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="54b94-156">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="54b94-156">Select **OK**.</span></span>
* <span data-ttu-id="54b94-157">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="54b94-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="54b94-158">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="54b94-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="54b94-159">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="54b94-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="54b94-160">Supprimer le dossier de migration.</span><span class="sxs-lookup"><span data-stu-id="54b94-160">Delete the migration folder.</span></span>  <span data-ttu-id="54b94-161">Utilisez les commandes suivantes pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="54b94-162">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="54b94-163">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="54b94-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b94-164">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="54b94-164">Additional resources</span></span>

* [<span data-ttu-id="54b94-165">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="54b94-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="54b94-166">[Précédent : ajout de la recherche](xref:tutorials/razor-pages/search)
> [suivant : ajout](xref:tutorials/razor-pages/validation) de la validation</span><span class="sxs-lookup"><span data-stu-id="54b94-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="54b94-167">Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="54b94-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="54b94-168">Ajouter un nouveau champ au modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-168">Add a new field to the model.</span></span>
* <span data-ttu-id="54b94-169">Migrer la nouvelle modification du schéma des champs vers la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="54b94-170">Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :</span><span class="sxs-lookup"><span data-stu-id="54b94-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="54b94-171">Ajoute une table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.</span><span class="sxs-lookup"><span data-stu-id="54b94-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="54b94-172">Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.</span><span class="sxs-lookup"><span data-stu-id="54b94-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="54b94-173">La vérification automatique de la synchronisation du schéma et du modèle facilite la détection des problèmes d’incohérence et de code de base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="54b94-174">Ajout d’une propriété Rating au modèle Movie</span><span class="sxs-lookup"><span data-stu-id="54b94-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="54b94-175">Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :</span><span class="sxs-lookup"><span data-stu-id="54b94-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="54b94-176">Générez l'application.</span><span class="sxs-lookup"><span data-stu-id="54b94-176">Build the app.</span></span>

<span data-ttu-id="54b94-177">Modifiez *Pages/Movies/Index.cshtml*et ajoutez un champ `Rating` :</span><span class="sxs-lookup"><span data-stu-id="54b94-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="54b94-178">Mettez à jour les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="54b94-178">Update the following pages:</span></span>

* <span data-ttu-id="54b94-179">Ajoutez le champ `Rating` aux pages Delete et Details.</span><span class="sxs-lookup"><span data-stu-id="54b94-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="54b94-180">Mettez à jour [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="54b94-181">Ajoutez le champ `Rating` à la Page Edit.</span><span class="sxs-lookup"><span data-stu-id="54b94-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="54b94-182">L’application ne fonctionne pas tant que la base de données n’est pas mise à jour pour inclure le nouveau champ.</span><span class="sxs-lookup"><span data-stu-id="54b94-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="54b94-183">Si vous l’exécutez à présent, l’application lève une `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="54b94-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="54b94-184">Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="54b94-185">(Il n’existe pas de colonne `Rating` dans la table de base de données.)</span><span class="sxs-lookup"><span data-stu-id="54b94-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="54b94-186">Plusieurs approches sont possibles pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="54b94-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="54b94-187">Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="54b94-188">Cette approche est très utile au début du cycle de développement. Elle permet de faire évoluer rapidement le modèle et le schéma de base de données ensemble.</span><span class="sxs-lookup"><span data-stu-id="54b94-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="54b94-189">L’inconvénient est que vous perdez les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="54b94-190">N’utilisez pas cette approche sur une base de données de production !</span><span class="sxs-lookup"><span data-stu-id="54b94-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="54b94-191">La suppression de la base de données lors des changements de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen efficace pour développer une application.</span><span class="sxs-lookup"><span data-stu-id="54b94-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="54b94-192">Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle.</span><span class="sxs-lookup"><span data-stu-id="54b94-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="54b94-193">L’avantage de cette approche est que vous conservez vos données.</span><span class="sxs-lookup"><span data-stu-id="54b94-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="54b94-194">Vous pouvez apporter cette modification manuellement ou en créant un script de modification de la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="54b94-195">Utilisez les migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="54b94-196">Pour ce didacticiel, nous allons utiliser les migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="54b94-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="54b94-197">Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="54b94-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="54b94-198">Vous pouvez voir un exemple de modification ci-dessous, mais elle doit être appliquée à chaque bloc `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="54b94-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="54b94-199">Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="54b94-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="54b94-200">Générez la solution.</span><span class="sxs-lookup"><span data-stu-id="54b94-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54b94-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54b94-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="54b94-202">Ajouter une migration pour le champ d’évaluation</span><span class="sxs-lookup"><span data-stu-id="54b94-202">Add a migration for the rating field</span></span>

<span data-ttu-id="54b94-203">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="54b94-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="54b94-204">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="54b94-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="54b94-205">La commande `Add-Migration` indique au framework qu’il doit :</span><span class="sxs-lookup"><span data-stu-id="54b94-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="54b94-206">Comparer le modèle `Movie` au schéma de base de données `Movie`</span><span class="sxs-lookup"><span data-stu-id="54b94-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="54b94-207">Créer du code pour migrer le schéma de base de données vers le nouveau modèle</span><span class="sxs-lookup"><span data-stu-id="54b94-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="54b94-208">Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="54b94-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="54b94-209">Il est utile d’utiliser un nom explicite pour le fichier de migration.</span><span class="sxs-lookup"><span data-stu-id="54b94-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="54b94-210">La commande `Update-Database` demande à l’infrastructure d’appliquer les modifications de schéma à la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="54b94-211">Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="54b94-212">Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).</span><span class="sxs-lookup"><span data-stu-id="54b94-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="54b94-213">Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="54b94-214">Pour supprimer la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="54b94-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="54b94-215">Sélectionnez la base de données dans SSOX.</span><span class="sxs-lookup"><span data-stu-id="54b94-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="54b94-216">Cliquez avec le bouton droit sur la base de données, puis sélectionnez *Supprimer*.</span><span class="sxs-lookup"><span data-stu-id="54b94-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="54b94-217">Cochez **Fermer les connexions existantes**.</span><span class="sxs-lookup"><span data-stu-id="54b94-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="54b94-218">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="54b94-218">Select **OK**.</span></span>
* <span data-ttu-id="54b94-219">Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="54b94-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="54b94-220">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="54b94-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="54b94-221">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="54b94-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="54b94-222">Supprimer la base de données et utiliser des migrations pour recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="54b94-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="54b94-223">Pour supprimer la base de données, supprimez le fichier de base de données (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="54b94-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="54b94-224">Ensuite, exécutez la commande `ef database update` :</span><span class="sxs-lookup"><span data-stu-id="54b94-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="54b94-225">Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`.</span><span class="sxs-lookup"><span data-stu-id="54b94-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="54b94-226">Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="54b94-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b94-227">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="54b94-227">Additional resources</span></span>

* [<span data-ttu-id="54b94-228">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="54b94-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="54b94-229">[Précédent : ajout de la recherche](xref:tutorials/razor-pages/search)
> [suivant : ajout](xref:tutorials/razor-pages/validation) de la validation</span><span class="sxs-lookup"><span data-stu-id="54b94-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
