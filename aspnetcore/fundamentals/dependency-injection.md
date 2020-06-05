---
title: Injection de dépendances dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core implémente l’injection de dépendances et comment l’utiliser.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84271902"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="e7ee1-103">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee1-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="e7ee1-104">Par [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="e7ee1-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7ee1-105">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e7ee1-106">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e7ee1-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7ee1-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e7ee1-108">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="e7ee1-108">Overview of dependency injection</span></span>

<span data-ttu-id="e7ee1-109">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e7ee1-110">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="e7ee1-111">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e7ee1-112">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="e7ee1-113">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e7ee1-114">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e7ee1-115">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e7ee1-116">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e7ee1-117">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e7ee1-118">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e7ee1-119">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e7ee1-120">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e7ee1-121">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e7ee1-122">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e7ee1-123">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e7ee1-124">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e7ee1-125">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e7ee1-126">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e7ee1-127">Dans l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e7ee1-128">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e7ee1-129">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="e7ee1-130">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e7ee1-131">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e7ee1-132">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e7ee1-133">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e7ee1-134">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e7ee1-135">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e7ee1-136">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e7ee1-137">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="e7ee1-138">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e7ee1-139">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e7ee1-140">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="e7ee1-141">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e7ee1-142">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e7ee1-143">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e7ee1-144">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e7ee1-145">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="e7ee1-146">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e7ee1-147">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e7ee1-148">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="e7ee1-149">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="e7ee1-149">Services injected into Startup</span></span>

<span data-ttu-id="e7ee1-150">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e7ee1-151">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="e7ee1-152">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e7ee1-153">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="e7ee1-153">Framework-provided services</span></span>

<span data-ttu-id="e7ee1-154">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e7ee1-155">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e7ee1-156">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="e7ee1-157">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="e7ee1-158">Type de service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-158">Service Type</span></span> | <span data-ttu-id="e7ee1-159">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="e7ee1-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-160">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="e7ee1-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="e7ee1-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="e7ee1-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="e7ee1-164">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="e7ee1-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-166">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="e7ee1-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-170">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="e7ee1-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="e7ee1-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="e7ee1-174">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="e7ee1-174">Register additional services with extension methods</span></span>

<span data-ttu-id="e7ee1-175">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e7ee1-176">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="e7ee1-177">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e7ee1-178">Durées de service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-178">Service lifetimes</span></span>

<span data-ttu-id="e7ee1-179">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e7ee1-180">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="e7ee1-181">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-181">Transient</span></span>

<span data-ttu-id="e7ee1-182">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="e7ee1-183">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="e7ee1-184">Délimité</span><span class="sxs-lookup"><span data-stu-id="e7ee1-184">Scoped</span></span>

<span data-ttu-id="e7ee1-185">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="e7ee1-186">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e7ee1-187">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e7ee1-188">Pour plus d’informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e7ee1-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-189">Singleton</span></span>

<span data-ttu-id="e7ee1-190">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e7ee1-191">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e7ee1-192">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e7ee1-193">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="e7ee1-194">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e7ee1-195">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e7ee1-196">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-196">Service registration methods</span></span>

<span data-ttu-id="e7ee1-197">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="e7ee1-198">Méthode</span><span class="sxs-lookup"><span data-stu-id="e7ee1-198">Method</span></span> | <span data-ttu-id="e7ee1-199">Automatique</span><span class="sxs-lookup"><span data-stu-id="e7ee1-199">Automatic</span></span><br><span data-ttu-id="e7ee1-200">object</span><span class="sxs-lookup"><span data-stu-id="e7ee1-200">object</span></span><br><span data-ttu-id="e7ee1-201">suppression</span><span class="sxs-lookup"><span data-stu-id="e7ee1-201">disposal</span></span> | <span data-ttu-id="e7ee1-202">Multiple</span><span class="sxs-lookup"><span data-stu-id="e7ee1-202">Multiple</span></span><br><span data-ttu-id="e7ee1-203">implémentations</span><span class="sxs-lookup"><span data-stu-id="e7ee1-203">implementations</span></span> | <span data-ttu-id="e7ee1-204">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="e7ee1-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="e7ee1-205">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="e7ee1-206">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-206">Yes</span></span> | <span data-ttu-id="e7ee1-207">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-207">Yes</span></span> | <span data-ttu-id="e7ee1-208">No</span><span class="sxs-lookup"><span data-stu-id="e7ee1-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-209">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="e7ee1-210">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-210">Yes</span></span> | <span data-ttu-id="e7ee1-211">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-211">Yes</span></span> | <span data-ttu-id="e7ee1-212">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="e7ee1-213">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="e7ee1-214">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-214">Yes</span></span> | <span data-ttu-id="e7ee1-215">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-215">No</span></span> | <span data-ttu-id="e7ee1-216">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-217">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="e7ee1-218">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-218">No</span></span> | <span data-ttu-id="e7ee1-219">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-219">Yes</span></span> | <span data-ttu-id="e7ee1-220">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-221">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="e7ee1-222">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-222">No</span></span> | <span data-ttu-id="e7ee1-223">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-223">No</span></span> | <span data-ttu-id="e7ee1-224">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-224">Yes</span></span> |

