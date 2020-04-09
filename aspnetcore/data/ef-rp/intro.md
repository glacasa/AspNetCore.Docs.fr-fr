---
title: Pages Razor avec Entity Framework Core dans ASP.NET Core - Tutoriel 1 sur 8
author: rick-anderson
description: Montre comment créer une application Pages Razor à l’aide d’Entity Framework Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656820"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="dd3d6-103">Pages Razor avec Entity Framework Core dans ASP.NET Core - Tutoriel 1 sur 8</span><span class="sxs-lookup"><span data-stu-id="dd3d6-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="dd3d6-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dd3d6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dd3d6-105">Il s’agit du premier d’une série de tutoriels qui montrent comment utiliser Entity Framework (EF) Core dans une [application ASP.NET Core Razor Pages.](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="dd3d6-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="dd3d6-106">Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="dd3d6-107">Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="dd3d6-108">Le tutoriel utilise le code première approche.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="dd3d6-109">Pour plus d’informations sur la suite de ce tutoriel en utilisant la première approche de base de données, voir [ce problème Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="dd3d6-110">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="dd3d6-111">[Télécharger les instructions](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dd3d6-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dd3d6-112">Prerequisites</span></span>

* <span data-ttu-id="dd3d6-113">Si vous débutez avec Razor Pages, parcourez le tutoriel [Bien démarrer avec Razor Pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celui-ci.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="dd3d6-116">Moteurs de base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-116">Database engines</span></span>

<span data-ttu-id="dd3d6-117">Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="dd3d6-118">Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="dd3d6-119">Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="dd3d6-120">Dépannage</span><span class="sxs-lookup"><span data-stu-id="dd3d6-120">Troubleshooting</span></span>

<span data-ttu-id="dd3d6-121">Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="dd3d6-122">Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="dd3d6-123">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="dd3d6-123">The sample app</span></span>

<span data-ttu-id="dd3d6-124">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="dd3d6-125">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="dd3d6-126">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-126">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

<span data-ttu-id="dd3d6-129">Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="dd3d6-130">Le tutoriel traite essentiellement de l’utilisation d’EF Core, et non de la façon de personnaliser l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="dd3d6-131">Suivez le lien en haut de la page pour obtenir le code source du projet terminé.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="dd3d6-132">Le dossier *cu30* contient le code de la version 3.0 d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="dd3d6-133">Les fichiers qui reflètent l’état du code pour les tutoriels 1-7 se trouvent dans le dossier *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd3d6-135">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="dd3d6-136">Supprimez trois fichiers et un dossier dont le nom contient *SQLite*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="dd3d6-137">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-137">Build the project.</span></span>
* <span data-ttu-id="dd3d6-138">Dans la console du Gestionnaire de package (PMC), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="dd3d6-139">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd3d6-141">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="dd3d6-142">Supprimez *ContosoUniversity.csproj*, puis renommez *ContosoUniversitySQLite.csproj* en *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="dd3d6-143">Supprimez *Startup.cs* et renommez *StartupSQLite.cs* en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="dd3d6-144">Supprimez *appSettings.json* et renommez *appSettingsSQLite.json* en *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="dd3d6-145">Supprimez le dossier *Migrations* et renommez *MigrationsSQL* en *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="dd3d6-146">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-146">Build the project.</span></span>
* <span data-ttu-id="dd3d6-147">Dans une invite de commandes, exécutez la commande suivante dans le dossier du projet :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="dd3d6-148">Dans votre outil SQLite, exécutez l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="dd3d6-149">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="dd3d6-150">Créer le projet d’application web</span><span class="sxs-lookup"><span data-stu-id="dd3d6-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-152">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dd3d6-153">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="dd3d6-154">Nommez le projet *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="dd3d6-155">Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="dd3d6-156">Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les listes déroulantes, puis **Application web**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd3d6-158">Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="dd3d6-159">Exécutez les commandes suivantes pour créer un projet Razor Pages et `cd` dans le nouveau dossier de projet :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="dd3d6-160">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="dd3d6-160">Set up the site style</span></span>

<span data-ttu-id="dd3d6-161">Configurez l’en-tête, le pied de page et le menu du site en mettant à jour *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="dd3d6-162">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="dd3d6-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="dd3d6-163">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-163">There are three occurrences.</span></span>

* <span data-ttu-id="dd3d6-164">Supprimez les entrées de menu **Home** et **Privacy** et ajoutez les entrées **About**, **Students**, **Courses**, **Instructors** et **Departments**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="dd3d6-165">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="dd3d6-166">Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant de façon à remplacer le texte relatif à ASP.NET Core par le texte se rapportant à cette application :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="dd3d6-167">Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="dd3d6-168">Modèle de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-168">The data model</span></span>

<span data-ttu-id="dd3d6-169">Les sections suivantes créent un modèle de données :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-169">The following sections create a data model:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="dd3d6-171">Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="dd3d6-172">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="dd3d6-172">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* <span data-ttu-id="dd3d6-174">Créez un dossier *Models* dans le dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="dd3d6-175">Créez *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="dd3d6-176">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="dd3d6-177">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="dd3d6-178">L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="dd3d6-179">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="dd3d6-180">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="dd3d6-181">Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="dd3d6-182">Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="dd3d6-183">Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="dd3d6-184">Par exemple, supposez qu’une ligne Student présente un ID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="dd3d6-185">Les lignes Enrollment associées auront un StudentID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="dd3d6-186">StudentID est une *clé étrangère* dans la table Enrollment.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="dd3d6-187">La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="dd3d6-188">Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="dd3d6-189">Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="dd3d6-190">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="dd3d6-190">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="dd3d6-192">Créez *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="dd3d6-193">La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="dd3d6-194">Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="dd3d6-195">Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="dd3d6-196">L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="dd3d6-197">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="dd3d6-198">La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="dd3d6-199">Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="dd3d6-200">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="dd3d6-201">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="dd3d6-202">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="dd3d6-203">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="dd3d6-204">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="dd3d6-205">Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="dd3d6-206">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="dd3d6-207">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="dd3d6-208">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="dd3d6-208">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="dd3d6-210">Créez *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="dd3d6-211">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="dd3d6-212">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="dd3d6-213">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="dd3d6-214">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="dd3d6-215">Générer automatiquement des modèles de pages Student</span><span class="sxs-lookup"><span data-stu-id="dd3d6-215">Scaffold Student pages</span></span>

<span data-ttu-id="dd3d6-216">Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="dd3d6-217">Une classe de *contexte* EF Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-217">An EF Core *context* class.</span></span> <span data-ttu-id="dd3d6-218">Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="dd3d6-219">Il dérive de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="dd3d6-220">Les pages Razor qui gèrent les opérations Create, Read, Update et Delete (CRUD) pour l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-222">Créez un dossier *Students* dans le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="dd3d6-223">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students*, puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="dd3d6-224">Dans le dialogue **Ajouter l’échafaudage,** sélectionnez **les pages Razor à l’aide de l’entité Cadre (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="dd3d6-225">Dans la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)**  :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="dd3d6-226">Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="dd3d6-227">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="dd3d6-228">Remplacez le nom du contexte de données *ContosoUniversity.Models.ContosoUniversityContext* par *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="dd3d6-229">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-229">Select **Add**.</span></span>

<span data-ttu-id="dd3d6-230">Les packages suivants sont automatiquement installés :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd3d6-232">Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="dd3d6-233">Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="dd3d6-234">Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="dd3d6-235">Créez un dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="dd3d6-236">Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="dd3d6-237">Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="dd3d6-238">**Sur Windows**</span><span class="sxs-lookup"><span data-stu-id="dd3d6-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="dd3d6-239">**Sur macOS ou Linux**</span><span class="sxs-lookup"><span data-stu-id="dd3d6-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="dd3d6-240">Si vous rencontrez un problème à l’étape précédente, générez le projet et recommencez l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="dd3d6-241">Le processus de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-241">The scaffolding process:</span></span>

* <span data-ttu-id="dd3d6-242">Crée les pages Razor suivantes dans le dossier *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="dd3d6-243">*Create.cshtml* et *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="dd3d6-244">*Delete.cshtml* et *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="dd3d6-245">*Details.cshtml* et *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="dd3d6-246">*Edit.cshtml* et *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="dd3d6-247">*Index.cshtml* et *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="dd3d6-248">Crée *Data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="dd3d6-249">Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="dd3d6-250">Ajoute une chaîne de connexion de base de données à *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="dd3d6-251">Chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd3d6-253">La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="dd3d6-254">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="dd3d6-255">Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd3d6-257">Modifiez la chaîne de connexion de sorte qu’elle pointe vers un fichier de base de données SQLite nommé *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="dd3d6-258">Mettre à jour la classe du contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-258">Update the database context class</span></span>

<span data-ttu-id="dd3d6-259">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="dd3d6-260">Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dd3d6-261">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="dd3d6-262">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="dd3d6-263">Mettez à jour *SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="dd3d6-264">Le code en surbrillance crée une propriété [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="dd3d6-265">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-265">In EF Core terminology:</span></span>

* <span data-ttu-id="dd3d6-266">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="dd3d6-267">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dd3d6-268">Comme un jeu d’entités contient plusieurs entités, les propriétés DBSet doivent être des noms au pluriel.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="dd3d6-269">Comme l’outil de génération de modèles automatique a créé un DBSet `Student`, cette étape le remplace par le pluriel `Students`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="dd3d6-270">Pour que le code Razor Pages corresponde au nouveau nom DBSet, apportez une modification globale à l’ensemble du projet en remplaçant `_context.Student` par `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="dd3d6-271">Il y a 8 occurrences.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-271">There are 8 occurrences.</span></span>

<span data-ttu-id="dd3d6-272">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="dd3d6-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="dd3d6-273">Startup.cs</span></span>

<span data-ttu-id="dd3d6-274">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dd3d6-275">Certains services (comme le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dd3d6-276">Ces services sont affectés aux composants qui les nécessitent (par exemple les Pages Razor) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dd3d6-277">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dd3d6-278">L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-280">Dans `ConfigureServices`, les lignes en surbrillance ont été ajoutées par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd3d6-282">Dans `ConfigureServices`, vérifiez que le code ajouté par l’outil de génération de modèles automatique appelle `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="dd3d6-283">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dd3d6-284">Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="dd3d6-285">Création de la base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-285">Create the database</span></span>

<span data-ttu-id="dd3d6-286">Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="dd3d6-287">La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="dd3d6-288">S’il n’existe pas de base de données, elle crée la base de données et le schéma.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="dd3d6-289">`EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="dd3d6-290">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-290">Delete the database.</span></span> <span data-ttu-id="dd3d6-291">Toutes les données existantes sont perdues.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-291">Any existing data is lost.</span></span>
* <span data-ttu-id="dd3d6-292">Modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-292">Change the data model.</span></span> <span data-ttu-id="dd3d6-293">Par exemple, ajoutez un champ `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="dd3d6-294">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-294">Run the app.</span></span>
* <span data-ttu-id="dd3d6-295">`EnsureCreated` crée une base de données avec le nouveau schéma.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="dd3d6-296">Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="dd3d6-297">La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="dd3d6-298">Dans ce cas, procédez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="dd3d6-299">Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="dd3d6-300">Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="dd3d6-301">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="dd3d6-301">Test the app</span></span>

* <span data-ttu-id="dd3d6-302">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-302">Run the app.</span></span>
* <span data-ttu-id="dd3d6-303">Sélectionnez le lien **Students**, puis **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="dd3d6-304">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="dd3d6-305">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-305">Seed the database</span></span>

<span data-ttu-id="dd3d6-306">La méthode `EnsureCreated` crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="dd3d6-307">Cette section ajoute du code qui remplit la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="dd3d6-308">Créez *Data/DbInitializer.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="dd3d6-309">Le code vérifie si des étudiants figurent dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="dd3d6-310">S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="dd3d6-311">Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="dd3d6-312">Dans *Program.cs*, remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd3d6-314">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd3d6-316">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="dd3d6-317">Redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-317">Restart the app.</span></span>

* <span data-ttu-id="dd3d6-318">Sélectionnez la page Students pour examiner les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="dd3d6-319">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-321">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="dd3d6-322">Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="dd3d6-323">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="dd3d6-324">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="dd3d6-325">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="dd3d6-326">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd3d6-328">Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="dd3d6-329">Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="dd3d6-330">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="dd3d6-330">Asynchronous code</span></span>

<span data-ttu-id="dd3d6-331">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="dd3d6-332">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="dd3d6-333">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="dd3d6-334">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="dd3d6-335">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="dd3d6-336">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="dd3d6-337">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="dd3d6-338">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="dd3d6-339">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="dd3d6-340">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="dd3d6-341">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="dd3d6-342">Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="dd3d6-343">Le type de retour `Task<T>` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="dd3d6-344">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="dd3d6-345">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="dd3d6-346">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="dd3d6-347">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="dd3d6-348">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="dd3d6-349">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="dd3d6-350">Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="dd3d6-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="dd3d6-351">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="dd3d6-352">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="dd3d6-353">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="dd3d6-354">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="dd3d6-355">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="dd3d6-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dd3d6-356">Tutoriel suivant</span><span class="sxs-lookup"><span data-stu-id="dd3d6-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd3d6-357">L’exemple d’application web Contoso University montre comment créer une application web Razor Pages ASP.NET Core à l’aide d’Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="dd3d6-358">L’exemple d’application est un site web pour une université Contoso fictive.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="dd3d6-359">Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="dd3d6-360">Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="dd3d6-361">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="dd3d6-362">[Télécharger les instructions](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dd3d6-363">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dd3d6-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="dd3d6-366">Connaissance des [Pages Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="dd3d6-367">Les programmeurs débutants doivent lire [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) avant de démarrer cette série.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="dd3d6-368">Dépannage</span><span class="sxs-lookup"><span data-stu-id="dd3d6-368">Troubleshooting</span></span>

<span data-ttu-id="dd3d6-369">Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="dd3d6-370">Un bon moyen d’obtenir de l’aide est de publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="dd3d6-371">L’application web Contoso University</span><span class="sxs-lookup"><span data-stu-id="dd3d6-371">The Contoso University web app</span></span>

<span data-ttu-id="dd3d6-372">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="dd3d6-373">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="dd3d6-374">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-374">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

<span data-ttu-id="dd3d6-377">Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="dd3d6-378">Le didacticiel est axé sur Core EF avec des pages Razor, et non sur l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="dd3d6-379">Créer l’application web Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="dd3d6-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-381">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dd3d6-382">Créez une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="dd3d6-383">Nommez le projet **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="dd3d6-384">Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="dd3d6-385">Sélectionnez **ASP.NET Core 2.1** dans la liste déroulante, puis sélectionnez **Application web**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="dd3d6-386">Pour les images des étapes précédentes, consultez [Créer une application web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="dd3d6-387">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="dd3d6-389">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="dd3d6-389">Set up the site style</span></span>

<span data-ttu-id="dd3d6-390">Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="dd3d6-391">Mettez à jour *Pages/Shared/_Layout.cshtml* avec les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="dd3d6-392">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="dd3d6-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="dd3d6-393">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-393">There are three occurrences.</span></span>

* <span data-ttu-id="dd3d6-394">Ajoutez des entrées de menu pour **Students**, **Courses**, **Instructors** et **Departments**, et supprimez l’entrée de menu **Contact**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="dd3d6-395">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-395">The changes are highlighted.</span></span> <span data-ttu-id="dd3d6-396">(Tout le balisage n’est *pas* affiché.)</span><span class="sxs-lookup"><span data-stu-id="dd3d6-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="dd3d6-397">Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="dd3d6-398">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-398">Create the data model</span></span>

<span data-ttu-id="dd3d6-399">Créez des classes d’entités pour l’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="dd3d6-400">Commencez par les trois entités suivantes :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-400">Start with the following three entities:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="dd3d6-402">Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="dd3d6-403">Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="dd3d6-404">Un étudiant peut s’inscrire à autant de cours qu’il le souhaite.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="dd3d6-405">Un cours peut avoir une quantité illimitée d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="dd3d6-406">Dans les sections suivantes, une classe pour chacune de ces entités est créée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="dd3d6-407">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="dd3d6-407">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

<span data-ttu-id="dd3d6-409">Créez un dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-409">Create a *Models* folder.</span></span> <span data-ttu-id="dd3d6-410">Dans le dossier *Models*, créez un fichier de classe nommé *Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="dd3d6-411">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="dd3d6-412">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="dd3d6-413">Dans `classnameID`, `classname` est le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="dd3d6-414">L’autre clé primaire reconnue automatiquement est `StudentID` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="dd3d6-415">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="dd3d6-416">Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="dd3d6-417">Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="dd3d6-418">Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="dd3d6-419">Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="dd3d6-420">Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="dd3d6-421">La table `Enrollment` a deux lignes avec `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="dd3d6-422">`StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="dd3d6-423">Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="dd3d6-424">Vous pouvez spécifier `ICollection<T>`, ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="dd3d6-425">Quand vous utilisez `ICollection<T>`, EF Core crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="dd3d6-426">Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="dd3d6-427">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="dd3d6-427">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="dd3d6-429">Dans le dossier *Models*, créez un fichier *Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="dd3d6-430">La propriété `EnrollmentID` est la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="dd3d6-431">Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="dd3d6-432">En général, les développeurs choisissent un modèle et l’utilisent dans tout le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="dd3d6-433">Dans un prochain didacticiel, nous verrons qu’utiliser ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="dd3d6-434">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="dd3d6-435">Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="dd3d6-436">Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="dd3d6-437">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="dd3d6-438">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="dd3d6-439">L’entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="dd3d6-440">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="dd3d6-441">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="dd3d6-442">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="dd3d6-443">Par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="dd3d6-444">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="dd3d6-445">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="dd3d6-446">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="dd3d6-446">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="dd3d6-448">Dans le dossier *Models*, créez un fichier *Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="dd3d6-449">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="dd3d6-450">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="dd3d6-451">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="dd3d6-452">Générer automatiquement le modèle d’étudiant</span><span class="sxs-lookup"><span data-stu-id="dd3d6-452">Scaffold the student model</span></span>

<span data-ttu-id="dd3d6-453">Dans cette section, le modèle d’étudiant est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="dd3d6-454">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création (Create), de lecture (Read), de mise à jour (Update) et de suppression (Delete) (CRUD) pour le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="dd3d6-455">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-455">Build the project.</span></span>
* <span data-ttu-id="dd3d6-456">Créez le dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd3d6-458">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students* > **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="dd3d6-459">Dans le dialogue **Ajouter l’échafaudage,** sélectionnez **les pages Razor à l’aide de l’entité Cadre (CRUD)** > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="dd3d6-460">Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dd3d6-461">Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="dd3d6-462">Dans la ligne **Classe de contexte de données**, sélectionnez le signe **+** (plus) et remplacez le nom généré par **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="dd3d6-463">Dans la liste déroulante **Classe de contexte de données**, sélectionnez **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="dd3d6-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="dd3d6-464">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-464">Select **Add**.</span></span>

![Boîte de dialogue CRUD](intro/_static/s1.png)

<span data-ttu-id="dd3d6-466">Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) si vous rencontrez un problème à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd3d6-468">Exécutez les commandes suivantes pour générer automatiquement le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="dd3d6-469">Le processus de génération de modèles automatique a créé et changé les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="dd3d6-470">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="dd3d6-470">Files created</span></span>

* <span data-ttu-id="dd3d6-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="dd3d6-472">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dd3d6-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="dd3d6-473">Mises à jour du fichier</span><span class="sxs-lookup"><span data-stu-id="dd3d6-473">File updates</span></span>

* <span data-ttu-id="dd3d6-474">*Startup.cs* : Les changements apportés à ce fichier sont détaillés dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="dd3d6-475">*appSettings.JSON* : La chaîne de connexion utilisée pour se connecter à une base de données locale est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dd3d6-476">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="dd3d6-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dd3d6-477">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dd3d6-478">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dd3d6-479">Ces services sont affectés aux composants qui les nécessitent (par exemple les Pages Razor) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dd3d6-480">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dd3d6-481">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dd3d6-482">Examinez la méthode `ConfigureServices` dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="dd3d6-483">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="dd3d6-484">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dd3d6-485">Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="dd3d6-486">Mettre à jour la méthode Main</span><span class="sxs-lookup"><span data-stu-id="dd3d6-486">Update main</span></span>

<span data-ttu-id="dd3d6-487">Dans *Program.cs*, modifiez la méthode `Main` pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="dd3d6-488">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="dd3d6-489">Appelez [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="dd3d6-490">Supprimez le contexte une fois la méthode `EnsureCreated` exécutée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="dd3d6-491">Le code suivant montre le fichier *Program.cs* mis à jour.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="dd3d6-492">`EnsureCreated` garantit que la base de données existe pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="dd3d6-493">Si elle existe, aucune action n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-493">If it exists, no action is taken.</span></span> <span data-ttu-id="dd3d6-494">Si elle n’existe pas, la base de données et tous ses schéma sont créés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="dd3d6-495">`EnsureCreated` n’utilise pas de migrations pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="dd3d6-496">Une base de données créée avec `EnsureCreated` ne peut pas être mise à jour à l’aide de migrations par la suite.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="dd3d6-497">`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="dd3d6-498">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-498">Delete the DB.</span></span>
* <span data-ttu-id="dd3d6-499">Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="dd3d6-500">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-500">Run the app.</span></span>
* <span data-ttu-id="dd3d6-501">`EnsureCreated` crée une base de données avec la colonne `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="dd3d6-502">`EnsureCreated` est pratique au début du développement quand le schéma évolue rapidement.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="dd3d6-503">Plus loin dans le tutoriel, la base de données est supprimée et les migrations sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="dd3d6-504">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="dd3d6-504">Test the app</span></span>

<span data-ttu-id="dd3d6-505">Exécutez l’application et acceptez la politique de cookies.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="dd3d6-506">Cette application ne conserve pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="dd3d6-507">Vous pouvez en savoir plus sur la politique de cookies à la section [Prise en charge du règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="dd3d6-508">Sélectionnez le lien **Students**, puis **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="dd3d6-509">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="dd3d6-510">Examiner le contexte de base de données SchoolContext</span><span class="sxs-lookup"><span data-stu-id="dd3d6-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="dd3d6-511">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="dd3d6-512">Le contexte de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dd3d6-513">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="dd3d6-514">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="dd3d6-515">Mettez à jour *SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="dd3d6-516">Le code en surbrillance crée une propriété [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="dd3d6-517">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-517">In EF Core terminology:</span></span>

* <span data-ttu-id="dd3d6-518">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="dd3d6-519">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dd3d6-520">`DbSet<Enrollment>` et `DbSet<Course>` peuvent être omis.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="dd3d6-521">EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="dd3d6-522">Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="dd3d6-523">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="dd3d6-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="dd3d6-524">La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="dd3d6-525">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="dd3d6-526">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="dd3d6-527">Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="dd3d6-528">Ajouter du code pour initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="dd3d6-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="dd3d6-529">EF Core crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="dd3d6-530">Dans cette section, une méthode `Initialize` est écrite pour la remplir avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="dd3d6-531">Dans le dossier *Data*, créez un fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="dd3d6-532">Remarque : Le `Models` code précédent utilise`namespace ContosoUniversity.Models`pour `Data`l’espace de nom ( ) plutôt que .</span><span class="sxs-lookup"><span data-stu-id="dd3d6-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="dd3d6-533">`Models` est cohérent avec le code généré par l’outil de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="dd3d6-534">Pour plus d’informations, consultez [ce problème de génération de modèles automatique GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="dd3d6-535">Le code vérifie s’il existe des étudiants dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="dd3d6-536">S’il n’y en a pas, la base de données est initialisée avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="dd3d6-537">Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="dd3d6-538">La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="dd3d6-539">Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="dd3d6-540">Dans *Program.cs*, modifiez la méthode `Main` pour appeler `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="dd3d6-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd3d6-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd3d6-542">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd3d6-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd3d6-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd3d6-544">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="dd3d6-545">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="dd3d6-545">View the DB</span></span>

<span data-ttu-id="dd3d6-546">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="dd3d6-547">Il sera donc « SchoolContext-{GUID} ».</span><span class="sxs-lookup"><span data-stu-id="dd3d6-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="dd3d6-548">Le GUID est différent pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="dd3d6-549">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="dd3d6-550">Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="dd3d6-551">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="dd3d6-552">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="dd3d6-553">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="dd3d6-553">Asynchronous code</span></span>

<span data-ttu-id="dd3d6-554">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="dd3d6-555">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="dd3d6-556">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="dd3d6-557">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="dd3d6-558">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="dd3d6-559">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="dd3d6-560">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="dd3d6-561">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="dd3d6-562">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="dd3d6-563">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="dd3d6-564">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="dd3d6-565">Crée automatiquement l’objet [Task](/dotnet/api/system.threading.tasks.task) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="dd3d6-566">Pour plus d’informations, consultez [Type de retour Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="dd3d6-567">Le type de retour implicite `Task` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="dd3d6-568">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="dd3d6-569">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="dd3d6-570">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="dd3d6-571">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="dd3d6-572">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="dd3d6-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="dd3d6-573">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="dd3d6-574">Cela comprend `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="dd3d6-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="dd3d6-575">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="dd3d6-576">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="dd3d6-577">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd3d6-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="dd3d6-578">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="dd3d6-579">Dans le didacticiel suivant, nous allons examiner les opérations CRUD de base (créer, lire, mettre à jour, supprimer).</span><span class="sxs-lookup"><span data-stu-id="dd3d6-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="dd3d6-580">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dd3d6-580">Additional resources</span></span>

* [<span data-ttu-id="dd3d6-581">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="dd3d6-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="dd3d6-582">Suivant</span><span class="sxs-lookup"><span data-stu-id="dd3d6-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
