---
title: Bien démarrer avec ASP.NET Core et Entity Framework 6
author: rick-anderson
description: Cet article montre comment utiliser Entity Framework 6 dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 7c77ecb663bebcaf0c6239f07ac15dda23f17b9c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775477"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="d4bc1-103">Bien démarrer avec ASP.NET Core et Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="d4bc1-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="d4bc1-104">Par [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d4bc1-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d4bc1-105">Cet article montre comment utiliser Entity Framework 6 dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="d4bc1-106">Vue d’ensemble</span><span class="sxs-lookup"><span data-stu-id="d4bc1-106">Overview</span></span>

<span data-ttu-id="d4bc1-107">Pour utiliser Entity Framework 6, votre projet doit être compilé pour .NET Framework, car Entity Framework 6 ne prend pas en charge .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="d4bc1-108">Si vous avez besoin de fonctionnalités multiplateformes, vous devez effectuer une mise à niveau vers [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="d4bc1-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="d4bc1-109">La méthode recommandée pour utiliser Entity Framework 6 dans une application ASP.NET Core consiste à placer le contexte EF6 et les classes de modèle dans un projet de bibliothèque de classes qui cible .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="d4bc1-110">Ajoutez une référence à la bibliothèque de classes depuis le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="d4bc1-111">Consultez l’exemple [Solution Visual Studio avec des projets Entity Framework 6 et ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="d4bc1-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="d4bc1-112">Vous ne pouvez pas mettre un contexte EF6 dans un projet ASP.NET Core, car les projets .NET Core ne prennent pas en charge toutes les fonctionnalités nécessaires pour les commandes EF6, comme *Enable-Migrations*.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="d4bc1-113">Quel que soit le type de projet où vous placez votre contexte EF6, seuls les outils en ligne de commande EF6 fonctionnent avec un contexte EF6.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="d4bc1-114">Par exemple, `Scaffold-DbContext` est disponible seulement dans Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="d4bc1-115">Si vous devez effectuer une ingénierie à rebours pour une base de données dans un modèle EF6, consultez [Code First pour une base de données existante](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="d4bc1-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="d4bc1-116">Référencer le framework complet et EF6 dans le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4bc1-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="d4bc1-117">Votre projet de ASP.NET Core doit cibler .NET Framework et référencer EF6.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="d4bc1-118">Par exemple, le fichier *.csproj* de votre projet ASP.NET Core doit être similaire à l’exemple suivant (seules les parties concernées du fichier sont montrées).</span><span class="sxs-lookup"><span data-stu-id="d4bc1-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="d4bc1-119">Quand vous créez un projet, utilisez le modèle **Application web ASP.NET Core (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="d4bc1-120">Gérer les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="d4bc1-120">Handle connection strings</span></span>

<span data-ttu-id="d4bc1-121">Les outils en ligne de commande EF6 que vous allez utiliser dans le projet de bibliothèque de classes EF6 nécessitent un constructeur par défaut pour pouvoir instancier le contexte.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="d4bc1-122">Si vous voulez spécifier la chaîne de connexion à utiliser dans le projet ASP.NET Core, votre constructeur de contexte doit avoir un paramètre qui vous permet de passer la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="d4bc1-123">Voici un exemple.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="d4bc1-124">Comme votre contexte EF6 n’a pas de constructeur sans paramètre, votre projet EF6 doit fournir une implémentation de [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="d4bc1-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="d4bc1-125">Les outils en ligne de commande EF6 trouvent et utilisent cette implémentation : ils peuvent donc instancier le contexte.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="d4bc1-126">Voici un exemple.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="d4bc1-127">Dans cet exemple de code, l’implémentation de `IDbContextFactory` passe une chaîne de connexion codée en dur.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="d4bc1-128">Il s’agit de la chaîne de connexion utilisée par les outils en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="d4bc1-129">Vous pouvez implémenter une stratégie pour garantir que la bibliothèque de classes utilise la même chaîne de connexion que celle de l’application appelante.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="d4bc1-130">Par exemple, vous pouvez obtenir la valeur d’une variable d’environnement dans les deux projets.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="d4bc1-131">Configurer l’injection de dépendances dans le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4bc1-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="d4bc1-132">Dans le fichier *Startup.cs* du projet Core, configurez le contexte EF6 pour l’injection de dépendances dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="d4bc1-133">Les objets de contexte EF doit avoir une durée de vie limitée à une demande.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="d4bc1-134">Vous pouvez ensuite obtenir une instance du contexte dans vos contrôleurs en utilisant l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="d4bc1-135">Le code est similaire à ce que vous pourriez écrire pour un contexte EF Core :</span><span class="sxs-lookup"><span data-stu-id="d4bc1-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="d4bc1-136">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="d4bc1-136">Sample application</span></span>

<span data-ttu-id="d4bc1-137">Pour un exemple d’application opérationnelle, consultez [l’exemple de solution Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) qui accompagne cet article.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="d4bc1-138">Vous pouvez créer cet exemple à partir de zéro dans Visual Studio en effectuant les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="d4bc1-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="d4bc1-139">Créez une solution.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-139">Create a solution.</span></span>

* <span data-ttu-id="d4bc1-140">**Ajouter**  >  **Nouveau projet**  >  **Web**  >  **Application Web ASP.net Core**</span><span class="sxs-lookup"><span data-stu-id="d4bc1-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="d4bc1-141">Dans la boîte de dialogue de sélection du modèle de projet, sélectionnez API et .NET Framework dans la liste déroulante</span><span class="sxs-lookup"><span data-stu-id="d4bc1-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="d4bc1-142">**Ajouter**  >  **Nouveau projet**  >  **Bureau Windows**  >  **Bibliothèque de classes (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="d4bc1-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="d4bc1-143">Dans la **Console du Gestionnaire de package** pour les deux projets, exécutez la commande `Install-Package Entityframework`.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="d4bc1-144">Dans le projet de bibliothèque de classes, créez des classes de modèle de données et une classe de contexte, ainsi qu’une implémentation de `IDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="d4bc1-145">Dans la console du Gestionnaire de package, pour le projet de bibliothèque de classes, exécutez les commandes `Enable-Migrations` et `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="d4bc1-146">Si vous avez défini le projet ASP.NET Core comme projet de démarrage, ajoutez `-StartupProjectName EF6` à ces commandes.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="d4bc1-147">Dans le projet Core, ajoutez une référence de projet au projet de bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="d4bc1-148">Dans le projet Core, dans *Startup.cs*, inscrivez le contexte pour l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="d4bc1-149">Dans le projet Core, dans *appsettings.json*, ajoutez la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="d4bc1-150">Dans le projet Core, ajoutez un contrôleur et une ou plusieurs vues pour vérifier que vous pouvez lire et écrire des données.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="d4bc1-151">(Notez que la génération de modèles automatique d’ASP.NET Core MVC ne fonctionne pas avec le contexte EF6 référencé depuis la bibliothèque de classes.)</span><span class="sxs-lookup"><span data-stu-id="d4bc1-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="d4bc1-152">Résumé</span><span class="sxs-lookup"><span data-stu-id="d4bc1-152">Summary</span></span>

<span data-ttu-id="d4bc1-153">Cet article a fourni des instructions de base pour utiliser Entity Framework 6 dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4bc1-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4bc1-154">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d4bc1-154">Additional resources</span></span>

* [<span data-ttu-id="d4bc1-155">Entity Framework - Configuration basée sur le code</span><span class="sxs-lookup"><span data-stu-id="d4bc1-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