<span data-ttu-id="e7ee1-225">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e7ee1-226">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e7ee1-227">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e7ee1-228">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="e7ee1-229">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e7ee1-230">Pour plus d'informations, voir :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="e7ee1-231">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e7ee1-232">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e7ee1-233">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e7ee1-234">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="e7ee1-235">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="e7ee1-236">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="e7ee1-237">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e7ee1-238">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="e7ee1-238">Constructor injection behavior</span></span>

<span data-ttu-id="e7ee1-239">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e7ee1-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e7ee1-241">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e7ee1-242">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e7ee1-243">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e7ee1-244">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e7ee1-245">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e7ee1-246">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e7ee1-246">Entity Framework contexts</span></span>

<span data-ttu-id="e7ee1-247">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e7ee1-248">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="e7ee1-249">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e7ee1-250">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="e7ee1-250">Lifetime and registration options</span></span>

<span data-ttu-id="e7ee1-251">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e7ee1-252">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e7ee1-253">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e7ee1-254">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="e7ee1-255">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e7ee1-256">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e7ee1-257">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e7ee1-258">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e7ee1-259">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e7ee1-260">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="e7ee1-261">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e7ee1-262">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="e7ee1-263">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="e7ee1-264">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e7ee1-265">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="e7ee1-266">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e7ee1-267">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e7ee1-268">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="e7ee1-269">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e7ee1-270">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-270">**First request:**</span></span>

<span data-ttu-id="e7ee1-271">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-271">Controller operations:</span></span>

<span data-ttu-id="e7ee1-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e7ee1-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e7ee1-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e7ee1-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e7ee1-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-276">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-276">`OperationService` operations:</span></span>

<span data-ttu-id="e7ee1-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e7ee1-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e7ee1-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e7ee1-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e7ee1-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-281">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-281">**Second request:**</span></span>

<span data-ttu-id="e7ee1-282">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-282">Controller operations:</span></span>

<span data-ttu-id="e7ee1-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e7ee1-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e7ee1-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e7ee1-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e7ee1-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-287">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-287">`OperationService` operations:</span></span>

<span data-ttu-id="e7ee1-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e7ee1-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e7ee1-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e7ee1-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e7ee1-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-292">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e7ee1-293">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-293">*Transient* objects are always different.</span></span> <span data-ttu-id="e7ee1-294">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="e7ee1-295">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="e7ee1-296">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="e7ee1-297">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e7ee1-298">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="e7ee1-298">Call services from main</span></span>

<span data-ttu-id="e7ee1-299">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e7ee1-300">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e7ee1-301">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="e7ee1-302">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="e7ee1-302">Scope validation</span></span>

<span data-ttu-id="e7ee1-303">Lorsque l’application s’exécute dans l’environnement de développement et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e7ee1-304">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e7ee1-305">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="e7ee1-306">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="e7ee1-307">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e7ee1-308">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e7ee1-309">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e7ee1-310">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e7ee1-311">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="e7ee1-312">Services de requête</span><span class="sxs-lookup"><span data-stu-id="e7ee1-312">Request Services</span></span>

<span data-ttu-id="e7ee1-313">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="e7ee1-314">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e7ee1-315">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e7ee1-316">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e7ee1-317">Au lieu de cela, demandez les types que les classes requièrent via les constructeurs de classe et autorisez l’infrastructure à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="e7ee1-318">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e7ee1-319">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e7ee1-320">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="e7ee1-320">Design services for dependency injection</span></span>

