---
title: Ajouter un modèle à une application de pages Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ajouter des classes pour gérer des films dans une base de données à l’aide d’Entity Framework Core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658934"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Ajouter un modèle à une application de pages Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Dans cette section, des cours sont ajoutés pour la gestion des films dans une base de [données transplateforme SQLite](https://www.sqlite.org/index.html). Les applications créées à partir d’un modèle ASP.NET Core utilisent une base de données SQLite. Les catégories de modèles de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) pour travailler avec la base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models*. Sélectionnez **Ajouter** > **la classe**. Nommez la classe **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models*.
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans Solution Pad, cliquez à droite sur le projet **RazorPagesMovie,** puis **sélectionnez Ajouter** > **un nouveau dossier...**. Nommez les *modèles*de dossier .
* Cliquez à droite sur le dossier *Modèles,* puis sélectionnez **Ajouter** > **un nouveau fichier...**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie**, puis sélectionnez **Nouveau**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.
* Nommez le dossier *Movies*.

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Razor Pages avec Entity Framework (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :

* Dans la liste déroulante **Classe de modèle**, sélectionnez **Film (RazorPagesMovie.Models)**.
* Dans la ligne **Classe du contexte de données**, sélectionnez le signe (plus) **+** et changez le nom généré de RazorPagesMovie.**Models**.RazorPagesMovieContext en RazorPagesMovie.**Data**.RazorPagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).
* Installez l’outil de génération de modèles automatique :

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Pour Windows**: Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux**, exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.
* Nommez le dossier *Movies*.

Cliquez à droite sur le dossier *Pages/Films* > **Ajouter** > **un nouvel échafaudage...**.

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans le nouveau dialogue **d’échafaudage,** sélectionnez **Des pages de rasoir en utilisant entity Framework (CRUD)** > **Next**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :

* Dans la **classe Modèle** baissez, sélectionnez ou tapez, **Film (RazorPagesMovie.Models)**.
* Dans la rangée **de classe de contexte de données,** tapez le nom pour la nouvelle classe, RazorPagesMovie. **Données**. RazorPagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

### <a name="add-ef-tools"></a>Ajouter des outils EF

Exécutez la commande CLI CLI de base suivante :

```dotnetcli
dotnet tool install --global dotnet-ef
```

La commande précédente ajoute les outils de base du cadre d’entité pour le CLI de base .NET.

---

### <a name="files-created"></a>Fichiers créés

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/Movies* : Create, Delete, Details, Edit, Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/Movies* : Create, Delete, Details, Edit, Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Le processus de génération de modèles automatique crée les fichiers suivants :

* *Pages/Movies* : Create, Delete, Details, Edit, Index.

Les fichiers créés sont expliqués dans la section suivante.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Les commandes précédentes génèrent l’avertissement suivant : « Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'. Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »

Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.

La commande de migrations génère du code pour créer le schéma initial de base de données. Le schéma est basé sur `DbContext`le modèle spécifié dans . L’argument `InitialCreate` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.

La `update` commande `Up` exécute la méthode dans les migrations qui n’ont pas été appliquées. Dans ce `update` cas, `Up` exécute la méthode dans *les migrations /\<délai>_InitialCreate.cs* fichier, qui crée la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont affectés aux composants qui les nécessitent (par exemple les Pages Razor) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une propriété [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examinez la méthode `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Test de l'application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le lien **Créer**.

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les liens **Edit**, **Details** et **Delete**.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent: Get Started](xref:tutorials/razor-pages/razor-pages-start)
> [Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Dans cette section, des cours sont ajoutés pour la gestion des films dans une base de [données transplateforme SQLite](https://www.sqlite.org/index.html). Les applications créées à partir d’un modèle ASP.NET Core utilisent une base de données SQLite. Les catégories de modèles de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) pour travailler avec la base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **RazorPagesMovie** > **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.

Cliquez avec le bouton droit sur le dossier *Models*. Sélectionnez **Ajouter** > **la classe**. Nommez la classe **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models*.
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **RazorPagesMovie**, puis sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Models*.
* Cliquez avec le bouton droit sur le dossier *Modèles*, puis sélectionnez **Ajouter** > **Nouveau fichier**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie**, puis sélectionnez **Nouveau**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.
* Nommez le dossier *Movies*.

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter un modèle automatique**, sélectionnez **Razor Pages avec Entity Framework (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Dans la liste déroulante **Classe de modèle**, sélectionnez **Film (RazorPagesMovie.Models)**.
* Dans la ligne **Classe du contexte de données**, sélectionnez le signe (plus) **+** et acceptez le nom généré **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arp.png)

Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs*, *Startup.cs* et *.csproj*).

* **Pour Windows**: Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux**, exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier**.
* Nommez le dossier *Movies*.

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement**.

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans le nouveau dialogue **d’échafaudage Ajouter,** sélectionnez **les pages Razor à l’aide du cadre d’entité (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Renseignez la boîte de dialogue **Pages Razor avec Entity Framework (CRUD)** :

* Dans la **classe Modèle** baissez, sélectionnez ou **tapez Movie**.
* Dans la ligne **de classe de contexte data,** tapez le **RazorPagesMovieContext,** ce qui créera une nouvelle classe de contexte db avec l’espace de nom correct. Dans ce cas, il sera **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le fichier *appsettings.json* est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

---

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

### <a name="files-created"></a>Fichiers créés

* *Pages/Movies* : Create, Delete, Details, Edit, Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Fichier mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration Initial
Update-Database
```

La commande `Add-Migration` génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans `DbContext` (dans le fichier *RazorPagesMovieContext.cs*). L’argument `InitialCreate` est utilisé pour nommer la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé. Pour plus d’informations, consultez <xref:data/ef-mvc/migrations>.

La commande `Update-Database` exécute la méthode `Up` dans le fichier *Migrations/\<horodatage>_InitialCreate.cs*. La méthode `Up` crée la base de données.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Les commandes précédentes génèrent l’avertissement suivant : «*Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'. Cela entraînera des valeurs silencieusement tronquées si elles ne rentrent pas dans la précision et l’échelle par défaut. Spécifier explicitement le type de colonne de serveur SQL qui peut accueillir toutes les valeurs à l’aide de 'HasColumnType'.* Vous pouvez ignorer cet avertissement, il sera fixé dans un tutoriel ultérieur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont affectés aux composants qui les nécessitent (par exemple les Pages Razor) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une propriété [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.NET Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du fichier *appsettings.json*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examinez la méthode `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Test de l'application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le lien **Créer**.

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les liens **Edit**, **Details** et **Delete**.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent: Get Started](xref:tutorials/razor-pages/razor-pages-start)
> [Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
