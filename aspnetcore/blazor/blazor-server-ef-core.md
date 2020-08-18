---
title: ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)
author: JeremyLikness
description: Aide sur l’utilisation de EF Core dans les Blazor Server applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ce2467ccda04b584a6bc04d1c6d9c66bcd659f2
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504253"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server avec Entity Framework Core (EFCore)

Par : [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server est une infrastructure d’application avec état. L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*. Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit. Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.

> [!NOTE]
> Cet article traite de EF Core dans les Blazor Server applications. Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes. L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.

## <a name="sample-app"></a>Exemple d'application

L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core. L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour. L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme. L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées. Cette configuration est configurée dans `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération. Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.

> [!NOTE]
> Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés. Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

## <a name="database-access"></a>Accès à la base de données

EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut. Dans Blazor Server les applications, les inscriptions de service étendues peuvent être problématiques, car l’instance est partagée entre les composants dans le circuit de l’utilisateur. <xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée. Les durées de vie existantes sont inappropriées pour les raisons suivantes :

* **Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.
* **Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.
* Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.

Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.

* Par défaut, envisagez d’utiliser un contexte par opération. Le contexte est conçu pour une instanciation rapide et à faible surcharge :

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Utilisez un indicateur pour empêcher plusieurs opérations simultanées :

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

  Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.

* Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime).

### <a name="new-dbcontext-instances"></a>Nouvelles instances de DbContext

Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance. Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires. Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.

La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique. EF Core 5,0 ou version ultérieure fournit une fabrique intégrée pour la création de nouveaux contextes.

L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données. Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances. Par exemple, dans `Pages/Index.razor` :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

### <a name="scope-to-the-component-lifetime"></a>Portée à la durée de vie du composant

Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant. Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.
Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant. Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

L’exemple d’application garantit que le contact est supprimé lorsque le composant est supprimé :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte. Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server est une infrastructure d’application avec état. L’application maintient une connexion permanente au serveur et l’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*. Voici un exemple d’état utilisateur : données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) qui sont étendues au circuit. Le modèle d’application unique Blazor Server fourni par requiert une approche spéciale pour utiliser Entity Framework Core.

> [!NOTE]
> Cet article traite de EF Core dans les Blazor Server applications. Blazor WebAssembly les applications s’exécutent dans un bac à sable (sandbox) webassembly qui empêche la plupart des connexions directes. L’exécution de EF Core dans Blazor WebAssembly dépasse le cadre de cet article.

## <a name="sample-app"></a>Exemple d'application

L’exemple d’application a été créé en tant que référence pour les Blazor Server applications qui utilisent EF Core. L’exemple d’application comprend une grille avec des opérations de tri et de filtrage, de suppression, d’ajout et de mise à jour. L’exemple illustre l’utilisation de EF Core pour gérer l’accès concurrentiel optimiste.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple utilise une base de données [SQLite](https://www.sqlite.org/index.html) locale pour qu’elle puisse être utilisée sur n’importe quelle plateforme. L’exemple configure également la journalisation de la base de données pour afficher les requêtes SQL qui sont générées. Cette configuration est configurée dans `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Les composants de grille, d’ajout et de vue utilisent le modèle « contexte par opération », où un contexte est créé pour chaque opération. Le composant Edit utilise le modèle « contexte par composant », où un contexte est créé pour chaque composant.

> [!NOTE]
> Certains des exemples de code de cette rubrique requièrent des espaces de noms et des services qui ne sont pas affichés. Pour inspecter le code entièrement opérationnel, y compris [`@using`](xref:mvc/views/razor#using) les [`@inject`](xref:mvc/views/razor#inject) directives et requises pour obtenir des Razor exemples, consultez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

## <a name="database-access"></a>Accès à la base de données

EF Core s’appuie sur un <xref:Microsoft.EntityFrameworkCore.DbContext> comme moyen de configurer l’accès à la [base de données](/ef/core/miscellaneous/configuring-dbcontext) et d’agir en tant qu' [*unité de travail*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core fournit l' <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension pour les applications ASP.net core qui inscrit le contexte en tant que service *étendu* par défaut. Dans Blazor Server les applications, cela peut être problématique, car l’instance est partagée par plusieurs composants dans le circuit de l’utilisateur. <xref:Microsoft.EntityFrameworkCore.DbContext> n’est pas thread-safe et n’est pas conçu pour une utilisation simultanée. Les durées de vie existantes sont inappropriées pour les raisons suivantes :

* **Singleton** partage l’État sur tous les utilisateurs de l’application et provoque une utilisation simultanée inappropriée.
* **Étendue** (valeur par défaut) pose un problème similaire entre les composants pour le même utilisateur.
* Les résultats **transitoires** génèrent une nouvelle instance par demande ; mais comme les composants peuvent être de longue durée de vie, cela se traduit par un contexte plus long que prévu.

## <a name="database-access"></a>Accès à la base de données

Les recommandations suivantes sont conçues pour fournir une approche cohérente de l’utilisation de EF Core dans les Blazor Server applications.

* Par défaut, envisagez d’utiliser un contexte par opération. Le contexte est conçu pour une instanciation rapide et à faible surcharge :

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Utilisez un indicateur pour empêcher plusieurs opérations simultanées :

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

  Placez les opérations après la `Loading = true;` ligne dans le `try` bloc.

* Pour les opérations à long terme qui tirent parti du [suivi des modifications](/ef/core/querying/tracking) ou du [contrôle d’accès concurrentiel](/ef/core/saving/concurrency)de EF Core, limitez [le contexte à la durée de vie du composant](#scope-to-the-component-lifetime).

### <a name="create-new-dbcontext-instances"></a>Créer des instances de DbContext

Le moyen le plus rapide de créer une nouvelle <xref:Microsoft.EntityFrameworkCore.DbContext> instance consiste à utiliser `new` pour créer une nouvelle instance. Toutefois, il existe plusieurs scénarios qui peuvent nécessiter la résolution de dépendances supplémentaires. Par exemple, vous souhaiterez peut-être utiliser [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) pour configurer le contexte.

La solution recommandée pour créer un nouveau <xref:Microsoft.EntityFrameworkCore.DbContext> avec des dépendances consiste à utiliser une fabrique. L’exemple d’application implémente sa propre fabrique dans `Data/DbContextFactory.cs` .

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Dans la fabrique précédente, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfait toutes les dépendances via le fournisseur de services.

L’exemple suivant configure [SQLite](https://www.sqlite.org/index.html) et active la journalisation des données. Le code utilise une méthode d’extension pour configurer la fabrique de base de données pour DI et fournir les options par défaut :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

La fabrique est injectée dans des composants et utilisée pour créer de nouvelles instances. Par exemple, dans `Pages/Index.razor` :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

### <a name="scope-to-the-component-lifetime"></a>Portée à la durée de vie du composant

Vous pouvez créer un <xref:Microsoft.EntityFrameworkCore.DbContext> qui existe pour la durée de vie d’un composant. Cela vous permet de l’utiliser comme une [unité de travail](https://martinfowler.com/eaaCatalog/unitOfWork.html) et de tirer parti des fonctionnalités intégrées, telles que le suivi des modifications et la résolution de l’accès concurrentiel.
Vous pouvez utiliser la fabrique pour créer un contexte et effectuer le suivi de la durée de vie du composant. Tout d’abord, implémentez <xref:System.IDisposable> et injectez la fabrique comme indiqué dans `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

L’exemple d’application garantit que le contact est supprimé lorsque le composant est supprimé :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Enfin, [`OnInitializedAsync`](xref:blazor/components/lifecycle) est substitué pour créer un nouveau contexte. Dans l’exemple d’application, [`OnInitializedAsync`](xref:blazor/components/lifecycle) charge le contact dans la même méthode :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

Dans l’exemple précédent :

* Lorsque `Busy` a la valeur `true` , les opérations asynchrones peuvent commencer. Lorsque `Busy` a la valeur `false` , les opérations asynchrones doivent être terminées.
* Placez une logique de gestion des erreurs supplémentaire dans un `catch` bloc.

:::moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

> [Documentation EF Core](/ef/)