<span data-ttu-id="e7ee1-321">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-321">Best practices are to:</span></span>

* <span data-ttu-id="e7ee1-322">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e7ee1-323">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e7ee1-324">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="e7ee1-325">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e7ee1-326">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e7ee1-327">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e7ee1-328">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e7ee1-329">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e7ee1-330">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e7ee1-331">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e7ee1-332">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="e7ee1-332">Disposal of services</span></span>

<span data-ttu-id="e7ee1-333">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e7ee1-334">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="e7ee1-335">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="e7ee1-336">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-336">In the following example:</span></span>

* <span data-ttu-id="e7ee1-337">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e7ee1-338">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="e7ee1-339">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e7ee1-340">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="e7ee1-341">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="e7ee1-341">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="e7ee1-342">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="e7ee1-342">Transient, limited lifetime</span></span>

<span data-ttu-id="e7ee1-343">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-343">**Scenario**</span></span>

<span data-ttu-id="e7ee1-344">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-344">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="e7ee1-345">L’instance est résolue dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-345">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="e7ee1-346">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-346">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="e7ee1-347">**Solution**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-347">**Solution**</span></span>

<span data-ttu-id="e7ee1-348">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-348">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="e7ee1-349">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-349">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="e7ee1-350">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-350">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="e7ee1-351">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-351">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="e7ee1-352">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="e7ee1-353">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="e7ee1-353">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="e7ee1-354">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-354">**Scenario**</span></span>

<span data-ttu-id="e7ee1-355">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-355">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="e7ee1-356">**Solution**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-356">**Solution**</span></span>

<span data-ttu-id="e7ee1-357">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-357">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="e7ee1-358">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="e7ee1-359">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-359">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="e7ee1-360">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-360">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="e7ee1-361">Instructions générales</span><span class="sxs-lookup"><span data-stu-id="e7ee1-361">General Guidelines</span></span>

* <span data-ttu-id="e7ee1-362">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-362">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="e7ee1-363">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-363">Use the factory pattern instead.</span></span>
* <span data-ttu-id="e7ee1-364">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-364">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="e7ee1-365">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-365">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="e7ee1-366">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-366">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="e7ee1-367">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-367">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="e7ee1-368">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-368">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="e7ee1-369">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-369">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="e7ee1-370">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="e7ee1-370">Default service container replacement</span></span>

<span data-ttu-id="e7ee1-371">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-371">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e7ee1-372">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-372">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="e7ee1-373">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="e7ee1-373">Property injection</span></span>
* <span data-ttu-id="e7ee1-374">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="e7ee1-374">Injection based on name</span></span>
* <span data-ttu-id="e7ee1-375">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="e7ee1-375">Child containers</span></span>
* <span data-ttu-id="e7ee1-376">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="e7ee1-376">Custom lifetime management</span></span>
* <span data-ttu-id="e7ee1-377">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="e7ee1-377">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e7ee1-378">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="e7ee1-378">Convention-based registration</span></span>

<span data-ttu-id="e7ee1-379">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-379">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e7ee1-380">Autofac</span><span class="sxs-lookup"><span data-stu-id="e7ee1-380">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e7ee1-381">DryIoc</span><span class="sxs-lookup"><span data-stu-id="e7ee1-381">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e7ee1-382">Grace</span><span class="sxs-lookup"><span data-stu-id="e7ee1-382">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e7ee1-383">LightInject</span><span class="sxs-lookup"><span data-stu-id="e7ee1-383">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e7ee1-384">Lamar</span><span class="sxs-lookup"><span data-stu-id="e7ee1-384">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e7ee1-385">Stashbox</span><span class="sxs-lookup"><span data-stu-id="e7ee1-385">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e7ee1-386">Unity</span><span class="sxs-lookup"><span data-stu-id="e7ee1-386">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="e7ee1-387">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="e7ee1-387">Thread safety</span></span>

<span data-ttu-id="e7ee1-388">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-388">Create thread-safe singleton services.</span></span> <span data-ttu-id="e7ee1-389">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-389">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="e7ee1-390">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-390">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e7ee1-391">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-391">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e7ee1-392">Recommandations</span><span class="sxs-lookup"><span data-stu-id="e7ee1-392">Recommendations</span></span>

