---
title: ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)
author: JeremyLikness
description: Aide sur l’utilisation de EF Core dans les Blazor Server applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: a1b295b2ce42bc5ee06b8b9579ea2c70d480580a
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009659"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="6bf29-103">ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="6bf29-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="6bf29-104">Par : [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="6bf29-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6bf29-105">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="6bf29-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6bf29-106">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="6bf29-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6bf29-107">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="6bf29-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6bf29-108">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6bf29-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6bf29-109">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6bf29-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6bf29-110">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="6bf29-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6bf29-111">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="6bf29-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6bf29-112">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="6bf29-112">Sample app</span></span>

<span data-ttu-id="6bf29-113">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="6bf29-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6bf29-114">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="6bf29-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6bf29-115">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="6bf29-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6bf29-116">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bf29-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6bf29-117">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="6bf29-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6bf29-118">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="6bf29-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6bf29-119">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="6bf29-120">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="6bf29-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6bf29-121">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="6bf29-122">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="6bf29-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="6bf29-123">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="6bf29-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="6bf29-124">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="6bf29-124">Database access</span></span>

<span data-ttu-id="6bf29-125">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6bf29-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6bf29-126">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="6bf29-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="6bf29-127">Dans Blazor Server les applications, les inscriptions de service étendues peuvent être problématiques, car l’instance est partagée entre les composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6bf29-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6bf29-128"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="6bf29-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6bf29-129">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="6bf29-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6bf29-130">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="6bf29-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6bf29-131">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6bf29-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6bf29-132">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="6bf29-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="6bf29-133">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6bf29-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="6bf29-134">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="6bf29-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="6bf29-135">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="6bf29-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6bf29-136">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="6bf29-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="6bf29-137">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="6bf29-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="6bf29-138">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="6bf29-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="6bf29-139">Nouvelles instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="6bf29-139">New DbContext instances</span></span>

<span data-ttu-id="6bf29-140">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="6bf29-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6bf29-141">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="6bf29-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6bf29-142">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="6bf29-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="6bf29-143">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="6bf29-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6bf29-144">EF Core 5,0 ou version ultérieure fournit une fabrique intégrée pour la création de nouveaux contextes.</span><span class="sxs-lookup"><span data-stu-id="6bf29-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="6bf29-145">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="6bf29-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6bf29-146">Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="6bf29-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="6bf29-147">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="6bf29-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6bf29-148">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="6bf29-149">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="6bf29-150">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="6bf29-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="6bf29-151">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="6bf29-151">Scope to the component lifetime</span></span>

<span data-ttu-id="6bf29-152">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6bf29-153">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="6bf29-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6bf29-154">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6bf29-155">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="6bf29-156">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="6bf29-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="6bf29-157">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="6bf29-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="6bf29-158">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="6bf29-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="6bf29-159">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="6bf29-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6bf29-160">L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="6bf29-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6bf29-161">Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="6bf29-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6bf29-162">Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6bf29-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="6bf29-163">Cet article traite de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6bf29-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6bf29-164">Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes.</span><span class="sxs-lookup"><span data-stu-id="6bf29-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6bf29-165">L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="6bf29-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6bf29-166">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="6bf29-166">Sample app</span></span>

<span data-ttu-id="6bf29-167">L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core.</span><span class="sxs-lookup"><span data-stu-id="6bf29-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6bf29-168">L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="6bf29-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6bf29-169">L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="6bf29-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6bf29-170">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bf29-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6bf29-171">L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="6bf29-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6bf29-172">L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées.</span><span class="sxs-lookup"><span data-stu-id="6bf29-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6bf29-173">Cette configuration est configurée dans `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="6bf29-174">Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération.</span><span class="sxs-lookup"><span data-stu-id="6bf29-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6bf29-175">Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="6bf29-176">Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés.</span><span class="sxs-lookup"><span data-stu-id="6bf29-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="6bf29-177">Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="6bf29-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="6bf29-178">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="6bf29-178">Database access</span></span>

<span data-ttu-id="6bf29-179">EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6bf29-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6bf29-180">EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut.</span><span class="sxs-lookup"><span data-stu-id="6bf29-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="6bf29-181">Dans Blazor Server les applications, cela peut être problématique, car l’instance est partagée par plusieurs composants dans le circuit de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6bf29-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6bf29-182"><xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée.</span><span class="sxs-lookup"><span data-stu-id="6bf29-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6bf29-183">Les durées de vie existantes sont inappropriées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="6bf29-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6bf29-184">**Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.</span><span class="sxs-lookup"><span data-stu-id="6bf29-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6bf29-185">**Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6bf29-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6bf29-186">Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.</span><span class="sxs-lookup"><span data-stu-id="6bf29-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="6bf29-187">Accès à la base de données</span><span class="sxs-lookup"><span data-stu-id="6bf29-187">Database access</span></span>

<span data-ttu-id="6bf29-188">Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="6bf29-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="6bf29-189">Par défaut, envisagez d’utiliser un contexte par opération.</span><span class="sxs-lookup"><span data-stu-id="6bf29-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="6bf29-190">Le contexte est conçu pour une instanciation rapide et à faible surcharge :</span><span class="sxs-lookup"><span data-stu-id="6bf29-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6bf29-191">Utilisez un indicateur pour empêcher plusieurs opérations simultanées :</span><span class="sxs-lookup"><span data-stu-id="6bf29-191">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="6bf29-192">Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.</span><span class="sxs-lookup"><span data-stu-id="6bf29-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="6bf29-193">Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="6bf29-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="6bf29-194">Créer des instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="6bf29-194">Create new DbContext instances</span></span>

<span data-ttu-id="6bf29-195">Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="6bf29-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6bf29-196">Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="6bf29-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6bf29-197">Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.</span><span class="sxs-lookup"><span data-stu-id="6bf29-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="6bf29-198">La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique.</span><span class="sxs-lookup"><span data-stu-id="6bf29-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6bf29-199">L’exemple d’application implémente sa propre fabrique dans `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="6bf29-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="6bf29-200">Dans la fabrique précédente, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfait toutes les dépendances via le fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="6bf29-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="6bf29-201">L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données.</span><span class="sxs-lookup"><span data-stu-id="6bf29-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6bf29-202">Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :</span><span class="sxs-lookup"><span data-stu-id="6bf29-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="6bf29-203">La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="6bf29-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6bf29-204">Par exemple, dans `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="6bf29-205">`Wrapper` est une [référence de composant](xref:blazor/components/index#capture-references-to-components) au `GridWrapper` composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-205">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="6bf29-206">Consultez le `Index` composant ( `Pages/Index.razor` ) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="6bf29-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="6bf29-207">Portée à la durée de vie du composant</span><span class="sxs-lookup"><span data-stu-id="6bf29-207">Scope to the component lifetime</span></span>

<span data-ttu-id="6bf29-208">Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6bf29-209">Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="6bf29-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6bf29-210">Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="6bf29-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6bf29-211">Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="6bf29-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="6bf29-212">L’exemple d’application s’assure que le contexte est supprimé lorsque le composant est supprimé :</span><span class="sxs-lookup"><span data-stu-id="6bf29-212">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="6bf29-213">Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte.</span><span class="sxs-lookup"><span data-stu-id="6bf29-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="6bf29-214">Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :</span><span class="sxs-lookup"><span data-stu-id="6bf29-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="6bf29-215">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6bf29-215">In the preceding example:</span></span>

* <span data-ttu-id="6bf29-216">Lorsque `Busy` a la valeur `true` , les opérations asynchrones peuvent commencer.</span><span class="sxs-lookup"><span data-stu-id="6bf29-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="6bf29-217">Lorsque `Busy` a la valeur `false` , les opérations asynchrones doivent être terminées.</span><span class="sxs-lookup"><span data-stu-id="6bf29-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="6bf29-218">Placez une logique de gestion des erreurs supplémentaire dans un `catch` bloc.</span><span class="sxs-lookup"><span data-stu-id="6bf29-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6bf29-219">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6bf29-219">Additional resources</span></span>

* [<span data-ttu-id="6bf29-220">Documentation EF Core</span><span class="sxs-lookup"><span data-stu-id="6bf29-220">EF Core documentation</span></span>](/ef/)