* <span data-ttu-id="e7ee1-393">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-393">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e7ee1-394">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-394">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e7ee1-395">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-395">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e7ee1-396">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-396">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e7ee1-397">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-397">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e7ee1-398">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-398">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e7ee1-399">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-399">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="e7ee1-400">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-400">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="e7ee1-401">Évitez d’utiliser le *modèle de localisation de service*.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-401">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="e7ee1-402">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-402">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="e7ee1-403">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-403">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="e7ee1-404">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="e7ee1-404">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="e7ee1-405">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-405">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="e7ee1-406">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-406">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="e7ee1-407">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-407">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="e7ee1-408">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-408">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e7ee1-409">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-409">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e7ee1-410">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-410">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e7ee1-411">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-411">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="e7ee1-412">Modèles recommandés pour l’architecture mutualisée dans DI</span><span class="sxs-lookup"><span data-stu-id="e7ee1-412">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="e7ee1-413">Le [cœur du verger](https://github.com/OrchardCMS/OrchardCore) fournit une architecture mutualisée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="e7ee1-414">Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-414">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="e7ee1-415">Consultez les exemples d’applications à l’adresse https://github.com/OrchardCMS/OrchardCore.Samples pour obtenir des exemples de création d’applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger uniquement sans les fonctionnalités spécifiques au CMS.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-415">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7ee1-416">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7ee1-416">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e7ee1-417">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee1-417">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="e7ee1-418">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="e7ee1-418">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="e7ee1-419">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="e7ee1-419">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e7ee1-420">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="e7ee1-420">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="e7ee1-421">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee1-421">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7ee1-422">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-422">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e7ee1-423">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-423">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e7ee1-424">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7ee1-424">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e7ee1-425">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="e7ee1-425">Overview of dependency injection</span></span>

<span data-ttu-id="e7ee1-426">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-426">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e7ee1-427">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-427">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="e7ee1-428">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-428">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e7ee1-429">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-429">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="e7ee1-430">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-430">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e7ee1-431">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-431">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e7ee1-432">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-432">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e7ee1-433">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-433">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e7ee1-434">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-434">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e7ee1-435">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-435">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e7ee1-436">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-436">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e7ee1-437">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-437">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e7ee1-438">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-438">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e7ee1-439">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-439">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e7ee1-440">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-440">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e7ee1-441">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-441">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e7ee1-442">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-442">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e7ee1-443">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-443">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e7ee1-444">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-444">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e7ee1-445">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-445">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e7ee1-446">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-446">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="e7ee1-447">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-447">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e7ee1-448">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-448">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e7ee1-449">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-449">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e7ee1-450">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-450">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e7ee1-451">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-451">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e7ee1-452">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-452">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e7ee1-453">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-453">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e7ee1-454">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-454">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="e7ee1-455">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-455">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e7ee1-456">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-456">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e7ee1-457">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-457">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="e7ee1-458">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-458">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e7ee1-459">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-459">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e7ee1-460">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-460">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e7ee1-461">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-461">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e7ee1-462">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-462">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="e7ee1-463">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-463">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e7ee1-464">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-464">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e7ee1-465">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-465">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="e7ee1-466">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="e7ee1-466">Services injected into Startup</span></span>

<span data-ttu-id="e7ee1-467">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-467">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e7ee1-468">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-468">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="e7ee1-469">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-469">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e7ee1-470">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="e7ee1-470">Framework-provided services</span></span>

<span data-ttu-id="e7ee1-471">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-471">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e7ee1-472">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-472">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e7ee1-473">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-473">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="e7ee1-474">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-474">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="e7ee1-475">Type de service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-475">Service Type</span></span> | <span data-ttu-id="e7ee1-476">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="e7ee1-476">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-477">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-477">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="e7ee1-478">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-478">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="e7ee1-479">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-479">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="e7ee1-480">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-480">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="e7ee1-481">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-481">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="e7ee1-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-483">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-483">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="e7ee1-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="e7ee1-486">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-486">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-487">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-487">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="e7ee1-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-488">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="e7ee1-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="e7ee1-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="e7ee1-491">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="e7ee1-491">Register additional services with extension methods</span></span>

<span data-ttu-id="e7ee1-492">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-492">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e7ee1-493">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-493">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="e7ee1-494">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-494">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e7ee1-495">Durées de service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-495">Service lifetimes</span></span>

<span data-ttu-id="e7ee1-496">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-496">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e7ee1-497">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-497">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="e7ee1-498">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e7ee1-498">Transient</span></span>

<span data-ttu-id="e7ee1-499">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-499">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="e7ee1-500">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-500">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="e7ee1-501">Délimité</span><span class="sxs-lookup"><span data-stu-id="e7ee1-501">Scoped</span></span>

<span data-ttu-id="e7ee1-502">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-502">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="e7ee1-503">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-503">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e7ee1-504">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-504">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e7ee1-505">Pour plus d’informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-505">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e7ee1-506">Singleton</span><span class="sxs-lookup"><span data-stu-id="e7ee1-506">Singleton</span></span>

<span data-ttu-id="e7ee1-507">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-507">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e7ee1-508">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-508">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e7ee1-509">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-509">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e7ee1-510">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-510">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="e7ee1-511">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-511">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e7ee1-512">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-512">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e7ee1-513">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="e7ee1-513">Service registration methods</span></span>

<span data-ttu-id="e7ee1-514">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-514">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="e7ee1-515">Méthode</span><span class="sxs-lookup"><span data-stu-id="e7ee1-515">Method</span></span> | <span data-ttu-id="e7ee1-516">Automatique</span><span class="sxs-lookup"><span data-stu-id="e7ee1-516">Automatic</span></span><br><span data-ttu-id="e7ee1-517">object</span><span class="sxs-lookup"><span data-stu-id="e7ee1-517">object</span></span><br><span data-ttu-id="e7ee1-518">suppression</span><span class="sxs-lookup"><span data-stu-id="e7ee1-518">disposal</span></span> | <span data-ttu-id="e7ee1-519">Multiple</span><span class="sxs-lookup"><span data-stu-id="e7ee1-519">Multiple</span></span><br><span data-ttu-id="e7ee1-520">implémentations</span><span class="sxs-lookup"><span data-stu-id="e7ee1-520">implementations</span></span> | <span data-ttu-id="e7ee1-521">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="e7ee1-521">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="e7ee1-522">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-522">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="e7ee1-523">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-523">Yes</span></span> | <span data-ttu-id="e7ee1-524">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-524">Yes</span></span> | <span data-ttu-id="e7ee1-525">No</span><span class="sxs-lookup"><span data-stu-id="e7ee1-525">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-526">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-526">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="e7ee1-527">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-527">Yes</span></span> | <span data-ttu-id="e7ee1-528">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-528">Yes</span></span> | <span data-ttu-id="e7ee1-529">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="e7ee1-530">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-530">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="e7ee1-531">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-531">Yes</span></span> | <span data-ttu-id="e7ee1-532">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-532">No</span></span> | <span data-ttu-id="e7ee1-533">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-533">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-534">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="e7ee1-535">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-535">No</span></span> | <span data-ttu-id="e7ee1-536">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-536">Yes</span></span> | <span data-ttu-id="e7ee1-537">Yes</span><span class="sxs-lookup"><span data-stu-id="e7ee1-537">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="e7ee1-538">Exemples :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-538">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="e7ee1-539">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-539">No</span></span> | <span data-ttu-id="e7ee1-540">Non</span><span class="sxs-lookup"><span data-stu-id="e7ee1-540">No</span></span> | <span data-ttu-id="e7ee1-541">Oui</span><span class="sxs-lookup"><span data-stu-id="e7ee1-541">Yes</span></span> |

<span data-ttu-id="e7ee1-542">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-542">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e7ee1-543">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-543">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e7ee1-544">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-544">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e7ee1-545">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-545">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="e7ee1-546">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-546">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e7ee1-547">Pour plus d'informations, voir :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-547">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="e7ee1-548">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e7ee1-549">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-549">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e7ee1-550">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-550">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e7ee1-551">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-551">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="e7ee1-552">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-552">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="e7ee1-553">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-553">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="e7ee1-554">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-554">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e7ee1-555">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="e7ee1-555">Constructor injection behavior</span></span>

<span data-ttu-id="e7ee1-556">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-556">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e7ee1-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e7ee1-558">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-558">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e7ee1-559">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-559">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e7ee1-560">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-560">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e7ee1-561">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-561">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e7ee1-562">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-562">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e7ee1-563">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e7ee1-563">Entity Framework contexts</span></span>

<span data-ttu-id="e7ee1-564">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-564">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e7ee1-565">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-565">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="e7ee1-566">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-566">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e7ee1-567">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="e7ee1-567">Lifetime and registration options</span></span>

<span data-ttu-id="e7ee1-568">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-568">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e7ee1-569">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-569">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e7ee1-570">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-570">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e7ee1-571">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-571">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="e7ee1-572">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-572">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e7ee1-573">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-573">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e7ee1-574">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-574">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e7ee1-575">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-575">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e7ee1-576">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-576">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e7ee1-577">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-577">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="e7ee1-578">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-578">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e7ee1-579">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-579">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="e7ee1-580">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-580">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="e7ee1-581">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-581">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e7ee1-582">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-582">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="e7ee1-583">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-583">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e7ee1-584">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-584">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e7ee1-585">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-585">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="e7ee1-586">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-586">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e7ee1-587">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-587">**First request:**</span></span>

<span data-ttu-id="e7ee1-588">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-588">Controller operations:</span></span>

<span data-ttu-id="e7ee1-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e7ee1-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e7ee1-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e7ee1-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e7ee1-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-592">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-592">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-593">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-593">`OperationService` operations:</span></span>

<span data-ttu-id="e7ee1-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e7ee1-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e7ee1-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e7ee1-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e7ee1-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-597">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-598">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-598">**Second request:**</span></span>

<span data-ttu-id="e7ee1-599">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-599">Controller operations:</span></span>

<span data-ttu-id="e7ee1-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e7ee1-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e7ee1-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e7ee1-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e7ee1-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-603">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-604">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-604">`OperationService` operations:</span></span>

<span data-ttu-id="e7ee1-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e7ee1-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e7ee1-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e7ee1-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e7ee1-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e7ee1-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e7ee1-608">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e7ee1-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e7ee1-609">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-609">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e7ee1-610">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-610">*Transient* objects are always different.</span></span> <span data-ttu-id="e7ee1-611">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-611">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="e7ee1-612">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-612">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="e7ee1-613">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-613">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="e7ee1-614">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-614">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e7ee1-615">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="e7ee1-615">Call services from main</span></span>

<span data-ttu-id="e7ee1-616">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-616">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e7ee1-617">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-617">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e7ee1-618">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-618">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="e7ee1-619">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="e7ee1-619">Scope validation</span></span>

<span data-ttu-id="e7ee1-620">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-620">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e7ee1-621">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-621">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e7ee1-622">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-622">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="e7ee1-623">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-623">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="e7ee1-624">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-624">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e7ee1-625">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-625">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e7ee1-626">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-626">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e7ee1-627">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-627">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e7ee1-628">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-628">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="e7ee1-629">Services de requête</span><span class="sxs-lookup"><span data-stu-id="e7ee1-629">Request Services</span></span>

<span data-ttu-id="e7ee1-630">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-630">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="e7ee1-631">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-631">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e7ee1-632">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-632">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e7ee1-633">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-633">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e7ee1-634">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-634">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="e7ee1-635">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-635">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e7ee1-636">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-636">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e7ee1-637">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="e7ee1-637">Design services for dependency injection</span></span>

<span data-ttu-id="e7ee1-638">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-638">Best practices are to:</span></span>

* <span data-ttu-id="e7ee1-639">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-639">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e7ee1-640">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-640">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e7ee1-641">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-641">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="e7ee1-642">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-642">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e7ee1-643">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-643">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e7ee1-644">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-644">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e7ee1-645">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-645">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e7ee1-646">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-646">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e7ee1-647">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-647">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e7ee1-648">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-648">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e7ee1-649">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="e7ee1-649">Disposal of services</span></span>

<span data-ttu-id="e7ee1-650">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-650">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e7ee1-651">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-651">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="e7ee1-652">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-652">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="e7ee1-653">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-653">In the following example:</span></span>

* <span data-ttu-id="e7ee1-654">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-654">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e7ee1-655">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-655">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="e7ee1-656">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-656">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e7ee1-657">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-657">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="e7ee1-658">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="e7ee1-658">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="e7ee1-659">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="e7ee1-659">Transient, limited lifetime</span></span>

<span data-ttu-id="e7ee1-660">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-660">**Scenario**</span></span>

<span data-ttu-id="e7ee1-661">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-661">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="e7ee1-662">L’instance est résolue dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-662">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="e7ee1-663">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-663">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="e7ee1-664">**Solution**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-664">**Solution**</span></span>

<span data-ttu-id="e7ee1-665">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-665">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="e7ee1-666">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-666">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="e7ee1-667">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-667">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="e7ee1-668">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-668">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="e7ee1-669">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="e7ee1-670">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="e7ee1-670">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="e7ee1-671">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-671">**Scenario**</span></span>

<span data-ttu-id="e7ee1-672">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-672">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="e7ee1-673">**Solution**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-673">**Solution**</span></span>

<span data-ttu-id="e7ee1-674">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-674">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="e7ee1-675">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="e7ee1-676">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-676">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="e7ee1-677">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-677">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="e7ee1-678">Instructions générales</span><span class="sxs-lookup"><span data-stu-id="e7ee1-678">General Guidelines</span></span>

* <span data-ttu-id="e7ee1-679">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-679">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="e7ee1-680">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-680">Use the factory pattern instead.</span></span>
* <span data-ttu-id="e7ee1-681">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-681">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="e7ee1-682">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-682">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="e7ee1-683">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-683">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="e7ee1-684">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-684">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="e7ee1-685">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-685">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="e7ee1-686">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-686">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="e7ee1-687">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="e7ee1-687">Default service container replacement</span></span>

<span data-ttu-id="e7ee1-688">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-688">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e7ee1-689">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-689">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="e7ee1-690">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="e7ee1-690">Property injection</span></span>
* <span data-ttu-id="e7ee1-691">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="e7ee1-691">Injection based on name</span></span>
* <span data-ttu-id="e7ee1-692">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="e7ee1-692">Child containers</span></span>
* <span data-ttu-id="e7ee1-693">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="e7ee1-693">Custom lifetime management</span></span>
* <span data-ttu-id="e7ee1-694">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="e7ee1-694">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e7ee1-695">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="e7ee1-695">Convention-based registration</span></span>

<span data-ttu-id="e7ee1-696">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-696">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e7ee1-697">Autofac</span><span class="sxs-lookup"><span data-stu-id="e7ee1-697">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e7ee1-698">DryIoc</span><span class="sxs-lookup"><span data-stu-id="e7ee1-698">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e7ee1-699">Grace</span><span class="sxs-lookup"><span data-stu-id="e7ee1-699">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e7ee1-700">LightInject</span><span class="sxs-lookup"><span data-stu-id="e7ee1-700">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e7ee1-701">Lamar</span><span class="sxs-lookup"><span data-stu-id="e7ee1-701">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e7ee1-702">Stashbox</span><span class="sxs-lookup"><span data-stu-id="e7ee1-702">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e7ee1-703">Unity</span><span class="sxs-lookup"><span data-stu-id="e7ee1-703">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="e7ee1-704">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="e7ee1-704">Thread safety</span></span>

<span data-ttu-id="e7ee1-705">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-705">Create thread-safe singleton services.</span></span> <span data-ttu-id="e7ee1-706">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-706">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="e7ee1-707">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-707">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e7ee1-708">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-708">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e7ee1-709">Recommandations</span><span class="sxs-lookup"><span data-stu-id="e7ee1-709">Recommendations</span></span>

* <span data-ttu-id="e7ee1-710">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-710">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e7ee1-711">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-711">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e7ee1-712">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-712">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e7ee1-713">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-713">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e7ee1-714">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-714">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e7ee1-715">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-715">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e7ee1-716">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-716">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="e7ee1-717">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-717">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="e7ee1-718">Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-718">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="e7ee1-719">N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="e7ee1-719">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="e7ee1-720">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="e7ee1-720">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="e7ee1-721">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="e7ee1-721">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="e7ee1-722">Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="e7ee1-722">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="e7ee1-723">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="e7ee1-723">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="e7ee1-724">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-724">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e7ee1-725">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-725">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e7ee1-726">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-726">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e7ee1-727">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="e7ee1-727">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7ee1-728">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7ee1-728">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e7ee1-729">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee1-729">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="e7ee1-730">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="e7ee1-730">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="e7ee1-731">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="e7ee1-731">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e7ee1-732">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="e7ee1-732">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="e7ee1-733">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ee1-733">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
